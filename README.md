# Ask — Multilingual, Unicode-First Programming Language

<p align="center">
  <strong>Ask is named after its founder, Amarjith Singh Kalidasan.</strong><br/>
  One language. Many human languages. Many native platforms.
</p>

<p align="center">
  A Unicode-first programming language designed for multilingual source code, a common semantic core, and cross-platform native applications.
</p>

---

## About Ask

**Ask**, also known as **AS Language**, is a general-purpose programming language founded and created by **Amarjith Singh Kalidasan**.

The name **Ask** is directly associated with its founder:

> **Ask = the programming language founded by Amarjith Singh Kalidasan.**

Ask uses **.as** source files and is designed for multilingual programming, Unicode-first source code, one canonical language core, mixed-language source, cross-platform development, bytecode and native execution, self-hosting, and native interoperability.

Ask is **one language**, not a collection of separate language forks.

## Founder

### Amarjith Singh Kalidasan

**Amarjith Singh Kalidasan** is the founder and creator of the **Ask / AS Language** project.

```text
Founder / Creator : Amarjith Singh Kalidasan
Language          : Ask / AS Language
Source extension  : .as
Repository        : AMAR-AS/ask-multi-lingual-programming-language
```

The founder's name is part of the project's official identity and documentation.

## Core vision

```text
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
```

**Human-language syntax belongs at the frontend. Program meaning remains language-neutral.**

## Multilingual design

Ask separates **Unicode identifiers** from **language keywords**. Human-language keywords are represented as data-driven language packs rather than hard-coded parser branches.

The initial project direction explicitly includes:

- English
- Tamil
- Telugu
- Hindi
- Kannada
- Malayalam
- Mandarin Chinese
- Japanese

The architecture is extensible so additional languages can be added without rewriting the compiler's semantic core.

Equivalent surface spellings should canonicalize to the same internal Ask operation. A defining correctness property is **cross-language equivalence**: semantically equivalent programs written with different language packs should produce the same canonical AST/IR.

## .as source files

The official Ask source-file extension is **.as**.

Ask reserves these project-defined words:

```text
amar
as
ask
asika
```

Their final language-level semantics are governed by the canonical Ask language specification; they should not be treated as ordinary user identifiers.

## Cross-platform / multi-native goal

Ask is designed with a **Flutter-like development model** in mind: write an application once against common Ask APIs and target multiple platforms through native backends.

```text
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
```

The long-term target set includes Android, iOS, Windows, Linux, macOS, Web/WebAssembly, and additional native or embedded targets where practical.

## Compiler architecture

Ask follows a conventional compiler pipeline while keeping multilingual syntax isolated from language semantics:

1. Source loading
2. Unicode normalization and handling
3. Multilingual lexing
4. Canonical parsing
5. Semantic and type analysis
6. Ask IR generation
7. Optimization
8. Bytecode/native lowering
9. VM/native execution
10. Platform services

## Current status

**Ask is an early-stage language and compiler project.**

The repository contains an architectural foundation and experimental implementations across lexer, parser, AST, semantic analysis, IR, optimization, code generation, VM, runtime, examples, and tests.

Historical prototypes contain syntax differences such as **def / func / fcn** and **ret / return**. These are implementation-history artifacts until resolved by the canonical language specification.

## Development priorities

1. **Canonical language specification**
2. **Multilingual frontend and language packs**
3. **End-to-end .as execution**
4. **Stable runtime and standard library**
5. **Native platform backends**
6. **CLI, formatter, linter, REPL, debugger and package tooling**
7. **Progressive self-hosting**

## Testing philosophy

Multilingual equivalence is a first-class invariant:

```text
Unicode
  ↓
Lexer
  ↓
Parser
  ↓
Canonical AST
  ↓
Semantic analysis
  ↓
Canonical IR
  ↓
Optimizer
  ↓
Bytecode / Native
  ↓
Runtime behavior
```

Equivalent programs written using different supported human-language forms should converge to equivalent canonical AST/IR.

## Project principles

1. **One language, many surface languages.**
2. **Unicode is foundational.**
3. **Semantics are canonical and language-neutral.**
4. **Multilingual support is data-driven.**
5. **Platform support belongs behind stable abstractions.**
6. **Portable and native execution share the same language semantics.**
7. **Implemented, tested capabilities are distinguished from planned APIs.**
8. **The compiler has a clear path toward self-hosting.**
9. **The founder and project identity are documented consistently.**
10. **Correctness and architectural coherence come before uncontrolled feature growth.**

## Contributing

Contributions should strengthen the canonical Ask architecture rather than introduce competing grammars or runtime models.

For major changes:

1. define the language/specification rule;
2. define the canonical representation;
3. add tests;
4. implement compiler/runtime behavior;
5. update documentation;
6. verify multilingual and cross-platform behavior.

## License

Ask is available under either:

- [MIT License](./LICENSE-MIT)
- [Apache License 2.0](./LICENSE-APACHE-2.0)

at your option.

---

<p align="center">
  <strong>Ask / AS Language</strong><br/>
  Founded and created by <strong>Amarjith Singh Kalidasan</strong><br/><br/>
  <em>Write naturally. Compile consistently. Run across platforms.</em>
</p>
