# walk-me-through-lean

A Claude skill that explains existing Lean 4 code in a guided chat walk, one step per turn.

Give it a theorem, a function, a `do` block, a structure, an inductive type, a class, or an instance. It shows the statement, checks which background ideas you want explained, gives a short plan for longer code, and then steps through the code. After each step it shows what Lean says: the goal state after a tactic, or the type Lean expects after any other step. You move on with "next".

It does not write, fix, or search for code. It does not explain macros, syntax extensions, or whole files.

The prose follows Simplified Technical English (STE), as set out in the ASD-STE100 standard of the ASD (AeroSpace and Defence Industries Association of Europe): short sentences, active voice, one idea per sentence. Analogies point to Rust where one fits.

## Requirements

- Claude Code.
- For `Verified` facts: a Lean 4 project with a `lakefile.lean` or `lakefile.toml`, already built with `lake build`.
- Optional: a Lean language server tool in the session.

Without a built project, every fact is `Reconstructed` and "run it" is not offered.

## Install

For all projects:

```sh
git clone https://github.com/agostbiro/walk-me-through-lean.git ~/.claude/skills/walk-me-through-lean
```

For one project, run this from the project root:

```sh
git clone https://github.com/agostbiro/walk-me-through-lean.git .claude/skills/walk-me-through-lean
```

## Use

Type `/walk-me-through-lean`, or ask Claude to walk you through a declaration in your project:

```
walk me through `insertSorted_sorted`
walk me through Foo.lean lines 40-80, with helpers
```

You can also paste a snippet. Every turn ends with a list of moves you can make, such as:

- "next": go to the next step
- "break that down": split the current step into smaller steps
- "enter `<helper>`": walk a helper from your project, then come back
- "skip": leave the helper and go back
- "what does `<syntax>` do": a one-sentence note on a tactic or keyword
- "run it" or "run it on `<input>`": `#eval` the function on one input
- "figure": an ASCII diagram of a structure in the code

A free question does not move you forward in the walk. Name a new declaration to start a new walk.

## Facts from Lean

The skill gets goal states and types from Lean itself when it can. It tries these sources in order:

1. The Lean language server, through an LSP (Language Server Protocol) tool if the session has one.
2. A scratch compile: it copies your file to a scratch directory, adds probes such as `trace_state`, and runs `lake env lean`. It never edits your source files. In rare cases it writes a temporary file next to yours and deletes it after.
3. Reading alone, for a pasted snippet or when Lean is not available.

Facts from sources 1 and 2 are tagged `Verified`. Facts from source 3 are tagged `Reconstructed`.

"Run it" is offered only when Lean is available and the function is outside `IO`, can print its result, and has no `sorry`. Lean runs `#eval` at compile time, so `IO` effects would run on your machine.

## Lean style for agents

To make your project's code easy to walk, copy the rules in [`LEAN-STYLE-AGENTS.md`](LEAN-STYLE-AGENTS.md) into your project's `AGENTS.md`.

## Files

- [`SKILL.md`](SKILL.md): the skill's main instructions
- [`LEAN-STYLE-AGENTS.md`](LEAN-STYLE-AGENTS.md): Lean style rules to copy into your project's `AGENTS.md`
- [`STYLE.md`](STYLE.md): turn format and prose rules
- [`LEAN-FACTS.md`](LEAN-FACTS.md): how to get facts out of Lean
- [`CONTEXT.md`](CONTEXT.md): the skill's vocabulary

## License

[MIT](LICENSE)
