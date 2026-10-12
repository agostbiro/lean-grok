---
name: walk-me-through-lean
description: Explain an existing Lean 4 proof in a guided chat walk, one tactic per turn, in Simplified Technical English. Use when the user wants to understand, read, grok, or be walked through a theorem, lemma, or proof. Not for writing, fixing, or searching for proofs.
---

# walk-me-through-lean

Explain one Lean proof (the **Subject**) as a guided **Walk**: a sequence of short **Turns** the user steps through with "next". Vocabulary is in `CONTEXT.md` next to this file. Prose rules and the Turn format are in [`STYLE.md`](STYLE.md). Commands for getting facts out of Lean are in [`LEAN-FACTS.md`](LEAN-FACTS.md).

## 1. Locate the Subject

The user gives a theorem name, a file plus a name or line range, or a pasted snippet. Settle which:

- A name or file: search the current project for the declaration and read its full source, including its statement, docstring, and the Helpers it calls from the same project. Done when you hold the exact source span of the Subject.
- A pasted snippet with no project on disk: the Subject is the snippet. Every Goal State and lemma statement will be **Reconstructed**.

Default **Scope** is the Subject alone. "With helpers" in the request puts the named Helpers, or all Helpers, in Scope.

## 2. Collect facts from Lean

Follow the chain in `LEAN-FACTS.md`: language server first, then a scratch compile with `trace_state` and `#check`, then reading alone. Done when every Step has a Goal State and every Library lemma used has a signature, each tagged **Verified** or **Reconstructed**. Tag honestly. A Reconstructed fact is never shown as Verified.

## 3. Open the Walk

Every Turn of the Walk ends with a **Moves list** in the format of `STYLE.md`, then waits for the user's move. Send these Turns in order, one per message:

1. **Statement**: the Subject's signature with the body as `...`, and one sentence on what it claims in plain words.
2. **Concept check**: list the candidate Concepts, the ideas the user needs to restate the claim in plain words. Ask which to explain. Then one Turn per chosen Concept, with a Rust Analogy where one fits.
3. **Plan**: the numbered high-level moves of the proof, at most 5 items.

## 4. Walk the Steps

Set the **Cursor** to the first Step. A Step is one tactic, one combinator chain such as `cases x <;> simp`, or one proof term. For each Step send one Turn:

- Code block 1: the Step's source line or lines.
- Prose: why this Step is taken here and which Plan item it realises. Name each Library lemma it uses, with its one-line statement. Say what a tactic does only when the user asks.
- Code block 2: the Goal State after the Step, Trimmed to fit, with its tag and the editor line where the full state is visible.

Advance the Cursor on "next". Close the last Step with "This concludes the proof."

## 5. Offer a Figure

When a Turn describes a structure that a picture shows better than prose, such as a state machine, a run split into parts, a case tree, or how a specification meets an implementation, add a "figure" bullet that names the thing to the Turn's Moves list. On "figure", send the **Figure** as its own Turn: an ASCII diagram in one code block of at most 10 lines, with one caption sentence. If the user asks for a rendered figure, publish it as an SVG artifact instead.

## 6. Answer the user's moves

Any message that is not "next" is handled in one Turn and leaves the Cursor where it is, except the moves below:

- **A question**: answer it in at most 3 sentences.
- **"What does `<tactic>` do"**: a Tactic note of one sentence plus a Rust Analogy.
- **"Walk `<helper>`" or "enter"** at a Step that uses a Helper: push the Cursor, run steps 3 and 4 on the Helper, and on its end or on "skip" pop the Cursor and re-send the parent Step's Turn.
- **"Break that down"** at a proof-term Step: a Breakdown. Each function application in the term becomes a Step, with its expected type as the Goal State.
- **"Figure"**: send the Figure.
- **A new theorem name**: drop the Cursor stack and start again from step 1.
