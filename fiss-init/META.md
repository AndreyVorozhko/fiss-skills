# Provenance and Design Decisions

## Status

- **Skill status:** Extracted from `fiss-maintain` into dedicated `fiss-init` skill to enforce Single Responsibility Principle.
- **Static validation:** `passed`.
- **Specification alignment:** Strictly conforms to FISS v1.0.0 normative standard.

---

## Provenance Entries

### 1. Separation of 0-to-1 Initialization from Day 2 Maintenance

- **Concept:** Distinct skill lifecycles for project bootstrapping vs ongoing operational maintenance.
- **Source:** Standard software engineering separation of concerns; experience from `fiss-maintain` complexity growth.
- **Adaptation:** Extracted all root creation (`FISS/INDEX.md`, `BOOTSTRAP.md`), entry point configuration (`AGENTS.md`), and initial Principle 6 continuity mechanism setup into `fiss-init`. `fiss-maintain` now handles exclusively Day 2 operational continuity.
- **Rationale:** Merging initialization with routine maintenance bloated `fiss-maintain`, increased cognitive load, and forced maintenance prompts to carry setup edge cases that never occur during normal tasks.
- **Decision:** Implemented as `fiss-init`. When `fiss-maintain` detects an absent FISS space, it directs the agent to `fiss-init`.

### 2. Autonomous Principle 6 Continuity Mechanism

- **Concept:** Timely capturing and preserving useful context changes (FISS Principle 6: Continuous).
- **Source:** FISS v1.0.0 specification principles (7C).
- **Adaptation:** Proactively scaffolds the 7-point context refresh audit checklist and 3-phase handoff gate protocol during space initialization if no custom mechanism is specified.
- **Rationale:** Without an explicit, discoverable continuity mechanism anchored at space inception, agents fail to capture durable architectural decisions, risks, and domain knowledge generated during tasks.
- **Decision:** Mandated as an invariant in `fiss-init/SKILL.md`.

### 3. Three-Phase Git-Committed Handoff Gate Protocol

- **Concept:** Verifiable, immutable task transition checkpoints in version control.
- **Source:** Auditability requirements and prevention of uncommitted working-tree drift.
- **Adaptation:** Structured into three observable phases:
  - Phase 1: Lock (`pending` commit before substantive code);
  - Phase 2: Prepare (`pending` context refresh, verification, and outcome staging commit);
  - Phase 3: Release (`synchronized` commit upon authorization).
- **Rationale:** Ensures every state switch is recorded in Git history, prevents uncommitted `pending` drift across implementation commits, and provides a clear review checkpoint.
- **Decision:** Standardized in `fiss-init` documentation and templates.

### 4. Gate Authorization Policy Architecture

- **Concept:** Differentiating autonomous agent gate closure from projects requiring explicit human verification (e.g. Human Review Surface).
- **Source:** Enterprise governance and user requirements for human-in-the-loop verification.
- **Adaptation:** Enabled via standard `FISS/overrides/` mechanism (e.g. `FISS/overrides/handoff.md`). `fiss-init` allows bootstrapping with either autonomous release or mandatory human confirmation.
- **Rationale:** Different projects have different risk profiles; FISS standard does not dictate governance models, but provides the extension mechanism via `overrides/`.
- **Decision:** Documented in `DESIGN.md` and `EXAMPLES.md`.
