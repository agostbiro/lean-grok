# Style: Turn format and prose rules

## Turn limits

- At most 3 sentences of prose per Turn.
- At most 2 code blocks per Turn, at most 10 lines each.
- A Goal State over 10 lines is Trimmed: keep the goal and the hypotheses the Step touched, drop coercions such as `↑` and `.toNat`, and show one side of an `↔` when the other side did not change. Every Trimmed Goal State says so.

## Goal State block

```lean
-- after `rw [split_run]` · Verified · see Foo.lean:42
h : carryIn = true
⊢ RunEndsWithCarry carryIn (column :: columnsLE) carryOut
```

The first line carries the Step, the tag (`Verified` or `Reconstructed`), and the editor line. Add `· Trimmed` to the tag when the state is Trimmed.

## Prose: ASD-STE100 rules

Write every sentence under these rules. Lean identifiers, tactic names, and mathematical terms are technical names and are exempt from the vocabulary rules.

- At most 20 words in a sentence that tells the user to do something. At most 25 words in any other sentence.
- One idea per sentence. One instruction per sentence.
- Active voice. Name the actor: "The `rw` step replaces the left side", never "the left side is replaced".
- Simple present, simple past, simple future, infinitive, and imperative only. Write "Lean closed the goal", never "Lean has closed the goal" or "Lean is closing the goal".
- A verb for an action, not a noun: "Lean rewrites the goal", never "Lean does a rewrite of the goal".
- An "-ing" word only inside a technical name such as "rewriting engine". Never as a verb.
- Verbs without trailing particles: "remove", never "take out".
- Noun clusters of at most 3 words. Write "the carry of the first column", never "the first column carry bit value".
- Keep articles and the subject: "the goal is now", never "goal now:".
- Put the condition first: "If the hypothesis is false, `omega` closes the goal."
- Commas, periods, colons, and parentheses are fine. No semicolons.
- Third person. Write "the proof", "the Step", "Lean", "the user". Never "we", "let's", or "you can trust".
- Plain statements. No humor, no rhetorical questions.

## Replacements for common non-approved words

| Write | Instead of |
|---|---|
| make sure | verify, check, confirm, ensure |
| use | utilize, employ, leverage |
| show | demonstrate, illustrate |
| start | begin, commence, initiate |
| give | provide, supply |
| apply | perform (an application of) |
| do | perform, carry out, execute |
| is the same as | is equivalent to |
| because | since, as (causal) |
| get | obtain, acquire |
| need | require |
| in | within |
| about | approximately |
| at this point | now (as a filler) |
| after that | subsequently, then |
| one | single |

## Analogy

An Analogy is one sentence that maps a Lean idea onto a Rust construct. Examples:

- `obtain ⟨x, y, z⟩ := column` destructures a tuple like `let (x, y, z) = column` in Rust.
- An `inductive` type with no constructors is like an empty Rust `enum`.
- `example : foo = bar := by decide` is a unit test that the compiler runs.
