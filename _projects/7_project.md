---
layout: page
title: C Compiler From Scratch
description: Built a mini-compiler for a subset of a C-like language, covering the core phases of compiler construction- lexical analysis, parsing, symbol table and scope management, and code generation. It includes a Flex-based lexer, a Yacc/Bison grammar that handles declarations, expressions, and control flow (if, else, while), a scope-aware symbol table for tracking function and variable declarations, and a basic assembly-style code generator, demonstrating a complete end-to-end compiler workflow from source code to generated instructions.
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

A complete academic compiler lab implementation covering the core phases of compiler construction for a subset of a C-like language, from tokenizing through to code generation.

## Key Work

- **Lexical Analysis** (`1905099.l`) — a Flex-based lexer that tokenizes the source language.
- **Parsing & Code Generation** (`1905099.y`) — a Yacc/Bison grammar that drives parsing and emits basic assembly-style code.
- **Symbol Table & Scope Management** (`1905099_classes.h`) — tracks function and variable declarations across nested scopes.
- **Language Features** — expressions and assignments, control flow (`if`, `else`, `while`), return statements, and print support.

> A working end-to-end compiler flow for a C-like language subset — parsing, semantic checks, and assembly-style code generation in a course-appropriate structure.
