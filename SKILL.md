---
name: walk-me-through-lean
description: Explain existing Lean 4 code in a guided chat walk, one step per turn, in Simplified Technical English. Covers theorems, functions, `do` blocks, structures, inductive types, classes, and instances. Use when the user wants to be walked through, step through, or grok a Lean declaration. Not for a quick question about Lean code, and not for writing, fixing, or searching for code or proofs.
---

# walk-me-through-lean

Explain one Lean declaration (the **Subject**) as a guided **Walk**: a sequence of short **Turns** the user steps through with "next". Vocabulary is in `CONTEXT.md` next to this file. Prose rules and the Turn format are in [`STYLE.md`](STYLE.md). Commands for getting facts out of Lean are in [`LEAN-FACTS.md`](LEAN-FACTS.md).

## 1. Locate the Subject

The user gives a declaration name, a file plus a name or line range, or a pasted snippet. Settle which:

- A name or file: search the current project for the declaration and read its full source, including its signature, docstring, and the Helpers it names from the same project. Done when you hold the exact source span of the Subject.
- A pasted snippet with no project on disk: the Subject is the snippet. Every Fact will be **Reconstructed**.

Default **Scope** is the Subject alone. "With helpers" in the request puts the named Helpers, or all Helpers, in Scope.

## 2. Split the Subject into Steps

The Subject's kind sets the Step unit and the Fact that follows each Step:

| Subject or block | One Step is | Fact after the Step |
|---|---|---|
| Tactic proof (`by`) | one tactic, or one combinator chain such as `cases x <;> simp` | Goal State |
| Proof term | the whole term | Type View |
| `do` block | one statement (`let x ← ...`, `for`, `if`, `return`) | Type View |
| `def` by pattern matching | one equation or `match` arm | Type View |
| `def` with one expression body | the whole body | Type View |
| `structure`, `class` | one field, or the fields that share one line | Type View |
| `inductive` | one constructor | Type View |
| `instance` | one field or method | Type View |
| `deriving` clause | the whole clause | Type View |

A block of another kind inside a Step (a `by` argument, a `decreasing_by`, a nested `do`, a proof field) stays inside that Step. The user opens it with "break that down". If the Subject's whole body is one `by` block or one `do` block, its Steps are the Steps of that block.

Done when every line of the Subject's body belongs to exactly one Step.

## 3. Collect facts from Lean

Follow the chain in `LEAN-FACTS.md`: language server first, then a scratch compile with probes, then reading alone. Done when every Step has its Fact and every Library item used has a signature, each tagged **Verified** or **Reconstructed**. Tag honestly. A Reconstructed Fact is never shown as Verified.

## 4. Open the Walk

Every Turn of the Walk ends with a **Moves list** in the format of `STYLE.md`, then waits for the user's move. Send these Turns in order, one per message:

1. **Statement**: the Subject's signature with the body as `...`, and one sentence on what it claims, computes, or models in plain words.
2. **Concept check**: list the candidate Concepts, the ideas the user needs to restate the Subject in plain words. Ask which to explain. Then one Turn per chosen Concept, with a Rust Analogy where one fits.
3. **Plan**: at most 5 numbered items. For a proof, the high-level moves. For a function or `do` block, the stages of the computation. For a type declaration, the fields or constructors grouped by role. For an instance, the methods it defines and the methods it gets by default. A Subject of 3 Steps or fewer has no Plan Turn.

## 5. Walk the Steps

Set the **Cursor** to the first Step. For each Step send one Turn:

- Code block 1: the Step's source line or lines.
- Prose: why this Step is here and which Plan item it realises. Name each Library item it uses, with its one-line signature. Name each project instance that Lean finds by itself. Say what a tactic, keyword, or notation does only when the user asks.
- Code block 2: the Fact after the Step, Trimmed to fit, with its tag and the editor line where the full state is visible.

Advance the Cursor on "next". Close the last Step with "This concludes the Walk."

## 6. Offer a Figure

When a Turn describes a structure that a picture shows better than prose, such as a state machine, a run split into parts, a case tree, the shape of a recursive type, or how a specification meets an implementation, add a "figure" bullet that names the thing to the Turn's Moves list. On "figure", send the **Figure** as its own Turn: an ASCII diagram in one code block of at most 10 lines, with one caption sentence. If the user asks for a rendered figure, publish it as an SVG artifact instead.

## 7. Answer the user's moves

Any message that is not "next" is handled in one Turn and leaves the Cursor where it is, except the moves below:

- **A question**: answer it in at most 3 sentences.
- **"What does `<syntax>` do"**: a Syntax note of one sentence plus a Rust Analogy.
- **"Walk `<helper>`" or "enter"** at a Step that names a Helper: push the Cursor, run steps 2 to 5 on the Helper with the rules of its own kind, and on its end or on "skip" pop the Cursor and re-send the parent Step's Turn.
- **"Break that down"**: a Breakdown of the Step. A nested block splits into the Steps of its own kind. A term splits by function application, and each application becomes a Step with its expected type as the Fact.
- **"Run it"** or **"run it on `<input>`"**: a Run, sent as its own Turn in the format of `STYLE.md`. Offer this move only when the Subject is a function outside `IO` whose result Lean can print. `LEAN-FACTS.md` gives the test.
- **"Figure"**: send the Figure.
- **A new declaration name**: drop the Cursor stack and start again from step 1.
