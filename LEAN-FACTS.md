# Getting facts from Lean

Try each source in order. Stop at the first that works. Tag every Fact with the source's tag.

## 1. Language server (tag: Verified)

If an LSP tool is available in the session and a Lean server is configured for the project:

- Hover on a name gives its signature.
- A goal request at the position just after a tactic gives the Goal State. The Lean server answers `$/lean/plainGoal` at a `{textDocument, position}`.
- A term goal request at the start of a term gives the Type View. The Lean server answers `$/lean/plainTermGoal` at a `{textDocument, position}` with the names in scope and the expected type.

If the LSP tool only offers standard requests, hover still gives signatures. Use source 2 for Goal States and Type Views.

## 2. Scratch compile (tag: Verified)

Requires a `lakefile.lean` or `lakefile.toml` at the project root and a built project (`.lake/build` exists). Never edit the user's source file.

1. Copy the Subject's file to the scratchpad directory, for example `<scratchpad>/walk-me-through-lean/Subject.lean`.
2. In the copy, add one probe per Step from the table below.
3. For each Library item the Subject uses, append `#check <name>` at the end of the copy. Without `@`, the output keeps the argument names.
4. Run from the project root:

   ```sh
   cd <project-root> && lake env lean --json <scratchpad>/walk-me-through-lean/Subject.lean
   ```

5. Parse the output. Each line is one JSON message with `pos.line`, `pos.column`, and `data`. Match each message to its probe by position. A `trace_state` message holds the names in scope and the `⊢` line. A `#check` message holds one signature.

### Probes

| Step | Probe | Output |
|---|---|---|
| Tactic | A line `trace_state` after the tactic, at the same indentation. After a whole combinator chain. Inside a `cases ... with` arm, after the arm's last tactic. | Hypotheses and `⊢` goal |
| `do` statement | A statement `let _ := (by trace_state; exact () : Unit)` after it, at the same indentation | Names bound so far with types. `let mut` names also show their value. |
| `do` block, once | A statement `(by trace_state; exact pure ())` | `⊢` the type of the block, such as `IO Unit` |
| Equation, `match` arm, one-expression body, proof term | Wrap the right side: `(by trace_state; exact <rhs>)` | Pattern variables with types and `⊢` the expected type |
| Field of a `structure` or `class` | `#check <Type>.<field>` after the declaration | Signature of the projection |
| Constructor | `#check <Type>.<ctor>` | Signature with argument names |
| Whole `structure` or `class` | `#print <Type>` | Parents, all fields with defaults, constructor |
| Instance method | `#check @<Class>.<method> <Type> _` after the instance | The method's type with the class parameters filled in |
| Instance method body | Wrap the body as for an equation | Arguments with types and `⊢` the result type |
| `deriving` clause | `#synth <Class> <Type>` for each derived class | The instance Lean made |

### When a probe fails

- **Type mismatch at a wrapped right side.** The wrap breaks a right side that needs a coercion, such as a `Nat` where Lean expects an `Int`. Replace the right side with `_` instead. Lean reports "don't know how to synthesize placeholder" with the same names and `⊢` line. The error stops the checks of later code that uses the declaration, so use one `_` per compile.
- **The copy depends on its own module name.** Add the original file's directory to the import path, or move the copy next to the original under a name such as `_lean_grok_scratch.lean`, then delete it when done.

### Clean the output for a Type View

- A name with a dagger, such as `acc✝` or `__s✝`, is an old version of a `let mut` name or an internal name. Keep only the name with no dagger.
- A name with a dagger in a pattern, such as `tail✝`, stands for a `_` in the source. Show it as `_`.
- The recursive function itself is not in the list. Add nothing for it.

## 3. Run (tag: Verified)

A Run uses the same scratch copy. Append `#eval <Subject> <input>` at the end and compile as in source 2.

Offer "run it" only when all of these hold:

- The Subject is a function, and its type is outside `IO` and outside every monad built on `IO`. Lean runs `IO` effects at compile time, on the user's machine.
- The result type has a `Repr` or `ToString` instance.
- The Subject is computable, and neither it nor its Helpers contain `sorry`.

Pick one small input that reaches the main path of the function. If the user gives an input, use that one. If Lean refuses, the Run Turn gives Lean's reason in one sentence. Never use `#eval!`.

## 4. Reading alone (tag: Reconstructed)

Use this when the Subject is a pasted snippet with no project, or when sources 1 and 2 both failed. Write the Goal State or Type View you expect after each Step from the known behaviour of the syntax. Write signatures from memory of Lean core and Mathlib. Every such Fact carries the `Reconstructed` tag in its code block header, and the first Reconstructed Turn says once why Lean could not be asked. A Run needs Lean, so the "run it" move is absent.
