# Style: Turn format and prose rules

## Turn limits

- At most 3 sentences of prose per Turn. The Moves list is not prose.
- At most 2 code blocks per Turn, at most 10 lines each.
- A Goal State over 10 lines is Trimmed: keep the goal and the hypotheses the Step touched, drop coercions such as `↑` and `.toNat`, and show one side of an `↔` when the other side did not change.
- A Type View over 10 lines is Trimmed: keep the `⊢` line and the names the Step binds or uses.
- Every Trimmed Fact says so.

## Goal State block

```lean
-- after `rw [split_run]` · Verified · see Foo.lean:42
h : carryIn = true
⊢ RunEndsWithCarry carryIn (column :: columnsLE) carryOut
```

The first line carries the Step, the tag (`Verified` or `Reconstructed`), and the editor line. Add `· Trimmed` to the tag when the state is Trimmed.

## Type View block

The same first line, then the names in scope with their types, then a `⊢` line with the expected type of the Step's right side. For a `do` statement, the `⊢` line is the type of the block.

```lean
-- after `let mut acc := 0` · Verified · see Foo.lean:12
rows : List Row
acc : Nat := 0
⊢ StateM Cache Nat
```

For a field, a constructor, or an instance method, the Type View is the one signature that Lean gives:

```lean
-- field `z` · Verified · see Point.lean:7
Point3.z {α : Type} (self : Point3 α) : α
```

## Run Turn

Code block 1 is the `#eval` line. Code block 2 is Lean's output with the tag. One sentence says which path of the function the input takes.

```lean
#eval insertSorted 2 [1, 3]
```

```lean
-- Run · Verified
[1, 2, 3]
```

## Moves list

Every Turn ends with a Moves list: the moves the user can make now, one per bullet, at most 4 bullets.

- The first bullet is "next" and names the Turn it leads to, such as the Plan or the Step's first tactic. The last Step has no "next" bullet.
- Each other bullet is a move from section 7 of `SKILL.md` that applies at this Turn. It names its object: the Helper to enter, the syntax to explain, the block or term to break down, the input of a Run, the thing a Figure shows.

```markdown
- "next": Step 2, `cases w <;> cases k <;> cases c`
- "enter `interlock_step`": the Walk of this Helper
- "what does `simp_all` do": a Syntax note
- "figure": the case tree of the three `cases`
```

```markdown
- "next": Step 3, the arm `| x :: xs`
- "break that down": the `decreasing_by` block
- "run it": `insertSorted 2 [1, 3]`
- "what does `termination_by` do": a Syntax note
```

## Prose: ASD-STE100 rules

Write every sentence under these rules. Lean identifiers, tactic names, keywords, and mathematical terms are technical names and are exempt from the vocabulary rules.

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

An Analogy is one sentence that maps a Lean idea onto a Rust construct. If the match stops at a point that matters for the Step, the sentence says where. If no Rust construct fits, the Turn has no Analogy. Examples:

- `obtain ⟨x, y, z⟩ := column` destructures a tuple like `let (x, y, z) = column` in Rust.
- An `inductive` type with no constructors is like an empty Rust `enum`.
- `example : foo = bar := by decide` is a unit test that the compiler runs.
- A `structure` is like a Rust `struct`, but a field can hold a proof about the other fields.
- A `class` is like a Rust `trait`, and an `instance` is like `impl Trait for Type`.
- `let x ← e` is like `let x = e?` in Rust, but it works in every monad, not only for errors.
- `let mut acc := 0` is like `let mut acc = 0` in Rust, but Lean rewrites the block into pure code.
- `deriving Repr` is like `#[derive(Debug)]` in Rust.
