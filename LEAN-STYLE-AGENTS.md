# Lean style for agents

Copy the section below into your project's `AGENTS.md`. Code that follows these rules is easy to walk with this skill.

---

## Lean style

A walk with the `walk-me-through-lean` skill splits a declaration into steps (section 2 of the skill's `SKILL.md`). Write code with clear steps.

### Structure

- In a tactic proof, write one tactic or one combinator chain on each line. Use short named `have` steps, not a long chain whose goal state a reader cannot follow.
- In a `def`, write one match arm or one `do` statement on each line. Use named helpers, not large anonymous functions.
- Split a long proof into named lemmas.
- A premise is a hypothesis in parentheses in the signature. When a theorem has more than one premise, or its result is a conjunction, write each premise and each conjunct on its own line. Put a `--` comment above each line. The comment says what the line states.

  ```lean
  theorem sum_bounds
      -- `a` is at most `n`.
      (ha : a ≤ n)
      -- `b` is at most `n`.
      (hb : b ≤ n) :
      -- The sum is at most `2 * n`.
      a + b ≤ 2 * n ∧
      -- The sum is at least `a`.
      a ≤ a + b := by
  ```

- Put a one-line comment above each case of an induction, each case of a case split, and each bullet after `constructor`, `apply`, or `refine`.

### Docs

- Give each `def`, `theorem`, `structure`, and `inductive` a docstring (`/-- ... -/`) of at most 3 sentences. The docstring says what the declaration states or does.
- Put conceptual overviews in the module doc (`/-! ... -/`) at the top of the file.

### Language

- Write comments and docstrings in Simplified Technical English (STE), as set out in the ASD-STE100 standard of the ASD (AeroSpace and Defence Industries Association of Europe): short sentences, active voice, one idea per sentence, one word for one meaning.
