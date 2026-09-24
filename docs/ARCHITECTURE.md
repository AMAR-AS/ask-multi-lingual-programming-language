# Ask Architecture

This document records the architectural direction for Ask / AS Language. It is intentionally separate from implementation details so that compiler rewrites do not change the project's core design.

## 1. Language identity

- Name: **Ask / AS Language**
- Source extension: **.as**
- Author: **Amarjith Singh Kalidasan**
- Design priorities: multilingual source syntax, Unicode, a canonical semantic core, cross-platform execution, native backends, and a path to self-hosting.

Ask should remain one language even when the source can be written using different human-language surface forms.

## 2. Frontend contract

The frontend converts human-readable source into a language-neutral representation.

~~~text
Unicode source
    ↓
Language-pack recognition
    ↓
Canonical tokens
    ↓
Canonical AST
~~~

Language packs must be data-driven. The parser must consume canonical tokens rather than contain separate grammar implementations for every human language.

### Required properties

- Unicode-aware identifiers and strings.
- Explicit normalization rules.
- Deterministic keyword recognition.
- Clear handling of mixed-language source.
- Source locations preserved for diagnostics.
- Canonical tokens independent of the source language.

## 3. Canonical semantics

Every supported surface language must converge on the same semantic model.

~~~text
English program ─┐
Tamil program   ─┤
Telugu program  ─┤
Hindi program   ─┤
...             ─┤
                 ▼
          Canonical AST / IR
~~~

A multilingual implementation is incomplete if two equivalent surface programs take different semantic paths merely because they use different human languages.

## 4. Compiler pipeline

~~~text
Source
  ↓
Unicode + multilingual lexer
  ↓
Parser
  ↓
Canonical AST
  ↓
Name resolution
  ↓
Type / semantic checking
  ↓
Ask IR
  ↓
Optimization
  ├──────────────┐
  ▼              ▼
Bytecode       Native IR
  ↓              ↓
Ask VM       Native backends
  └──────┬───────┘
         ▼
   Ask Runtime
         ↓
Platform services
~~~

Each stage should have a narrow contract and independently testable inputs/outputs.

## 5. Execution targets

### Bytecode / VM

The VM is the portable semantic reference target. It should provide:

- deterministic instruction semantics;
- useful diagnostics;
- debugging hooks;
- a stable runtime ABI;
- portability across host operating systems.

### Native

Native compilation should lower from the same Ask IR used by the portable path.

The native pipeline may evolve through multiple technologies, including a platform-independent native IR and platform-specific code generation, without changing Ask source semantics.

## 6. Runtime layers

The runtime should be divided into explicit layers.

~~~text
Ask Runtime Core
├── values
├── memory
├── strings
├── collections
└── errors

Standard Library
├── math
├── I/O
├── filesystem
├── networking
├── concurrency
└── serialization

Platform Abstraction
├── windows
├── input
├── graphics
├── audio
├── storage
├── networking
└── device services

Native Backends
├── Android
├── iOS
├── Windows
├── Linux
├── macOS
└── Web / WebAssembly
~~~

A capability is considered **supported** only when it has an implementation, tests, and documented behavior. A declaration or placeholder is not sufficient.

## 7. Cross-platform application model

Ask's multi-native goal is similar in spirit to Flutter's single-codebase model:

~~~text
                 Ask application
                       │
                common Ask APIs
                       │
            platform abstraction
             ┌─────────┼─────────┐
             ▼         ▼         ▼
          Android      iOS     Desktop/Web
             │         │         │
             └──── native APIs ──┘
~~~

The application should depend on stable Ask APIs. Platform-specific implementations belong in backend layers.

## 8. Self-hosting path

Self-hosting is a progression, not a requirement that every early compiler component immediately be rewritten.

~~~text
External host implementation
        ↓
Stable Ask specification
        ↓
Ask compiler components
        ↓
Ask-built compiler
        ↓
Self-hosted Ask toolchain
~~~

The self-hosted implementation must be validated against the same canonical tests as the host implementation.

## 9. Language packs

Language packs should describe mappings such as:

~~~text
surface keyword / spelling
        ↓
canonical token
        ↓
canonical AST operation
~~~

A language pack should not redefine semantics.

The initial multilingual direction explicitly includes English, Tamil, Telugu, Hindi, Kannada, Malayalam, Mandarin Chinese, and Japanese, with an extensible model for additional languages.

## 10. Reserved words

The project-defined reserved words currently include:

~~~text
amar
as
ask
asika
~~~

Their exact grammar roles belong in the canonical language specification. Until those roles are finalized, compiler components should reserve them consistently rather than silently treating them as ordinary identifiers.

## 11. Testing model

Testing should be layered:

1. Unicode normalization and identifier tests.
2. Lexer/token tests.
3. Parser/AST tests.
4. Semantic/type tests.
5. IR generation tests.
6. Optimizer equivalence tests.
7. Bytecode/VM tests.
8. Runtime tests.
9. Native backend tests.
10. Cross-platform integration tests.
11. Cross-language equivalence tests.

The most important multilingual invariant is:

**Equivalent source written using different language packs must converge to equivalent canonical AST/IR.**

## 12. Repository evolution

The repository contains historical prototypes from multiple implementation stages. When replacing a prototype:

- preserve useful tests and examples;
- document incompatible syntax changes;
- avoid creating a second competing grammar;
- keep the canonical specification authoritative;
- distinguish implemented behavior from planned APIs;
- prefer small, testable interfaces over broad placeholder APIs.

The purpose of the architecture is to let Ask grow without repeatedly restarting the project from scratch.
