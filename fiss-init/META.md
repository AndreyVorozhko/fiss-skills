# Provenance and Design Decisions

## Status

- **FISS-Relevant Outcome Classification Model (FISS v1.0.0):** `passed`. Integrated work classification taxonomy initialization. Analyzes project to discover FISS-relevant vs operational work classes during initialization and creates `FISS/knowledge/project/work-classification.md`.
- **Skill status:** Extracted from `fiss-maintain` into dedicated `fiss-init` skill to enforce Single Responsibility Principle.
- **Static validation:** `passed`.
- **Specification alignment:** Strictly conforms to FISS v1.0.0 normative standard.

---

## Provenance Entries

### 1. Work Classification Taxonomy Initialization (FISS v1.0.0 Clarification)

- **Concept:** Project-specific taxonomy distinguishing FISS-relevant work (changes what the intellectual space represents) from operational work (uses the space as context).
- **Source:** FISS v1.0.0 specification clarification (2026-10-06) addressing ambiguity where any FISS context use could be misinterpreted as requiring FISS synchronization.
- **Adaptation:**
  - Added Step 2 to initialization procedure: "Analyze Project and Create Work Classification Taxonomy"
  - Analyzes repository structure, documentation, commit history, and issue tracker for work patterns
  - Creates `FISS/knowledge/project/work-classification.md` with project-specific work classes and concrete examples
  - Links to work-classification.md from `FISS/knowledge/project/INDEX.md` with appropriate read condition
  - Discovers at least 2-3 concrete examples for each work class (FISS-relevant and operational)
- **Rationale:** Without explicit work classification at initialization, agents cannot correctly determine when to apply the expensive three-phase handoff gate and 7-point audit. Operational work (e.g., catalog operations, routine content updates) should efficiently use FISS context without triggering unnecessary synchronization overhead.
- **Decision:** Implemented as invariant 5 in `fiss-init/SKILL.md`. Work classification taxonomy is canonical project knowledge.
- **Validation:** Aligned with `fiss-maintain` (maintains work-classification.md) and `fiss-validate` (verifies existence and correct application).

### 2. Separation of 0-to-1 Initialization from Day 2 Maintenance

### 2. Separation of 0-to-1 Initialization from Day 2 Maintenance

- **Concept:** Distinct skill lifecycles for project bootstrapping vs ongoing operational maintenance.
- **Source:** Standard software engineering separation of concerns; experience from `fiss-maintain` complexity growth.
- **Adaptation:** Extracted all root creation (`FISS/INDEX.md`, `BOOTSTRAP.md`), entry point configuration (`AGENTS.md`), and initial Principle 6 continuity mechanism setup into `fiss-init`. `fiss-maintain` now handles exclusively Day 2 operational continuity.
- **Rationale:** Merging initialization with routine maintenance bloated `fiss-maintain`, increased cognitive load, and forced maintenance prompts to carry setup edge cases that never occur during normal tasks.
- **Decision:** Implemented as `fiss-init`. When `fiss-maintain` detects an absent FISS space, it directs the agent to `fiss-init`.

### 3. Autonomous Principle 6 Continuity Mechanism

- **Concept:** Timely capturing and preserving useful context changes (FISS Principle 6: Continuous).
- **Source:** FISS v1.0.0 specification principles (7C).
- **Adaptation:** Proactively scaffolds the 7-point context refresh audit checklist and 3-phase handoff gate protocol during space initialization if no custom mechanism is specified. **Note (FISS v1.0.0):** Gate and audit apply only to FISS-relevant work.
- **Rationale:** Without an explicit, discoverable continuity mechanism anchored at space inception, agents fail to capture durable architectural decisions, risks, and domain knowledge generated during tasks.
- **Decision:** Mandated as an invariant in `fiss-init/SKILL.md`.

### 4. Three-Phase Git-Committed Handoff Gate Protocol

- **Concept:** Verifiable, immutable task transition checkpoints in version control.
- **Source:** Auditability requirements and prevention of uncommitted working-tree drift.
- **Adaptation:** Structured into three observable phases:
  - Phase 1: Lock (`pending` commit before substantive code);
  - Phase 2: Prepare (`pending` context refresh, verification, and outcome staging commit);
  - Phase 3: Release (`synchronized` commit upon authorization).
- **Rationale:** Ensures every state switch is recorded in Git history, prevents uncommitted `pending` drift across implementation commits, and provides a clear review checkpoint. **Note (FISS v1.0.0):** Gate applies only to FISS-relevant work.
- **Decision:** Standardized in `fiss-init` documentation and templates.

### 5. Gate Authorization Policy Architecture

- **Concept:** Differentiating autonomous agent gate closure from projects requiring explicit human verification (e.g. Human Review Surface).
- **Source:** Enterprise governance and user requirements for human-in-the-loop verification.
- **Adaptation:** Enabled via standard `FISS/overrides/` mechanism (e.g. `FISS/overrides/handoff.md`). `fiss-init` allows bootstrapping with either autonomous release or mandatory human confirmation.
- **Rationale:** Different projects have different risk profiles; FISS standard does not dictate governance models, but provides the extension mechanism via `overrides/`.
- **Decision:** Documented in `DESIGN.md` and `EXAMPLES.md`.
