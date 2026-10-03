# Provenance and Design Decisions

## Status

- **Three-Phase Git-Committed Gate Protocol:** `passed`. Upgraded transition gate to three explicit phases:
  1. `Phase 1: Lock` (closing gate with an atomic Git commit before substantive code);
  2. `Phase 2: Prepare` (7-point context refresh audit, `fiss-lint --strict` verification, outcome staging, and gate authorization policy evaluation);
  3. `Phase 3: Release` (opening gate with an atomic Git commit upon authorization).
- **Gate Authorization Policy Integration:** `passed`. Added native support for project-specific gate release policies via `FISS/overrides/` (such as `FISS/overrides/handoff.md`), enabling projects to require human confirmation (e.g. via Human Review Surface) while preserving autonomous operation when no policy override exists.
- **Skill Scope Clarification & Extraction of `fiss-init`:** `passed`. Removed 0-to-1 initialization logic from `fiss-maintain` and extracted it into dedicated `fiss-init` skill. `fiss-maintain` now focuses strictly on Day 2 operational continuity and context preservation.
- **`fiss-lint` CLI Delegation:** `passed`. Deterministic verification is delegated directly to `fiss-lint --strict`.
- **Principle 6 Context Refresh:** `passed`. Mandates auditing 7 context dimensions before task synchronization.

---

## Provenance Entries

### 1. Three-Phase Git-Committed Handoff Gate

- **Concept:** Observable, auditable, and phased transition gate recorded in version control.
- **Source:** Task lifecycle integrity and prevention of uncommitted working-tree drift across agent turns.
- **Adaptation:** Decomposed the handoff gate into:
  - `Phase 1: Lock` (`chore(handoff): close transition gate (pending)`);
  - `Phase 2: Prepare` (`chore(handoff): prepare context refresh and outcomes (pending confirmation)`);
  - `Phase 3: Release` (`chore(handoff): open transition gate (synchronized)`).
- **Rationale:** Ensures that the moment work begins is immutably captured, context refresh happens while the gate is still closed, and release is a distinct, verifiable action.
- **Decision:** Implemented as a core invariant in `fiss-maintain/SKILL.md`.

### 2. Gate Authorization Policy via Overrides

- **Concept:** Decoupling gate mechanics from human governance models.
- **Source:** User requirements for human-in-the-loop review (e.g. Human Review Surface) without polluting universal FISS standards with methodology-specific couplings.
- **Adaptation:** `fiss-maintain` checks project overrides (`FISS/overrides/`, e.g. `FISS/overrides/handoff.md`) during Phase 2. If the project policy requires human confirmation, the agent commits prepared outcomes under `pending` and awaits confirmation; if autonomous release is permitted, it proceeds to Phase 3.
- **Rationale:** Different projects, organizations, or safety levels require different gate policies. Using `FISS/overrides/` provides full extensibility while keeping universal skills completely portable.
- **Decision:** Standardized in `fiss-maintain/SKILL.md` and `DESIGN.md`.

### 3. Extraction of Initialization to `fiss-init`

- **Concept:** Single Responsibility Principle applied to Agent Skills.
- **Source:** Cognitive complexity management and tool simplification.
- **Adaptation:** Extracted root creation (`INDEX.md`, `BOOTSTRAP.md`), entry point configuration, and bootstrap scaffolding into `fiss-init`.
- **Rationale:** `fiss-maintain` was burdened with 0-to-1 bootstrap edge cases that are irrelevant during routine maintenance. Separating initialization creates clean, focused tools.
- **Decision:** Implemented across `fiss-init` and `fiss-maintain`.

### 4. Preservation-First Structural Maintenance

- **Concept:** Additive structural migration (`Expand -> Migrate -> Contract`) and consumer migration ownership.
- **Source:** `deprecation-and-migration` patterns and FISS navigation invariants.
- **Adaptation:** Mandatory migration gate for single-file to composite area transitions.
- **Rationale:** Prevents orphan references and broken navigation during directory restructuring.
- **Decision:** Retained as core invariant in `fiss-maintain/SKILL.md`.

### 5. Deterministic Delegation to `fiss-lint`

- **Concept:** Offload deterministic AST, syntax, and link checks to compiled native CLI.
- **Source:** Tooling division of responsibility.
- **Adaptation:** `fiss-maintain` invokes `fiss-lint --strict` and requires exit code 0.
- **Rationale:** Eliminates brittle manual link parsing and duplicates zero linter code in prompts.
- **Decision:** Retained as core invariant in `fiss-maintain/SKILL.md`.
