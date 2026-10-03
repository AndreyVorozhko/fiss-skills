# Design Model

## Responsibility

`fiss-maintain` is an operational continuity mechanism operating on an **existing** FISS intellectual space. A completed task can leave a valid code diff while losing durable context, current state, a navigation edge, or the location of an operational artifact. The skill treats task lifecycle transitions as synchronization events and keeps two outcomes separate:

1. **Synchronization completeness**: every durable result is captured across 7 context dimensions, delegated, or explicitly classified as not requiring persistence.
2. **FISS conformance**: the affected space still satisfies applicable FISS requirements and passes `fiss-lint --strict`.

Conformance alone cannot prove continuity. A space can be structurally valid and still omit the context that the next task needs.

0-to-1 initialization of a space from scratch is owned exclusively by **`fiss-init`**. `fiss-maintain` owns Day 2 operational continuity, structural evolution, and handoff gate progression.

---

## Input to Result Model

```text
Task Trigger
  │
  ├──► 1. Establish & Lock (Phase 1)
  │      ├── Verify fiss-lint & existing FISS root (or halt to fiss-init)
  │      └── Atomic Git commit: close gate (fiss synchronization: pending)
  │
  ├──► 2. Substantive Implementation
  │      └── Author code/documentation changes
  │
  ├──► 3. Context Refresh & Verification (Phase 2: Prepare)
  │      ├── 7-point context refresh audit (knowledge, ADRs, risks, questions, terms)
  │      ├── Execute fiss-lint --strict .
  │      ├── Stage classified outcomes in handoff artifact
  │      └── Check Gate Release Policy (overrides/ or workflow)
  │
  ├──► 4. Gate Authorization Check
  │      ├── Human confirmation required?
  │      │     └── Author "prepare" commit (pending), present review surface, await approval
  │      └── Autonomous permitted?
  │            └── Proceed directly to Phase 3
  │
  └──► 5. Release (Phase 3)
         └── Atomic Git commit: open gate (fiss synchronization: synchronized)
```

---

## Ownership Boundaries

- **Specialized skills** own semantic content such as ADRs, risks, open questions, and subject knowledge.
- **`fiss-maintain`** owns where that content is integrated, how it is navigated, what its read conditions are, and whether its canonical or derived status is clear. New knowledge is canonical by default; a representation based on existing knowledge uses `Derived from:` with direct source links.
- **`fiss-lint`** owns all deterministic AST and graph verification (links, anchors, reachability, syntax). `fiss-maintain` invokes `fiss-lint --strict` as its mechanical verification engine rather than implementing ad-hoc parsers.
- **`fiss-validate`** owns comprehensive semantic answers to “does the existing space conform”; `fiss-maintain` invokes or consumes its reports for broad topology migrations but does not become a duplicate validator.
- **`fiss-init`** owns initial space creation, minimal root scaffolding, and baseline configuration.

---

## Three-Phase Git-Committed Handoff Gate

To guarantee auditability, eliminate working-tree drift, and support flexible governance models, the transition gate operates across three distinct phases:

1. **Phase 1: Lock (Gate Closure Commit)**
   - Before substantive implementation code or commits are authored, the transition from `synchronized` to `pending` is committed to Git.
   - This provides an immutable timeline of when a task assumed control of the space and prevents uncommitted state drift across branches or developer checkouts.
   - Leaving `pending` uncommitted across task execution is prohibited.

2. **Phase 2: Prepare (Execution, Refresh, Verification & Policy Evaluation)**
   - All 7 context dimensions are audited and integrated into FISS.
   - Deterministic verification (`fiss-lint --strict`) proves zero defects.
   - Classified outcomes (`Capture here`, `Delegate`, `No persistence`) are documented in the handoff record.
   - **Gate Release Policy Check**: The skill checks project overrides (`FISS/overrides/`, e.g. `FISS/overrides/handoff.md`) or workflow documentation.
     - *If human confirmation is required:* The agent commits the prepared state while holding `fiss synchronization: pending`, presents the review surface (e.g. Human Review Surface) to the user, and halts until confirmation is given.
     - *If autonomous release is permitted:* The agent proceeds directly to Phase 3.

3. **Phase 3: Release (Gate Opening Commit)**
   - Upon receiving required authorization (or autonomously when permitted), the transition to `fiss synchronization: synchronized` is committed to Git, opening the gate for subsequent tasks.

---

## Preservation-First Maintenance

Structural maintenance starts with what must survive: knowledge, historical meaning, links, read conditions, canonicality, applicability, relationships, and reachability. `Expand -> Migrate -> Contract` makes intermediate states inspectable and assigns consumer migration to the initiator:

1. **Expand**: create target directory and `INDEX.md`; preserve old file.
2. **Migrate**: move/extract content, preserve read conditions, update incoming links.
3. **Contract**: remove or archive old file only after fresh link evidence confirms all traffic uses the new index.

---

## Validation Scope

- **Mechanical Verification**: Post-mutation verification executes `fiss-lint --strict <workspace>`. An exit code of 0 provides conclusive evidence that all link targets resolve, composite indexes exist, read conditions are syntactically present, reachability is unbroken, and derivation markers conform to the standard.
- **Semantic Review**: For significant migrations or topology restructuring, `fiss-maintain` invokes `fiss-validate` for deep qualitative 7C audit.
- Fresh observable evidence from `fiss-lint` is mandatory before completion claims.
