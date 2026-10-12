# walk-me-through-lean

A Claude skill that explains a Lean 4 proof to its reader in a guided chat dialogue. It follows the structure of "Anatomy of a Lean Proof" and writes in ASD-STE100 (Simplified Technical English).

## Language

**Subject**:
The theorem or lemma the reader asked the skill to explain. Named by theorem name, by file and line range, or given as a pasted snippet.
_Avoid_: Target, input, proof under study

**Scope**:
The set of proofs the Walk covers in one call. The default Scope is the Subject alone. The reader can widen it to include Helpers.
_Avoid_: Depth, range

**Helper**:
A lemma defined in the reader's own project that the Subject's proof uses. Outside the Scope, a Helper gets a one-line statement only.
_Avoid_: Dependency, auxiliary lemma

**Library lemma**:
A lemma from Mathlib or another dependency that the Subject's proof uses. It is never dissected. It gets its full name and a one-line statement of what it says.
_Avoid_: External lemma, import

**Concept**:
A mathematical or domain idea the reader needs to restate the Subject in plain words. The skill lists candidate Concepts, and the reader picks which ones get explained.
_Avoid_: Background, prerequisite, theory

**Walk**:
The guided, ordered sequence of Turns that explains the Subject. It follows the post's order: statement, restatement, Concepts, Plan, then one Step per Turn.
_Avoid_: Tour, walkthrough, explanation

**Turn**:
One message from the skill in the Walk. At most 3 sentences of prose, plus at most 2 code blocks of at most 10 lines each.
_Avoid_: Reply, response, chunk

**Moves list**:
The list at the end of every Turn of the moves the reader can make now. Its first item names the Turn that "next" leads to.
_Avoid_: Prompt, menu, options

**Step**:
One tactic, one tactic combinator chain, or one proof term in the Subject's proof. Each Step gets its own Turn with the Goal State after it.
_Avoid_: Line, tactic (when meaning the unit of the Walk)

**Breakdown**:
A Walk into the inside of a proof term, requested by the reader. Each function application in the term becomes a Step, with its expected type as the Goal State.
_Avoid_: Expansion, zoom, drill-down

**Cursor**:
The Step the Walk is currently at. "Next" advances the Cursor. A free question from the reader does not move it. When the reader enters a Helper, the Cursor is pushed on a stack and restored on return.
_Avoid_: Position, pointer, progress

**Enter**:
The reader's move into a Helper at the Step that uses it. The Helper's Walk runs nested, and "skip" or its end returns to the same Step.
_Avoid_: Descend, drill into, open

**Plan**:
The numbered list of high-level moves the proof makes, given before the Steps. Each Step says which Plan item it realises.
_Avoid_: Strategy, outline, roadmap

**Goal State**:
The Lean goal and hypotheses after a Step. It is one of Verified, Reconstructed, and it may be Trimmed.
_Avoid_: Tactic state, infoview, context

**Verified**:
A Goal State or lemma statement obtained from Lean itself, by the language server or by a compile with trace or check output.
_Avoid_: Real, actual, true

**Reconstructed**:
A Goal State or lemma statement the skill wrote from reading or memory alone because Lean could not be asked. Always labelled as such.
_Avoid_: Guessed, estimated, inferred

**Trimmed**:
A Goal State shown with coercions removed, with only the relevant side displayed, or cut to fit the code block limit. The Turn says when a Goal State is Trimmed and names the editor line where the full state is visible.
_Avoid_: Simplified, cleaned, abridged

**Figure**:
A diagram of a structure in the Subject, offered in the Moves list when a picture shows it better than prose, and drawn only when the reader asks for it.
_Avoid_: Diagram, image, illustration

**Tactic note**:
A one-sentence description of what a tactic does, given only when the reader asks. By default a Turn says why a Step is taken, never what its tactic does.
_Avoid_: Tactic explanation, tutorial

**Analogy**:
A one-sentence comparison of a Lean idea to a Rust construct, used inside a Tactic note or a Concept explanation.
_Avoid_: Metaphor, comparison
