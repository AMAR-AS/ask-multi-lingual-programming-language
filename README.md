# AskLang

**AskLang** is a prototype for a multi-lingual programming language — a language designed to eventually (1) support keywords/syntax in different human languages, and (2) interoperate cleanly with other programming languages and native code from the same script.

> ⚠️ **Status: Early prototype / scaffold.** The full compiler pipeline is stubbed out end-to-end, but most stages are placeholders rather than working logic. This is a skeleton to build on, not a working language yet — see [Current State](#-current-state-read-this-first) below for specifics.

---

## 📖 Table of Contents

- [What This Is](#what-this-is)
- [Architecture](#-architecture)
- [Project Structure](#-project-structure)
- [Current State (read this first)](#-current-state-read-this-first)
- [Getting Started](#-getting-started)
- [Example](#-example)
- [Roadmap](#-roadmap)
- [Contributing](#-contributing)
- [License](#-license)

---

## What This Is

AskLang has two "multi-lingual" goals in mind:

1. **Human multi-lingual syntax** — the aspiration to let people write AskLang code using keywords in their own language (English, Hindi, etc.), rather than being locked into one. This is not yet implemented in the code — it's a design goal for later.
2. **Cross-language interoperability** — the ability to call out to native code and other runtimes from AskLang scripts. This shows up today as `__native_*` calls (e.g. `__native_sin`, `__native_print`) that the runtime is meant to bind to real implementations.

The project is being built as a classic **compiler + virtual machine**, and — unusually for a prototype this early — it's being built **twice in parallel**:

- A **C++ host implementation** (the "real" compiled binary), and
- A **self-hosted implementation written in AskLang itself** (the seed of eventually letting AskLang compile itself).

---

## 🏗 Architecture

The pipeline implemented in `src/main.cpp` is:

```
Source (.as file)
      │
      ▼
   Lexer          → tokenizes source text
      │
      ▼
   Parser         → builds an AST from tokens
      │
      ▼
Semantic Analyzer  → walks the AST, checks types/symbols
      │
      ▼
  IR Generator     → emits a simple three-address IR
      │
      ▼
   Optimizer       → e.g. constant folding on the IR
      │
      ▼
 Code Generator    → emits bytecode
      │
      ▼
      VM           → loads and runs the bytecode
```

A `Sandbox` module also exists (in both C++ and AskLang) with logic to catch things like divide-by-zero and malformed instructions — but it is **not currently wired into `main.cpp`**.

### Two parallel implementations

| Layer | Where | Language | Purpose |
|---|---|---|---|
| Host compiler | `src/*.cpp`, `include/*.h` | C++17 | The actual compiled binary (`ask`), built via CMake |
| Self-hosted compiler | `src/*.as` | AskLang | A lexer/parser/AST/semantic analyzer/IR/optimizer/codegen written *in* AskLang, mirroring the C++ side — the seed of self-hosting |
| Standard library | `runtime/*.as` | AskLang | Native-bound library modules (math, I/O, strings, web, AI) |

---

## 📁 Project Structure

```
ask-multi-lingual-programming-language/
├── examples/              # Sample AskLang scripts
│   ├── hello_world.as     # Working — one line
│   ├── fib.as             # Empty placeholder
│   └── web_server.as      # Empty placeholder
├── include/                # C++ headers for the host compiler
│   ├── ast.h
│   ├── lexer.h
│   ├── parser.h
│   ├── semantic.h
│   ├── ir.h
│   ├── optimizer.h
│   ├── codegen.h
│   ├── vm.h
│   └── sandbox.h
├── runtime/                 # AskLang standard library modules
│   ├── math.as              # Implemented (trig, log, clamp, etc.)
│   ├── io.as                 # Empty placeholder
│   ├── string.as              # Empty placeholder
│   ├── web.as                  # Empty placeholder
│   └── ai.as                    # Empty placeholder
├── src/                      # Source — both C++ and self-hosted AskLang
│   ├── *.cpp                  # C++ host implementation (mostly stubs)
│   └── *.as                    # Self-hosted implementation (further along, inconsistent syntax)
├── tests/                     # All currently empty placeholders
│   ├── lexer_test.as
│   ├── parser_test.as
│   └── semantic_test.as
├── CMakeLists.txt              # Globs src/*.cpp into the `ask` executable
├── AskLang-Setup.exe            # Windows installer
├── ask.zip / ask 1.0.zip          # Packaged releases
├── LICENSE-MIT
└── LICENSE-APACHE-2.0
```

---

## 🔍 Current State (read this first)

This section exists so nobody — including future-you — mistakes the scaffold for a working language. As of this writing:

- **Lexer (C++)** — a stub. It treats the *entire source file* as a single `IDENTIFIER` token; there's no real tokenization yet.
- **Parser (C++)** — turns each token into a flat child node of a `"Program"` root. No real grammar/recursive descent yet.
- **Semantic Analyzer (C++)** — walks the tree and has symbol-table scaffolding (`declareVariable`, `checkAssignment`, etc.) but the checks are mostly unused/no-ops in practice.
- **IR Generator (C++)** — only handles `Identifier` nodes, emitting `LOAD` instructions.
- **Optimizer (C++)** — does real constant folding (`ADD`/`SUB`/`MUL`/`DIV` on literal operands), but there's a **signature mismatch**: `optimizer.h` declares `optimize()` returning `void`, while `optimizer.cpp` implements it returning `std::vector<IRInstruction>`. This needs to be reconciled before the project reliably builds.
- **Code Generator (C++)** — a stub. It emits a single `HALT` byte and nothing else.
- **VM (C++)** — a stub. `run()` just prints how many instructions it "would" run and returns.
- **Sandbox** — implemented (division-by-zero and malformed-instruction checks) in both C++ and AskLang, but not called anywhere in `main.cpp` yet.
- **Orphaned code fragments** — `parser.cpp`, `codegen.cpp`, `vm.cpp`, and `ast.h` each have extra code snippets sitting *outside* any function body (e.g. a `PrintStmt` visitor fragment, an `OP_PRINT` case block). These read like design notes or half-merged pieces rather than compiled code, and should be cleaned up or properly integrated.
- **Self-hosted AskLang files (`src/*.as`)** — further along logically (the `.as` lexer has a real character-by-character loop; the `.as` parser has real recursive-descent structure), but **syntax is inconsistent across files**: some use `def ... ret ... end`, others `func ... var ... ->`, others `fcn` with type annotations (`txt`, `num`, `bool`) and `own`/`self`. The language's own grammar hasn't been settled yet.
- **Examples & tests** — `hello_world.as` has one working line (`print("Hello, AskLang!");`); `fib.as`, `web_server.as`, and all three test files are currently empty (0 bytes).
- **Standard library** — `math.as` is implemented as thin wrappers over `__native_*` calls; `io.as`, `string.as`, `web.as`, `ai.as` are empty placeholders.

None of this is a criticism — this is exactly what a prototype scaffold looks like before the real work of implementing each stage begins. It's listed here so contributors (including future you) know exactly what's real vs. placeholder.

---

## 🚀 Getting Started

### Option 1: Windows Installer

Download and run [`AskLang-Setup.exe`](./AskLang-Setup.exe), then follow the setup wizard.

### Option 2: Build from Source

**Prerequisites**
- CMake 3.10+
- A C++17-compatible compiler

**Build steps**

```bash
git clone https://github.com/AMAR-AS/ask-multi-lingual-programming-language.git
cd ask-multi-lingual-programming-language
mkdir build && cd build
cmake ..
cmake --build .
```

> **Note:** given the `optimizer.h` / `optimizer.cpp` signature mismatch described above, the build may currently fail until that's fixed.

### Option 3: Prebuilt Archive

Extract `ask.zip` or `ask 1.0.zip` and run the included binary directly.

---

## 🧪 Example

The only currently-populated example, `examples/hello_world.as`:

```
print("Hello, AskLang!");
```

Run it with:

```bash
./ask examples/hello_world.as
```

*(Given the current stub state of the lexer/parser/codegen/VM described above, this won't yet produce real output — it's here as the target script to get working end-to-end first.)*

---

## 🗺️ Roadmap

Suggested near-term priorities, based on the current state of the code:

- [ ] Fix the `optimizer.h` / `optimizer.cpp` signature mismatch so the project builds cleanly
- [ ] Clean up or properly integrate the orphaned code fragments in `parser.cpp`, `codegen.cpp`, `vm.cpp`, and `ast.h`
- [ ] Implement a real lexer (character-level tokenization, not "whole file = one token")
- [ ] Implement a real recursive-descent (or Pratt) parser matching an actual grammar
- [ ] Wire `Sandbox` into the `main.cpp` pipeline
- [ ] Settle on one consistent syntax for the self-hosted `.as` files (`def`/`ret`/`end` vs `func`/`var`/`->` vs `fcn` with type hints)
- [ ] Fill in `runtime/io.as`, `string.as`, `web.as`, `ai.as`
- [ ] Get `hello_world.as` running end-to-end through the real pipeline
- [ ] Fill in `examples/fib.as`, `examples/web_server.as`, and the test files in `tests/`
- [ ] Begin implementing human multi-lingual keyword support (the core "multi-lingual" pitch — not yet started)
- [ ] Cross-platform installers (currently Windows-only via `AskLang-Setup.exe`)

---

## 🤝 Contributing

This is an early-stage prototype — feedback, issues, and pull requests are welcome. If you're picking up a piece of the roadmap above, it's worth mentioning which pipeline stage you're working on to avoid overlap with the parallel C++/`.as` implementations.

---

## 📄 License

AskLang is dual-licensed under either of:

- [MIT License](./LICENSE-MIT)
- [Apache License, Version 2.0](./LICENSE-APACHE-2.0)

at your option.

---

## 👤 Author

Created and maintained by [AMAR-AS](https://github.com/AMAR-AS).
