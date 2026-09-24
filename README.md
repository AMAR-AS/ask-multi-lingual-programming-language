# Ask — Multilingual, Unicode-First Programming Language

<p align="center">
  <strong>One language. Many human languages. Many native platforms.</strong><br/>
  A Unicode-first programming language designed for multilingual source code, a common semantic core, and cross-platform native applications.
</p>

<p align="center">
  <a href="https://github.com/AMAR-AS/ask-multi-lingual-programming-language">Repository</a>
  ·
  <a href="https://github.com/AMAR-AS/ask-multi-lingual-programming-language/issues">Issues</a>
  ·
  <a href="https://github.com/AMAR-AS/ask-multi-lingual-programming-language/discussions">Discussions</a>
</p>

> **Status:** early-stage language and compiler development. The repository contains an architectural foundation and prototypes; the complete compiler, runtime, and multilingual surface syntax are still under active development.

## What is Ask?

**Ask** (also referred to as **AS Language**) is a general-purpose programming language built around a simple idea:

> **The programming language should adapt to the programmer's human language without fragmenting the language itself.**

Ask uses **.as** source files and is designed for:

- **Multilingual programming** — language constructs can be expressed through supported human-language surface syntaxes.
- **Unicode-first source code** — Unicode identifiers and text are first-class concerns.
- **One canonical language core** — different human-language spellings resolve to the same tokens, AST, semantics, and IR.
- **Mixed-language source** — supported language forms can coexist while preserving one program meaning.
- **Cross-platform development** — one Ask codebase is intended to target multiple native platforms through a shared platform abstraction layer.
- **Bytecode and native execution** — a portable VM is a primary execution target, with native backends planned from the same semantic/IR foundation.
- **Self-hosting** — the compiler/toolchain has a long-term path toward being implemented in Ask itself.
- **Native interoperability** — Ask is intended to interoperate with platform libraries through explicit runtime/FFI boundaries.

Ask is **not** a collection of separate language forks. The goal is one language with multiple surface forms.

## Core vision

~~~text
        Human-language source
        ┌─────────────────────┐
        │ English / தமிழ் /   │
        │ తెలుగు / हिन्दी /   │
        │ ಕನ್ನಡ / മലയാളം /    │
        │ 中文 / 日本語 / ... │
        └──────────┬──────────┘
                   │
                   ▼
          Multilingual Frontend
                   │
                   ▼
          Canonical Ask Tokens
                   │
                   ▼
             Canonical AST
                   │
                   ▼
          Semantic / Type System
                   │
                   ▼
                 Ask IR
              ┌────┴────┐
              ▼         ▼
          Bytecode    Native IR
              │         │
              ▼      Native Backends
              │     ┌────┼─────────────┐
              │     ▼    ▼     ▼       ▼
              │   Android iOS Desktop  Web/WASM
              ▼
              Ask VM
                │
                ▼
          Ask Standard Runtime
                │
                ▼
       Platform Abstraction Layer
~~~

The architectural rule is important:

**Human-language syntax belongs at the frontend. Program meaning must remain language-neutral.**

## Multilingual design

Ask separates **Unicode identifiers** from **language keywords**.

### Unicode identifiers

Identifiers are designed to support Unicode so programmers can naturally name functions, variables, types, and values in their preferred writing system.

### Language packs

Human-language keywords are represented as data-driven language packs rather than hard-coded parser branches.

The initial project direction explicitly includes:

- English
- Tamil
- Telugu
- Hindi
- Kannada
- Malayalam
- Mandarin Chinese
- Japanese

The architecture is intentionally extensible so additional languages can be added without rewriting the compiler's semantic core.

Different surface spellings of an operation should canonicalize to the same internal operation:

~~~text
English form ──┐
Tamil form   ──┤
Telugu form  ──┤
Hindi form   ──┤──► canonical PRINT operation
...           ──┘
~~~

A defining correctness test for Ask is **cross-language equivalence**: semantically equivalent programs written with different language packs should produce the same canonical AST/IR.

## .as source files

The official Ask source-file extension is:

**.as**

Example:

~~~text
examples/hello_world.as
~~~

Ask reserves these project-defined words:

~~~text
amar
as
ask
asika
~~~

Their final language-level semantics are governed by the canonical Ask language specification; they should not be treated as ordinary user identifiers.

## Cross-platform / multi-native goal

Ask is designed with a **Flutter-like development model** in mind: write an application once against common Ask APIs and target multiple platforms through native backends.

~~~text
             Ask Application
                    │
             Ask Standard APIs
                    │
        Platform Abstraction Layer
          ┌─────────┼─────────┐
          ▼         ▼         ▼
       Android     iOS     Desktop/Web
          │         │         │
          └──── Native Backends ────┘
~~~

The long-term target set includes:

- Android
- iOS
- Windows
- Linux
- macOS
- Web / WebAssembly
- embedded and additional native targets where practical

The compiler should not contain large collections of platform-specific application logic. Platform capabilities should be exposed through stable runtime and platform-abstraction contracts implemented by each backend.

## Compiler architecture

Ask follows a conventional compiler pipeline while keeping multilingual syntax isolated from language semantics:

1. **Source loading** — read .as source as Unicode text.
2. **Normalization / Unicode handling** — establish canonical text handling rules.
3. **Multilingual lexing** — recognize language-pack keywords, identifiers, literals, operators, and symbols.
4. **Parsing** — build one canonical AST.
5. **Semantic analysis** — resolve names, scopes, types, control flow, modules, and language rules.
6. **Ask IR** — lower program meaning into a backend-independent intermediate representation.
7. **Optimization** — perform semantics-preserving transformations.
8. **Execution lowering** — produce portable bytecode and/or native backend representations.
9. **VM / native runtime** — execute programs against the Ask runtime.
10. **Platform services** — provide filesystem, networking, graphics, input, concurrency, storage, and device APIs through platform backends.

## Repository layout

The current repository is an evolving implementation scaffold:

~~~text
ask-multi-lingual-programming-language/
├── include/                 # C++ compiler headers
├── src/                     # C++ host implementation + Ask prototypes
├── runtime/                 # Ask runtime / standard-library prototypes
├── examples/                # Example .as programs
├── tests/                   # Language and compiler tests
├── docs/                    # Architecture and project documentation
├── CMakeLists.txt           # C++ build configuration
├── LICENSE-MIT
└── LICENSE-APACHE-2.0
~~~

Some components are currently prototypes or placeholders. The directory structure will evolve as the canonical specification and implementation converge.

## Current implementation status

The project is **not yet a production-ready programming language**.

The repository currently contains:

- a C++17 compiler/VM scaffold;
- experimental lexer, parser, AST, semantic, IR, optimizer, code-generation, VM, and sandbox components;
- an experimental Ask-language implementation intended as a seed for self-hosting;
- runtime API prototypes for I/O, strings, mathematics, web, AI, and native integration;
- example and test scaffolding.

The most important engineering task is to converge these pieces around **one canonical language specification** instead of continuing to grow parallel, incompatible syntax variants.

Historical prototypes contain differences such as **def / func / fcn** and **ret / return**. These are implementation-history artifacts until resolved by the canonical language specification.

## Development priorities

### 1. Canonical language specification

Define, in one place:

- lexical rules;
- Unicode and normalization rules;
- reserved words;
- literals and operators;
- grammar;
- declarations and scopes;
- functions and modules;
- types and conversions;
- collections and objects;
- errors and control flow;
- imports/packages;
- runtime semantics;
- multilingual keyword mappings.

### 2. Multilingual frontend

Build language packs that map supported human-language forms to canonical Ask tokens.

The parser, semantic analyzer, optimizer, and VM should not need separate implementations for each human language.

### 3. End-to-end execution

Make a small, coherent Ask program travel through:

~~~text
.as source
  → lexer
  → parser
  → semantic analysis
  → IR
  → bytecode
  → VM
  → observable result
~~~

before expanding the language surface.

### 4. Runtime and standard library

Build a stable runtime for:

- values and memory;
- Unicode strings;
- collections;
- I/O;
- math;
- filesystem;
- networking;
- concurrency;
- serialization;
- processes and system services;
- native/FFI integration.

### 5. Native platform backends

Define platform-neutral APIs first, then implement native backends for the target platforms.

### 6. Tooling

Develop the surrounding developer experience:

- ask CLI;
- formatter;
- linter;
- REPL;
- debugger;
- package/module tooling;
- language-server support;
- documentation tooling;
- test runner.

### 7. Self-hosting

Once the semantics and compiler pipeline are stable, progressively move compiler components into Ask itself and establish a reproducible self-hosted toolchain.

## Testing philosophy

Testing is a first-class part of the language design.

~~~text
Unicode
  ↓
Lexer
  ↓
Parser
  ↓
AST equivalence
  ↓
Semantic analysis
  ↓
IR equivalence
  ↓
Optimizer
  ↓
Bytecode
  ↓
VM / Native backend
  ↓
Runtime behavior
~~~

Multilingual equivalence is especially important:

> If two source programs express the same Ask program using different supported human-language forms, their canonical AST/IR should be equivalent.

This prevents multilingual support from becoming a collection of subtly different languages.

## Project principles

1. **One language, many surface languages.**
2. **Unicode is foundational.**
3. **Semantics are canonical and language-neutral.**
4. **Multilingual support must be data-driven.**
5. **Platform support belongs behind stable abstractions.**
6. **Portable execution and native execution share the same language semantics.**
7. **Runtime capabilities must be implemented and tested before being called supported.**
8. **The compiler should have a clear path to self-hosting.**
9. **Documentation should distinguish implemented features from planned features.**
10. **Compatibility and correctness matter more than adding isolated features quickly.**

## Contributing

Ask is an ambitious, long-term language project. Contributions should strengthen the canonical architecture rather than introduce another parallel syntax or runtime model.

Before implementing a major feature:

1. identify the language/specification rule it requires;
2. define the canonical representation;
3. update the relevant tests;
4. implement the compiler/runtime behavior;
5. add or update documentation;
6. verify that multilingual and cross-platform abstractions remain intact.

For architectural work, open an issue or discussion first so the project can preserve a coherent language design.

## Licensing

Ask is available under the terms of either:

- [MIT License](./LICENSE-MIT)
- [Apache License 2.0](./LICENSE-APACHE-2.0)

at your option.

## Author

**Amarjith Singh Kalidasan**

Project: [AMAR-AS/ask-multi-lingual-programming-language](https://github.com/AMAR-AS/ask-multi-lingual-programming-language)

---

**Ask / AS Language**  
*Write naturally. Compile consistently. Run across platforms.*
