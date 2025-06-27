# Intermediate-code-generator
# TAC Generator & Optimizer (C++)

## Overview

This project is a **mini‑compiler backend sandbox** written in modern C++.
Given one or more arithmetic expressions (and very simple `for`‑loops) on **standard input**, it walks through the classic pipeline:

1. **Infix → Postfix conversion** via the Shunting‑Yard algorithm.
2. **Three‑Address Code (TAC) generation** using temporary variables (`t1`, `t2`, …).
3. **Constant folding** (local algebraic simplification).
4. **Dead‑code elimination** (removes unused assignments).
5. **Expression‑tree construction** and pretty ASCII display.
6. **Naïve loop unrolling** for one‑line `for(init; cond; inc)` loops.

The program outputs every intermediate representation, so it is ideal for learning, demos, or quick prototyping of additional optimisation passes.

---

## Features

| Stage                 | Function(s)                     | What it does                                         |
| --------------------- | ------------------------------- | ---------------------------------------------------- |
| Infix → Postfix       | `convertToPostfix`              | Uses operator precedence & parenthesis handling      |
| TAC generation        | `createTAC` + `generateTempVar` | Emits 3‑address instructions and temp variables      |
| Constant folding      | `applyConstantFolding`          | Reduces constant expressions & neutral‐element rules |
| Dead‑code elimination | `eliminateDeadCode`             | Prunes assignments never used later                  |
| Expression tree       | `constructTree`, `displayTree`  | Builds a binary tree and prints it sideways          |
| Loop support          | `handleSimpleLoop`              | Expands `for(i=…; i<…; i++)` with inline comments    |

---

## Quick Start

### 1 · Prerequisites

* A C++11 (or newer) compiler. Tested on **GCC 13**, **Clang 17**, and **MSVC 19.38**.
* CMake is **optional**; a single g++ command is enough.

### 2 · Build

```bash
# Linux / macOS / MinGW
g++ -std=c++17 -Wall -O2 tac.cpp -o tac

# MSVC (Developer Prompt)
cl /std:c++17 /O2 tac.cpp /Fe:tac.exe
```

### 3 · Run

```bash
./tac
Enter expressions or loops separated by ':'
a = b + c * d : for(i=0;i<3;i++) : result = (x + 2) * (y - 1)
```

#### Sample Session

```text
=== Processing Expression 1 ===
Three Address Code (TAC):
t0 = c * d
t1 = b + t0
a = t1

Optimized TAC (After Constant Folding):
t0 = c * d
t1 = b + t0
a = t1

TAC After Dead Code Elimination:
t0 = c * d
t1 = b + t0
a = t1

Formatted Expression Tree:
        +
    b
        *
            d
        c
...
```

---

## Input Grammar & Limitations

* **Identifiers** must currently be *single letters* (`a`, `b`, `x`, …).
* **Constants** are *single digits* (`0`–`9`).
* Supported binary operators: `+  -  *  /` (integer semantics).
* Parentheses for explicit grouping are allowed.
* Loop support is limited to the pattern `for(var=start; var<limit; var++)` with `<` as the only comparison.

Feel free to extend the tokenizer and parser if you need multi‑character identifiers, floating‑point numbers, different loop forms, or additional optimisations.

---

## Internals at a Glance

```
convertToPostfix  ─▶  createTAC  ─▶  applyConstantFolding  ─▶  eliminateDeadCode
                          │                                        │
                          └──────────── constructTree ◀─────────────┘
```

* **`generateTempVar`** – Issues fresh temporary variable names and records them in a symbol table.
* **`processExpression`** – Orchestrates the full pipeline for every colon‑separated expression.
* **`splitExpressions`** – Splits raw user input on `:` so multiple expressions can be processed in one run.

---

## Extending the Project

* **Multi‑character tokens** – Replace the `isalnum`/`isdigit` spots with a proper lexer.
* **More optimisations** – Common sub‑expression elimination, strength reduction, copy propagation, etc.
* **Code generation** – Emit assembly or byte‑code instead of TAC, or feed into LLVM IR.
* **Unit tests** – Catch regressions; GoogleTest/ Catch2 harnesses are welcome.

---

## License

Released under the **MIT License** – see `LICENSE` for details. Feel free to use, modify, and distribute.
