# Getting facts from Lean

Try each source in order. Stop at the first that works. Tag every fact with the source's tag.

## 1. Language server (tag: Verified)

If an LSP tool is available in the session and a Lean server is configured for the project:

- Hover on a lemma name gives its signature.
- A goal request at the position just after a tactic gives the Goal State. The Lean server answers `$/lean/plainGoal` at a `{textDocument, position}`. If the LSP tool only offers standard requests, hover still gives lemma signatures. Use source 2 for Goal States.

## 2. Scratch compile (tag: Verified)

Requires a `lakefile.lean` or `lakefile.toml` at the project root and a built project (`.lake/build` exists). Never edit the user's source file.

1. Copy the Subject's file to the scratchpad directory, for example `<scratchpad>/lean-grok/Subject.lean`.
2. In the copy, after every tactic line of the Subject's proof, insert a line `trace_state` at the same indentation. For a combinator chain such as `cases x <;> simp`, insert `trace_state` after the whole chain. Inside a `cases ... with | a => tac | b => tac` arm, insert it after the arm's last tactic.
3. For each Library lemma the proof uses, append `#check @<name>` at the end of the copy.
4. Run from the project root:

   ```sh
   cd <project-root> && lake env lean <scratchpad>/lean-grok/Subject.lean
   ```

5. Parse the output. Each `trace_state` prints an `info: ...:L:C:` block with the hypotheses and the `⊢` goal at that point. Match blocks to Steps in order. Each `#check` prints the signature on one `info:` line.

If the copy does not compile because the copied file depends on its own module name, add the original file's directory to the import path or move the copy next to the original under a name such as `_lean_grok_scratch.lean`, then delete it when done.

## 3. Reading alone (tag: Reconstructed)

Use this when the Subject is a pasted snippet with no project, or when sources 1 and 2 both failed. Write the Goal State you expect after each Step from the tactic's known behaviour. Write lemma statements from memory of Mathlib. Every such fact carries the `Reconstructed` tag in its code block header, and the first Reconstructed Turn says once why Lean could not be asked.
