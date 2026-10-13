# walk-me-through-lean

A Claude skill that explains a Lean 4 declaration to its reader in a guided chat dialogue. It follows the structure of "Anatomy of a Lean Proof" and writes in ASD-STE100 (Simplified Technical English).

## Language

**Subject**:
The declaration the reader asked the skill to explain: a theorem, a function, a `do` block, a structure, an inductive type, a class, or an instance. Named by declaration name, by file and line range, or given as a pasted snippet. Macros, syntax extensions, and whole files are not Subjects.
_Avoid_: Target, input, proof under study

**Scope**:
The set of declarations the Walk covers in one call. The default Scope is the Subject alone. The reader can widen it to include Helpers.
_Avoid_: Depth, range

**Helper**:
A declaration of any kind, defined in the reader's own project, that the Subject's source names. Outside the Scope, a Helper gets a one-line signature only. A project instance that Lean finds by itself is not a Helper. The Step that uses it names it, and the reader can then enter it by name.
_Avoid_: Dependency, auxiliary lemma

**Library item**:
A declaration from Lean core, Mathlib, or another dependency that the Subject uses. It is never dissected. It gets its full name and a one-line signature.
_Avoid_: Library lemma, external lemma, import

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
The unit of the Walk. Its size depends on the kind of code: one tactic or combinator chain, one `do` statement, one equation or `match` arm, one field, one constructor, one instance method, or one whole term. A block of another kind inside a Step stays inside it. Each Step gets its own Turn with the Fact after it.
_Avoid_: Line, tactic (when meaning the unit of the Walk)

**Breakdown**:
A Walk into the inside of a Step, requested by the reader. A nested block splits into the Steps of its own kind. A term splits by function application, with the expected type of each application as its Fact.
_Avoid_: Expansion, zoom, drill-down

**Cursor**:
The Step the Walk is currently at. "Next" advances the Cursor. A free question from the reader does not move it. When the reader enters a Helper, the Cursor is pushed on a stack and restored on return.
_Avoid_: Position, pointer, progress

**Enter**:
The reader's move into a Helper at the Step that names it. The Helper's Walk runs nested, under the rules of the Helper's own kind, and "skip" or its end returns to the same Step.
_Avoid_: Descend, drill into, open

**Plan**:
The numbered list of the Subject's high-level parts, given before the Steps: the moves of a proof, the stages of a computation, the fields or constructors of a type grouped by role, or the methods of an instance. Each Step says which Plan item it realises. A Subject of 3 Steps or fewer has no Plan.
_Avoid_: Strategy, outline, roadmap

**Fact**:
Something Lean says about the Subject: a Goal State, a Type View, a signature, or a Run. Every Fact is Verified or Reconstructed.
_Avoid_: Output, info, result

**Goal State**:
The Lean goal and hypotheses after a tactic Step. It may be Trimmed.
_Avoid_: Tactic state, infoview, context

**Type View**:
The Fact after a Step that is not a tactic: the names in scope with their types, plus the expected type of the Step's right side. For a field, a constructor, or an instance method, it is the signature Lean gives. It may be Trimmed.
_Avoid_: Context, local state, environment

**Run**:
The result of `#eval` on the Subject with one sample input, sent as its own Turn when the reader asks. The skill picks the input unless the reader gives one. A Subject in `IO` never gets a Run, because Lean executes its effects at compile time.
_Avoid_: Test, execution, example output

**Verified**:
A Fact obtained from Lean itself, by the language server or by a compile with probe, check, or eval output.
_Avoid_: Real, actual, true

**Reconstructed**:
A Fact the skill wrote from reading or memory alone because Lean could not be asked. Always labelled as such.
_Avoid_: Guessed, estimated, inferred

**Trimmed**:
A Goal State or Type View shown with coercions removed, with only the relevant names or side displayed, or cut to fit the code block limit. The Turn says when a Fact is Trimmed and names the editor line where the full state is visible.
_Avoid_: Simplified, cleaned, abridged

**Figure**:
A diagram of a structure in the Subject, offered in the Moves list when a picture shows it better than prose, and drawn only when the reader asks for it.
_Avoid_: Diagram, image, illustration

**Syntax note**:
A one-sentence description of what a tactic, keyword, or notation does, given only when the reader asks. By default a Turn says why a Step is there, never what its syntax does.
_Avoid_: Tactic note, tactic explanation, tutorial

**Analogy**:
A one-sentence comparison of a Lean idea to a Rust construct, used inside a Syntax note or a Concept explanation. It says where the match stops when that matters for the Step.
_Avoid_: Metaphor, comparison
