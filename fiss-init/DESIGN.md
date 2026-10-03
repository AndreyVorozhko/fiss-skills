# Design Model

## Responsibility

`fiss-init` is dedicated exclusively to the **0-to-1 initialization** of a FISS (File-based Intellectual Space Standard v1.0.0) space. 

In intellectual space design, bootstrapping a new space presents distinct architectural concerns from maintaining an existing one:
- **0-to-1 (Initialization):** Creating entry points, resolving conventions, establishing base policies, and ensuring baseline reachability.
- **Day 2 (Maintenance):** Integrating task diffs, preserving continuity across context switches, structural evolution (`Expand -> Migrate -> Contract`), and handoff gate progression.

By separating initialization into `fiss-init`, `fiss-maintain` is relieved of bootstrap complexity and can focus entirely on high-fidelity operational continuity during ongoing task lifecycles.

---

## Input to Result Model

```text
Target Repository
  │
  ├──► 1. Establish Environment & Baseline
  │      └── Probe fiss-lint CLI, inspect existing workflows and files
  │
  ├──► 2. Scaffold Conforming Root Structure
  │      └── Create FISS/INDEX.md and FISS/BOOTSTRAP.md
  │
  ├──► 3. Resolve & Scaffold Handoff Representation
  │      └── Resolve default FISS/state/fiss-handoff.md or custom path
  │
  ├──► 4. Anchor Principle 6 Continuity Mechanism
  │      └── Document 3-phase handoff gate & 7-point context refresh audit
  │
  ├──► 5. Configure Agent Entry Points
  │      └── Guide agents in AGENTS.md to FISS/INDEX.md and BOOTSTRAP.md
  │
  ├──► 6. Mechanical Verification
  │      └── Execute fiss-lint --strict . (require exit code 0)
  │
  └──► Initialized, Conforming FISS Space
```

---

## Division of Responsibility

| Tool | Role | Mutates? | Lifecycle Boundary |
|---|---|:---:|---|
| **`fiss-init`** | Bootstrapper | **Yes** | **0-to-1**: Scaffolds initial root, resolves handoff, establishes Principle 6 policy, configures agent entry points. |
| **`fiss-maintain`** | Operational Maintainer | **Yes** | **Day 2**: Manages 3-phase handoff gate (`Lock` -> `Prepare` -> `Release`), integrates task outcomes, updates navigation, performs structural refactoring. |
| **`fiss-validate`** | Auditor & Inspector | **No** | **Continuous**: Strictly read-only audit against 7C principles and normative requirements. |
| **`fiss-lint`** | CLI Linter | **No** | **All**: Deterministic mechanical validation (syntax, links, reachability, markers). |

---

## Architectural Principles of Initialization

### 1. Minimalist Scaffolding
FISS prohibits speculative structure. `fiss-init` does not pre-populate empty folders or mock documents for areas (`knowledge/`, `human/`, `overrides/`, `state/`) that have no immediate purpose. A space is born with only what is strictly necessary:
- `FISS/INDEX.md` (root navigation entry point)
- `FISS/BOOTSTRAP.md` (mandatory entry context)
- Resolved handoff file (e.g. `FISS/state/fiss-handoff.md`)
- Applicable workflow/gate documentation

### 2. Autonomous Anchoring of Principle 6 (Continuous)
Standard FISS requires that useful context changes are timely captured and preserved. Without an explicit continuity mechanism, AI agents routinely produce code while dropping architectural learnings, risks, decisions, and domain insights. 

`fiss-init` establishes this mechanism at the space's inception by:
1. Formulating the **Three-Phase Git-Committed Handoff Gate**:
   - **Phase 1: Lock (`pending` commit):** The gate is closed before substantive implementation begins to prevent untracked drift.
   - **Phase 2: Prepare (`pending` outcomes commit):** Context is refreshed across 7 dimensions (subject/project knowledge, ADRs, risks, open questions, glossaries) and verified via `fiss-lint --strict`.
   - **Phase 3: Release (`synchronized` commit):** The gate is opened once authorized.
2. Formulating the **7-Point Context Refresh Checklist** in project workflow or bootstrap context.
3. Enabling project-specific **Gate Authorization Policies** (autonomous release vs. human confirmation via `FISS/overrides/handoff.md`).

### 3. Non-Intrusive Integration with External Trackers
`fiss-init` respects external issue trackers (GitHub Issues, Taiga, Jira, Linear). It configures the handoff representation as a bridge recording *synchronization state*, not as a competing tracker. Canonical task IDs are referenced, never duplicated.
