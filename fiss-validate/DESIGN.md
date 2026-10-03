# Design Model

## 1. Architectural Role and Operational Boundaries

`fiss-validate` is the evidence-grounded validation tool of the File-based Intellectual Space Standard (FISS). It exists to provide an objective, reproducible, and evidence-grounded assessment of an intellectual space's structural conformance and the discoverability of its preserved context.

It is an evidence-first auditor, not a formal mathematical theorem prover. It recognizes the inherent duality of FISS: strictly verifiable filesystem requirements on one hand, and qualitative architectural principles (7C) on the other.

### The Tri-Partite FISS Tooling Architecture

FISS architecture strictly separates concerns across three tooling layers to eliminate blind spots, hallucinations, and conflated responsibilities:

1. **`fiss-lint` (CLI Tool / Fast Mechanical Engine)**:
   - High-speed, deterministic, compiled binary.
   - Evaluates Level 1 deterministic invariants (`FISS-R001` through `FISS-R018`): AST parsing, link target and anchor resolution, reachability graph traversal, syntax scoping (code-fence stripping), and structural existence.
   - Zero hallucination, zero LLM token consumption, machine-readable JSON output, strict Unix exit codes.
2. **`fiss-validate` (Agent Skill / Impartial Semantic Inspector)**:
   - Read-only cognitive auditor.
   - Provisions and invokes `fiss-lint --format json` for mechanical checks.
   - Audits Level 2 semantic heuristics (7C principles): situational trigger clarity, index content leakage, information classification, canonical duplication & drift, and continuous maintenance mechanisms.
   - Synthesizes a unified report combining mechanical findings from `fiss-lint` and semantic findings from cognitive evaluation.
3. **`fiss-init` (Agent Skill / 0-to-1 Bootstrapper)**:
   - Scaffolds initial root structure (`FISS/INDEX.md`, `BOOTSTRAP.md`), handoff representation, and Principle 6 continuity mechanism from scratch.
4. **`fiss-maintain` (Agent Skill / Operational Mutator & Continuity Orchestrator)**:
   - Stateful Day 2 lifecycle mutator.
   - Manages the three-phase Git-committed handoff gate (Lock -> Prepare -> Release), classifies task outcomes (`Capture here`, `Delegate`, `No persistence`), synchronizes knowledge, and executes minimal scoped migrations.
   - Enforces the post-mutation verification gate by running `fiss-lint --strict` (and invoking `fiss-validate` for broad structural changes).

```text
       fiss-maintain                       fiss-validate
    (Operational Mutator)               (Semantic Inspector)
           │                                      │
           ▼                                      ▼
   What changed?                         Does it conform?
   What must be preserved?               Is context discoverable?
   Where to integrate?                   Are 7C principles held?
           │                                      │
           ▼                                      ▼
   [Filesystem Mutation]               [Cognitive Semantic Audit]
           │                                      │
           └──────────────────┬───────────────────┘
                              ▼
                      [ fiss-lint CLI ]
                 (Mechanical Verification Engine)
                 - Fast AST parsing
                 - Rules FISS-R001..FISS-R018
                 - Link & Anchor resolution
                 - Reachability graph traversal
                              │
                              ▼
                 [Unified Diagnostic Payload]
```

`fiss-maintain` is an operational continuity tool: it establishes task handoffs, migrates topologies, and repairs links. `fiss-validate` is an evidentiary tool: it checks observable conformance to FISS without touching a single byte on disk. Neither skill implements manual AST parsing or link-graph crawlers; that mechanical responsibility is delegated entirely to `fiss-lint`.

---

## 2. Core Evidentiary Model

The validator does not assess whether an intellectual space "feels good" or "looks complete." Every judgment proceeds through a rigorous five-stage evidentiary pipeline:

```text
FISS Specification + Applicable Project Overrides
                      │
                      ▼
            Applicable Requirements
                      │
                      ▼
             Observable Properties
                      │
                      ▼
               Fresh Evidence
                      │
                      ▼
                Determination
                      │
                      ▼
              Structured Finding
```

### The Evidentiary Unit

Every non-trivial statement emitted by the validator must satisfy the evidentiary equation:
$$\text{Observation} + \text{Evidence} + \text{Rule} \Longrightarrow \text{Finding}$$

1. **Observation**: Concrete, located entity in the workspace (file path, line number, verbatim text snippet).
2. **Evidence**: Observable state or discrepancy on disk (target file missing, zero incoming index links, missing read condition).
3. **Rule**: Normative clause from FISS specification or approved project override.
4. **Finding**: Formally typed result with Determination, Severity, and non-mutating Remedy Hint.

---

## 3. Calibration of Degrees of Freedom

To balance automated speed with deep contextual reasoning, `fiss-validate` divides verification into three distinct tiers of freedom:

```text
┌─────────────────────────────────────────────────────────────────────────┐
│ Level 1: Deterministic Invariants (Low Freedom / Delegated to fiss-lint)│
│ - Evaluated by compiled fiss-lint CLI (rules FISS-R001..FISS-R018)      │
│ - CommonMark syntax scoping (stripping code fences)                     │
│ - Mandatory root existence (FISS/INDEX.md, FISS/BOOTSTRAP.md)           │
│ - Index relative link target resolution (100% resolve to disk)          │
│ - Composite area INDEX.md presence (containers are excluded)            │
│ - Syntactic presence of read condition text in indexes                  │
│ - Reachability of all used areas from root index                        │
│ - Project overrides entry point and subject navigation                  │
│ - Canonical derived marker syntax (Derived from: + links)               │
│ - Handoff state semantic format and transition validity                 │
│                                                                         │
│ Execution: fiss-lint --format json                                      │
│ Outcome: VERIFIED, FAILED (on proven breach), or INSUFFICIENT_EVIDENCE  │
├─────────────────────────────────────────────────────────────────────────┤
│ Level 2: Evidence-Backed Semantic Heuristics (Medium Freedom / Corridor)│
│ - Evaluated by fiss-validate agent skill                                │
│ - Structural compactness ratchets (empty shells, trivial decomposition) │
│ - Context-first index leakage (link-to-prose density, embedded code)    │
│ - Read condition situational trigger clarity (when/who/condition)       │
│ - Classified domain separation (temporal state leakage into knowledge)  │
│ - Canonical duplication signals & derived-knowledge drift               │
│ - External mechanism independence (no duplicate issue backlogs)         │
│ - Continuous context maintenance mechanism (workflow gates, handoff)    │
│                                                                         │
│ Execution: Cognitive analysis of repository files                       │
│ Outcome: Typically VERIFIED, ADVISORY, UNRESOLVED, or INSUFFICIENT_EVIDENCE │
├─────────────────────────────────────────────────────────────────────────┤
│ Level 3: Epistemically Unprovable Properties (Open Field / Excluded)    │
│ - "Has all useful project knowledge been captured?"                     │
│ - "Which conflicting decision is historically correct?"                 │
│                                                                         │
│ Outcome: Excluded from Conformance (Declared Not Provable)              │
└─────────────────────────────────────────────────────────────────────────┘
*Note: INSUFFICIENT_EVIDENCE is a universal outcome applicable across both Level 1 and Level 2 when required context or target files outside the active scope cannot be evaluated.
```

---

## 4. Operationalization of the 7C Principles

The seven principles of FISS are operationalized into concrete, observable properties:

| Principle | Observable Property | Evidence Source | Inspection Tier | Determinations |
|:---|:---|:---|:---|:---|
| **Compact** | Absence of redundant empty shells or clearly redundant single-child composite areas. | Directory hierarchy and child entity relationships. | Structural Heuristic | `VERIFIED`, `ADVISORY` |
| **Context-aware** | Every index link declares situational applicability (read condition). | Link surroundings in `INDEX.md`; trigger phrase analysis. | Deterministic (presence) + Semantic (clarity) | `FAILED` (missing), `ADVISORY` (vague) |
| **Context-first** | Indexes navigate and select context; they do not store bulk content. | Ratio of links to prose; presence of large code blocks or H3/H4 text sections. | Structural Heuristic + Semantic | `VERIFIED`, `ADVISORY`, `FAILED` only on proven primary-content leakage |
| **Classified** | Clear separation of durable knowledge, current state, rules, and tools. | Temporal keywords in `knowledge/`; unexpired normative statements in `state/`. | Semantic Scan | `VERIFIED`, `ADVISORY`, `UNRESOLVED` |
| **Canonical** | Knowledge is canonical by default; exactly one canonical source exists for particular knowledge; derived knowledge uses `Derived from:` with source links. | Exact markers and source links; duplicate-knowledge signals; conflict detection without precedence rules. | Deterministic (marker/links/HMM) + Semantic (duplicates/conflicts) | `FAILED` (invalid derivation or proven independent duplication), `ADVISORY` (possible duplication or drift), `UNRESOLVED` (conflict) |
| **Continuous** | Preserved context is discoverable; unbroken navigation chains; presence of an observable operational mechanism/policy for timely capturing and synchronizing context changes, including the mandatory two-phase Git-committed handoff gate protocol. | Reachability of used areas from `FISS/INDEX.md`; index link target integrity; workflow gates, task overrides, or handoff records in `FISS/state/` or `FISS/knowledge/project/`. | Deterministic Graph Traversal + Semantic Mechanism Audit | `VERIFIED`, `FAILED` (unreachable used areas, broken index links), `WARNING`/`ADVISORY` (missing or unanchored continuous maintenance mechanism or missing gate closure rule) |
| **Composable** | Tool independence; overrides follow subject-based routing. | `overrides/INDEX.md` entry point; subject-based navigation; external state bounds. | Deterministic (overrides) + Semantic (trackers) | `FAILED` (tool-named overrides, missing entry) |

### The Continuous Discoverability Model

Conceptually, Continuous discoverability can be understood through the qualitative model:

$$\text{Continuous} \sim \text{Preserved Context} \times \text{Discoverability} \times \text{Context Selection} \times \text{Source Clarity}$$

> [!NOTE]
> This formula is an explanatory qualitative concept model, not a computable numeric score or percentage metric. Continuous is evaluated strictly as an observable property of *already preserved* context; `fiss-validate` checks observable discoverability conditions and never attempts to calculate artificial percentage coverage scores or guess unwritten knowledge.

#### What the Validator Verifies:
1. Navigation exists from root `INDEX.md` down to all used areas.
2. Every used area is reachable through the index hierarchy.
3. Index links are unbroken and resolve to actual files.
4. Read conditions are present and state when to read the target.
5. Derived knowledge uses `Derived from:` with resolvable source links, and independently duplicated canonical knowledge is surfaced.
6. A root `AGENTS.md`, when present, directs agents to `FISS/INDEX.md` without duplicating its content.
7. The project defines an observable operational mechanism, workflow gate, or policy ensuring that useful context changes are timely captured and synchronized before work items are concluded or marked `synchronized`, including the mandatory rule to close and commit the handoff gate (`fiss synchronization: pending`) before starting task implementation.

#### What the Validator Cannot Verify (Epistemic Boundaries):
1. Whether unrecorded knowledge from chats or meetings was lost.
2. Whether an absent document ought to have been written.
3. Whether `fiss-maintain` correctly classified a completed task's persistence needs.
4. Whether an unlinked standalone Markdown file is intended as an active single-file area when no declaration or project rule establishes that fact.

---

## 5. Deterministic Validation Checks (Delegated to `fiss-lint`)

`fiss-validate` does not perform ad-hoc regex scans or manual markdown parsing for deterministic rules. All formally observable Level 1 requirements are delegated to the compiled `fiss-lint` CLI, which executes the canonical ruleset:

1. **`FISS-R001` (Root Index)**: `FISS/INDEX.md` exists, is non-empty, and contains valid navigation.
2. **`FISS-R002` (Root Bootstrap)**: `FISS/BOOTSTRAP.md` exists and contains initial orientation content.
3. **`FISS-R003` (Bootstrap Link)**: Root index links to bootstrap with mandatory read condition before project work.
4. **`FISS-R004` (Index Link Resolution)**: 100% of relative links in indexes (outside code fences) resolve to valid targets.
5. **`FISS-R005` (Anchor Resolution)**: Markdown anchor fragments (`#heading`) resolve to existing document headings.
6. **`FISS-R006` (Read Conditions in Indexes)**: Every index navigation link declares when/under what conditions to read.
7. **`FISS-R007` (Single-File Read Conditions)**: Standalone single-file area links possess valid read conditions.
8. **`FISS-R008` (Composite Area Index)**: Composite areas contain their own `INDEX.md` (excluding pure container directories).
9. **`FISS-R009` (Reachability of Used Areas)**: Graph reachability from `FISS/INDEX.md` covers 100% of declared used areas.
10. **`FISS-R010` (Root Overrides Link)**: If `FISS/overrides/` exists, root index links to `FISS/overrides/INDEX.md`.
11. **`FISS-R011` (Overrides Subject-Based)**: Override navigation is organized by rule subject, not skill or tool name.
12. **`FISS-R012` (Derived Knowledge Marker)**: Derived knowledge uses exact `Derived from:` marker followed by valid links.
13. **`FISS-R013` (HMM Derivation)**: All content materials under `human/hmm/` declare explicit derivation sources.
14. **`FISS-R014` (AGENTS.md Navigation)**: Root `AGENTS.md` directs agents to `FISS/INDEX.md` without duplicating content.
15. **`FISS-R015` (Handoff State Semantic Validity)**: Logical handoff state format, allowed values, task identity.
16. **`FISS-R016` (Index Structure)**: Standard two-line index entry format (`- [Title](link)` followed by indented `Read when:`).
17. **`FISS-R017` (Naming Conventions)**: Directory and file naming conventions within `FISS/`.
18. **`FISS-R018` (Prohibited Content in Root)**: Root `FISS/` contains only permitted entry points and defined areas.

`fiss-validate` invokes `fiss-lint --format json`, parses the JSON stream, and translates any reported violations into `FAILED` or `ADVISORY` findings in the unified report.

---

## 6. Determination and Severity Taxonomy

`fiss-validate` maintains strict orthogonality between the evidentiary determination and its architectural severity:

```text
VERIFIED               → no finding, no severity
FAILED                 → ERROR (proven MUST/MUST NOT breach)
UNRESOLVED             → WARNING (decision needed; breach not proven)
INSUFFICIENT_EVIDENCE  → WARNING (required check cannot be completed)
ADVISORY               → WARNING or INFO (non-normative signal)
```

### Determinations

- **`VERIFIED`**: Evidence directly proves the requirement is fulfilled in the evaluated scope.
- **`FAILED`**: Concrete evidence demonstrates a violation of an applicable normative MUST/MUST NOT clause (derived from `fiss-lint` errors or proven semantic breaches).
- **`UNRESOLVED`**: The validator detects a conflict, competing truths, or ambiguity that cannot be resolved without a human project decision or missing selection rule. It highlights an open question, not necessarily an invalid space.
- **`INSUFFICIENT_EVIDENCE`**: The check could not be completed (e.g., target file in bounded scope is unreachable or external resource is inaccessible).
- **`ADVISORY`**: An architectural smell, complexity sprawl, or deviation from recommended practice (SHOULD) was observed, but does not violate a mandatory standard clause.

### Severities

- **`ERROR`**: Reserved for a proven `FAILED` breach of an applicable FISS `MUST` or `MUST NOT`; it blocks FISS conformance in the evaluated scope.
- **`WARNING`**: Poses a risk of cognitive drift, maintainability degradation, or unindexed content.
- **`INFO`**: Informational suggestion or minor stylistic optimization.

### Bounded Scope Conformance

Partial validation runs (e.g. `--scope navigation` or `--scope local <path>`) cannot emit global repository conformance. The report must state:
- Stated scope: `navigation`
- In-scope Conformance: `VERIFIED`
- Space-wide Conformance: `NOT EVALUATED (Bounded Scope)`
- Surveyed But Uninspected: explicitly enumerated.

---

## 7. Pipeline Integration with `fiss-lint` and `fiss-maintain`

`fiss-validate` provides objective diagnostic findings for the maintenance lifecycle without usurping the task-management authority of `fiss-maintain`. Both skills integrate with `fiss-lint` as their common mechanical verification foundation:

```text
┌────────────────────────────────────────────────────────────────────────┐
│                        fiss-maintain Lifecycle                         │
│                                                                        │
│ 1. Establish Baseline                                                  │
│ 2. Classify Handoff (Capture / Delegate / No persistence)              │
│ 3. Execute Scoped Mutations                                            │
│ 4. Verification Check:                                                 │
│    ├── Routine / Targeted Mutation ──► Run fiss-lint --strict          │
│    │   ├── Clean (exit 0) ───────────► Proceed to Handoff Gate         │
│    │   └── Errors (exit 1) ──────────► Repair mutations                │
│    └── Broad / Topology Change ──────► Invoke fiss-validate ─────┐     │
│                                                                  │     │
│                                  ┌───────────────────────────────┘     │
│                                  ▼                                     │
│                     fiss-validate Execution                            │
│                     1. Execute fiss-lint --format json (Mechanical)    │
│                     2. Execute Semantic Invariant Audit (Cognitive)    │
│                     3. Synthesize Unified Findings Payload             │
│                                  │                                     │
│                                  ▼                                     │
│ 5. Interpret Findings ◄──────────┴────────────────────────────────     │
│    (fiss-maintain evaluates payload against project transition rules)  │
│    ├── All required invariants verified ──► Proceeds to next steps     │
│    └── Unresolved or Failed findings ──► Decides repair, escalation,   │
│                                          or transition gating          │
└────────────────────────────────────────────────────────────────────────┘
```

The boundary between skills is strictly defined:
- `fiss-lint` verifies AST, links, anchors, and reachability with zero hallucination.
- `fiss-validate` establishes and reports semantic and mechanical facts (what conforms, what fails, what is unresolved or insufficient).
- `fiss-maintain` interprets those facts within the project's task lifecycle to determine whether to repair, escalate, or transition task state. `fiss-validate` never decides task transitions.

### The Diagnostic Payload Contract

To eliminate the need for parsing free-form text, `fiss-validate` provides a structured machine-readable payload containing both mechanical (`fiss-lint`) and semantic findings:
- `rule_id`: Standardized rule identifier (e.g., `FISS-R004`, `FISS-R009`, `FISS-SEM-READ-001`, `FISS-SEM-CONTINUOUS-001`).
- `determination`: `FAILED` | `UNRESOLVED` | `ADVISORY` | `INSUFFICIENT_EVIDENCE`.
- `severity`: `ERROR` | `WARNING` | `INFO`.
- `file`: Path relative to repository root.
- `line`: 1-indexed line number of the observation anchor.
- `quote`: Verbatim text snippet from the file (or empty for whole-file checks).
- `evidence`: Specific factual flaw.
- `remedy_hint`: Actionable hint for `fiss-maintain` to resolve the finding.
