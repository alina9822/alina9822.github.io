---
layout: page
title: Compiler From Scratch
description: Built the core front-end of a compiler- lexical analysis, parsing, and symbol table management. It includes a Flex-based lexer, a Yacc grammar for a mini-language, and a scope-aware symbol table to handle insertion, lookup, deletion, and nested scopes. The project also prints parse details and supports basic error reporting, demonstrating how a compiler reads source code and organizes identifiers before code generation.
importance: 4
category: fun
tools:
  - Flex
  - Bison
  - YACC
  - C++
github: https://github.com/alina9822/CSE-310-Compiler
---

**Technology & Tools:** Flex, Bison, YACC, C++

A compiler front-end built from scratch, covering lexical analysis, parsing, and scope-aware symbol table management for a mini-language.

## Key Work

- **Lexer** (`1905099.l`) — tokenizes keywords, identifiers, numbers, operators, and special symbols.
- **Parser/Grammar** (`1905099.y`) — defines the language structure for declarations, statements, functions, and expressions, and drives parse-tree generation.
- **Symbol Table & Scope Management** (`1905099_classes.h`) — supports insertion, lookup, deletion, nested scopes, and printing the current or all scope tables.
- **Driver Program** (`1905099_main.cpp`) — reads commands from `1905099_input.txt` and performs symbol-table operations: Insert, Lookup, Delete, EnterScope, ExitScope, and Print.
- **Error Handling & Logging** — records parse errors and scope/table events during execution.
- **Build Setup** (`99s.sh`) — compiles the lexer and parser into a working executable.

> Covers the core compiler front-end stages:
> - Lexical analysis
> - Parsing
> - Symbol table construction
> - Scope tracking
> - Parse tree output
>
> It does not include full code generation or optimization — the focus is on the analysis and semantic management stages of a compiler.
