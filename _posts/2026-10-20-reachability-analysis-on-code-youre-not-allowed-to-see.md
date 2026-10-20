---
layout: post
title: Reachability analysis on code you're not allowed to see
date: 2026-10-20 12:00:00
description: Notes on building a two-pass reachability analysis system that answers CVE questions on proprietary code without ever seeing it.
tags: security sbom reachability
categories: writeups
---

> **Note on this write-up.** These are informal notes from a project, not documentation
> or a spec. Customer details and internal implementation details are left out: no
> internal services, hosts, images or credentials. The diagrams are my own design
> sketches. Anything concrete here is about public tooling (CycloneDX, SBOM generators,
> Docker), and the architecture is described at a high level.

## The problem

Most CVE tooling tells you a vulnerable package is *present*. That's a low bar, and it's
why security backlogs fill up with tickets nobody acts on. The useful question is whether
the vulnerable function is actually **reachable** from your code. Answering it means
building the real dependency chain, generating call graphs for every artifact in it,
finding the functions the CVE lives in, and walking the call paths. Do that and you can
emit a VEX document that says "this doesn't affect us, here's the proof", which is much
more useful than a ticket.

The system I work on did all of this happily, for *public* packages. All the data lives
upstream: registries, source repos, published artifacts. Then the obvious commercial
question shows up, *what about my code?*, and the honest answer was "make it public
first", which is not an answer.

So: how do you run this analysis on proprietary code, inside someone else's firewall, on
a machine you don't control, against source you're never allowed to see?

## The shape of it

{% include figure.liquid loading="eager" path="assets/img/writeups/reachability-analysis/chain-trust-boundary.png" class="img-fluid rounded z-depth-1" %}

`A` is the customer's application. `B` and `C` are their internal packages. `D`, `E`, `F`
are open source, and the CVE is in `F`. The question is whether `A` is affected.

You can't answer this in one pass. The private half can't leave the building, and the
public half can't be analysed locally unless you ship your whole backend on-premise,
which is not realistic for a platform of several services and a large dataset.

So you cut the chain at the trust boundary and analyse twice:

1. **Public pass, hosted.** Send only the public portion of the SBOM upstream. It answers:
   does the CVE in `F` reach `D`, the customer's direct open-source dependency, and if
   so, which functions of `D` are affected?
2. **Private pass, local.** Those affected functions become the root-cause input for a
   second analysis that runs entirely on the customer's machine, over call graphs
   generated there for `A`, `B` and `C`.

Three outcomes fall out:

| | |
|---|---|
| **Case C** | The CVE never reaches `D`. Done: nothing private was ever involved. |
| **Case B** | It reaches `D` but dies somewhere in the private chain. Also done, and now you know *which* internal package it stops at. |
| **Case A** | It reaches `A`. A real finding, with a call path to show for it. |

The nice property is that Case C, which in practice is usually the common one, costs
nothing locally: you stop before any local analysis runs.

## Components

```
┌─────────────────────── customer network ────────────────────────┐
│                                                                 │
│  CI trigger                Orchestrator (container)             │
│  ──────────────  ───────▶  · splits the SBOM public/private     │
│  on: new CVE               · talks to the hosted API            │
│      advisory              · decides case A / B / C             │
│                            · runs call graphs locally           │
│                            · files the issue                    │
│                                      │                          │
│                                      ▼                          │
│                            Call-graph engine                    │
│                            (one container per language)         │
│                                                                 │
└──────────────────────────────────────┬──────────────────────────┘
                                       │  public SBOM out
                                       │  VEX + affected functions back
                                       ▼
                            Hosted analysis API
                            (public artifacts only)
```

All the orchestration logic lives in one place. The CI side is a dumb trigger; the
call-graph engine is a pure analysis tool with no opinions about workflow. That
separation mattered more than I expected: it let the trigger be swapped per CI system
without touching anything else.

## Decision one: who makes the SBOM

Everything rests on the SBOM, so this was the first decision, and it took far longer than
the architecture did. Two options: the customer hands us one, or we generate it from
their repo. A teammate took the first case; I took the second. My half is what settled it.

**SBOM generation is really lockfile parsing.** Without a resolved, built project
(populated lockfiles, warm caches), you capture *declared* dependencies, not transitive
ones. An SBOM of declared deps is near useless for reachability, because the vulnerable
package is almost always three levels down.

**The Python-native generator was a non-starter.** It installs into the project's own
virtualenv, so the tool's dependencies end up in the customer's SBOM, which is supposed
to describe their software, not my tooling. It wants the project installed
(`pip install -e .`) before it can analyse anything. And it emits references like
`flask==2.2.5` instead of `pkg:pypi/flask@2.2.5`, which breaks every PURL-based match
downstream.

**The universal scanner fixed those and brought its own problem.** Installed globally,
outside the project; proper PURLs; one tool across Python, JS, Java, Go, Rust and .NET;
auto-detects the ecosystem if you don't tell it:

```bash
# per ecosystem
<scanner> -t python -o sbom.json
<scanner> -t go     -o sbom.json

# or let it work it out, recursively, for a polyglot monorepo
<scanner> -r -o sbom.json
```

But it's the hardest of the bunch to fully air-gap, and like several generators it will
quietly call registries for license and metadata enrichment unless you explicitly turn
that off. If the promise is "nothing leaves your network", a tool that makes silent
outbound calls by default is a problem.

**Native vs. universal is a real trade-off**, not a preference. Ecosystem-native plugins
(Maven, npm, Cargo) give the most accurate transitive trees because they ask the actual
resolver. Universal scanners are convenient for polyglot repos and less precise.

**Auth is what actually breaks.** Go generation failed outright until `GOPRIVATE` was set
and SSH/PAT auth was configured for the private module host. Java needed explicit scope
configuration in the plugin to emit the tree we wanted (compile and runtime in, test
out). In CI, the generator has to run *after* a successful install
step; run it before, and missing credentials produce a smaller SBOM rather than an error,
which is the worst kind of failure because nothing looks wrong.

**And some things are just missing.** Vendored source, git submodules, binary tarballs and
commercial SDKs are detected by nothing at all. Full coverage means merging
package-manager SBOMs with filesystem scans plus manual augmentation. Git dependencies
make it non-deterministic too: a branch reference means today's SBOM and tomorrow's
disagree. Commit SHAs beat tags beat branches, and tools disagree about what belongs in
the version field anyway.

**Conclusion:** a trustworthy SBOM can only be produced from inside the customer's
authenticated build, after a successful install. That's their CI, not our container
running on their host. So we fixed the format (CycloneDX) and took the SBOM as input,
trading control over input quality for the ability to function
at all in an authenticated or air-gapped environment.

## Decision two: which packages are private?

Taking the SBOM as input creates a new problem. The document contains everything, public
and private mixed, and only the public half can go upstream. Something has to label the
private components, and the customer is the only party that knows.

So they declare them, and the SBOM is split locally into a public-only document that goes
upstream and a private list that stays on their side. Private component names never
leave the building.

Two things make this messier than it sounds.

**PURLs don't encode the private registry.** `pkg:npm/utils@1.0.0` from an internal
registry and `pkg:npm/utils@1.0.0` from the public one are the same string. That's a
namespace collision, and read the other way, it's the dependency-confusion attack. A
`repository_url` qualifier helps when the generator supports it; otherwise you fall back
to `externalReferences` on the component and hope it's populated.

**Some customers won't tell you the names at all.** If private package names are
obfuscated, the SBOM stops being one rooted tree and becomes a *forest*: disconnected
subtrees with no way to join them. You can compute chains up to the public boundary and
no further. We left this for later; I still think it's the hardest open problem in the
design.

Minor but irritating: internal packages routinely have no license field, so they land as
"unknown." That has to be fixed in the source manifest. Patching it into the SBOM
afterwards just hides the problem.

## Decision three: running the engine on someone else's host

The call-graph engine is a container, one per language, and the orchestrator has to
launch it on the customer's machine. Two ways:

- **Docker-in-Docker:** nested daemons, storage-driver conflicts, cgroup headaches. And
  if the controller image bundles every language engine, it gets enormous.
- **Docker-out-of-Docker:** mount the host's Docker socket and launch engines as
  *sibling* containers on the host.

```
orchestrator ──HTTP──▶ worker ──/var/run/docker.sock──▶ engine (sibling on host)
                         ▲                                   │
                         └────── results on shared volume ◀───┘
```

We took DooD, and it's the trade-off I'd flag most clearly to anyone copying this.
Mounting the socket gives the container root-level access to the host. That was
acceptable for a prototype on a three-week schedule, because a prototype you can't deploy
teaches you nothing, but it's the first thing to replace: rootless Docker (the daemon runs
as a non-root user) or Podman (rootless by default, with a Docker-compatible socket) both
leave the orchestration code unchanged.

Things the worker needed that weren't obvious up front:

- **Repeated requests shouldn't repeat work.** Call-graph generation is the expensive
  step, so the same input should map to the same job, and existing results should be
  reused.
- **Timeouts enforced by killing the container.** An engine that hangs on a pathological
  input will otherwise sit there forever.
- **Cleaning up after a crash.** If the worker crashes mid-run, the containers it launched
  keep running and nothing tracks them, so on startup it has to find and remove them.

## Getting at the source

Call graphs need source, and for private packages that is a credentials problem. Either
the customer makes the code available locally, which keeps credentials out of our hands,
or they give us access to fetch it, which is more convenient but immediately raises "what
else can that access read?". Private artifact registries each want their own kind of
credential (bearer tokens, API keys, deploy keys, app passwords), so the work grows with
every ecosystem supported.

## What gets reported

Output is a GitHub issue, not a PR. We report; the customer decides. Automated
remediation across a chain of repos you don't own means forks, cross-repo credentials and
version propagation through packages you have no write access to. That is a much bigger
problem, and deliberately out of scope. The trees below describe what the customer would
have to do, not what the tool does.

The content depends on the case, and Case A alone has four shapes depending on where a
fix exists in the chain:

{% include figure.liquid loading="eager" path="assets/img/writeups/reachability-analysis/case-a-decision-tree.png" class="img-fluid rounded z-depth-1" %}

- **A-1: no fix anywhere upstream.** The vulnerability is real and reachable and nobody
  has patched it. Fork or forge a fix, propagate it down through every package in between,
  then update the app.
- **A-2: a fixed version exists in the first open-source dependency.** The good case:
  bump one version, propagate internally, done.
- **A-3: fixed upstream, but no released version of the direct dependency picks it up.**
  Either work with that maintainer, or fork it and pull more of the chain along with you.
- **A-4: same, but the vulnerable package is a *direct* dependency of the first
  open-source one.** The fork is shallow, so this is the tractable version of A-3.

Case B is the one that looks simple and isn't:

{% include figure.liquid loading="eager" path="assets/img/writeups/reachability-analysis/case-b-decision-tree.png" class="img-fluid rounded z-depth-1" %}

The vulnerability gets into the proprietary chain but doesn't reach the application, so
the fix is needed in the internal packages, not in the app. If it reaches `C`, the update
is needed in `B` and/or `C`, and that branch inherits all four A shapes (B-A-1 to B-A-4).
If it never reaches `C`, no proprietary component needs an update. That's how you end up
with a decision tree this wide from a problem that started as "is it reachable, yes or
no".

And there's a subtlety that costs you if you miss it: **Case B is only true at that
commit.** Someone adds one call next sprint and it flips to A. So a "not reachable"
verdict has to be re-checked when the code it depended on changes, without re-running
everything every day.

## What I'd tell someone building this

**Cut at the trust boundary and accept two passes.** More moving parts and more state,
but trying to unify the analysis means either shipping your whole platform on-prem or
asking customers to upload code they legally can't.

**Your input-acquisition story will cost more than your analysis.** I spent more time on
SBOM fidelity and credentials than on anything resembling program analysis, and that
ratio wasn't a sign I was doing it wrong.

**Write the security cost of your shortcuts down, out loud, at the time you take them.**
"Mount the Docker socket" is a reasonable prototype decision and an unreasonable product
decision, and the only way that distinction survives contact with a deadline is if it's
written somewhere other than your head.
