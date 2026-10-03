# Examples

## Reading Contract

Read this file when encountering edge cases in finding formulation, scoping syntax, or formatting diagnostic reports. Skip it during routine execution when `SKILL.md` directly determines the action. These examples illustrate the contract and add no normative requirements.

---

## 0. fiss-lint Mechanical Execution and Output Mapping

`fiss-validate` autonomously provisions and invokes `fiss-lint` for all deterministic checks:

```bash
fiss-lint --format json .
```

### Raw `fiss-lint` Output Example

```json
[
  {
    "file": "FISS/knowledge/INDEX.md",
    "line": 15,
    "rule": "FISS-R004",
    "severity": "error",
    "message": "Index link 'project/architecture.md' does not resolve to an existing file"
  },
  {
    "file": "FISS/knowledge/security/INDEX.md",
    "line": 1,
    "rule": "FISS-R009",
    "severity": "error",
    "message": "Composite area 'FISS/knowledge/security/' is unreachable from FISS/INDEX.md"
  }
]
```

`fiss-validate` parses this JSON array directly into Level 1 findings (`FAILED` or `ADVISORY`), extracts verbatim quotes from the target files, and pairs each issue with standard remedies before performing its cognitive semantic audit.

---

## 1. Concrete Diagnostic Findings Across 7C

### FISS-R002: Missing Mandatory Root Bootstrap (Compact & Continuous)

```markdown
### [FINDING-01] FISS-R002: Missing Root Bootstrap Document
- **Source**: fiss-lint CLI (mechanical)
- **Determination**: FAILED
- **Severity**: ERROR
- **Principle**: Continuous
- **Location**: `FISS/INDEX.md:12`
- **Quote**: `[Getting Started](start.md)`
- **Evidence**: `FISS/BOOTSTRAP.md` does not exist on disk, and root `INDEX.md` lacks a link requiring bootstrap reading before work.
- **Rule**: FISS normative specification requires both `FISS/INDEX.md` and `FISS/BOOTSTRAP.md` at root.
- **Remedy Hint**: Create `FISS/BOOTSTRAP.md` defining project orientation and link it from `FISS/INDEX.md` with a read condition requiring it before any task.
```

### FISS-R004: Broken Index Navigation Link (Continuous)

```markdown
### [FINDING-02] FISS-R004: Unresolvable Index Navigation Link
- **Source**: fiss-lint CLI (mechanical)
- **Determination**: FAILED
- **Severity**: ERROR
- **Principle**: Continuous
- **Location**: `FISS/knowledge/INDEX.md:15`
- **Quote**: `[System Architecture](project/architecture.md)`
- **Evidence**: Target file `FISS/knowledge/project/architecture.md` does not exist on filesystem.
- **Rule**: Every index link MUST resolve to an existing physical target.
- **Remedy Hint**: Correct the relative path or invoke `fiss-maintain` to place the intended target.
```

### FISS-R009: Unreachable Used Area (Continuous Discoverability)

```markdown
### [FINDING-03] FISS-R009: Unreachable Used Area
- **Source**: fiss-lint CLI (mechanical)
- **Determination**: FAILED
- **Severity**: ERROR
- **Principle**: Continuous
- **Location**: `FISS/knowledge/security/INDEX.md:1`
- **Quote**: `# Security`
- **Evidence**: Composite area `FISS/knowledge/security/` has zero incoming navigation links from `FISS/INDEX.md` or any intermediate index.
- **Rule**: Every used area MUST be reachable from `FISS/INDEX.md` through indexes.
- **Remedy Hint**: Add an entry for `security/INDEX.md` into `FISS/knowledge/INDEX.md` with an appropriate read condition.
```

### FISS-SEM-REACH-002: Unindexed Standalone Markdown File (Continuous Diagnostic Signal)

```markdown
### [FINDING-04] FISS-SEM-REACH-002: Unindexed Markdown File
- **Source**: fiss-validate (semantic)
- **Determination**: ADVISORY
- **Severity**: INFO
- **Principle**: Continuous
- **Location**: `FISS/knowledge/project/draft-notes.md:1`
- **Quote**: `# Draft Notes on Caching`
- **Evidence**: File exists on disk within `FISS/` but is not referenced by any index and is not declared as a used area.
- **Rule**: This is a diagnostic signal only when the file's role cannot be established. FISS does not make every standalone Markdown file an area merely because it exists.
- **Remedy Hint**: If this document is an active used area, link it from `FISS/knowledge/project/INDEX.md`; if it is a scratch note, archive it outside `FISS/`.
```

### FISS-SEM-CONTINUOUS-001: Missing Operational Mechanism for Continuous Context Maintenance (Continuous)

```markdown
### [FINDING-04a] FISS-SEM-CONTINUOUS-001: Missing Operational Context Maintenance Mechanism
- **Source**: fiss-validate (semantic)
- **Determination**: WARNING
- **Severity**: WARNING
- **Principle**: Continuous
- **Location**: `FISS/state/fiss-handoff.md:6`
- **Quote**: `fiss synchronization: synchronized`
- **Evidence**: The project marks synchronization state as `synchronized`, but neither `FISS/knowledge/project/workflow.md`, `FISS/overrides/`, nor `FISS/BOOTSTRAP.md` defines an operational mechanism, handoff gate, or audit checklist to capture durable context changes before setting synchronized.
- **Rule**: Principle Continuous mandates that useful context changes must be timely captured into the intellectual space. When handoff states are managed, an observable workflow gate or protocol MUST govern when and how knowledge refresh occurs.
- **Remedy Hint**: Context analysis indicates this repository uses a multi-stage task lifecycle in `FISS/knowledge/project/workflow.md` and tracks operational state in `FISS/state/fiss-handoff.md`. Propose:
  1. Add an explicit "Handoff Gate & Intellectual Space Refresh" stage to `FISS/knowledge/project/workflow.md` requiring a 7-point audit (subject knowledge, project knowledge, ADRs, risks, open questions, subject terms, project terms) before closing tasks;
  2. Require structured outcome classification (`Capture here`, `Delegate`, `No persistence`) in `fiss-handoff.md` and invocation of `fiss-maintain` before declaring `synchronized`.
```


### FISS-R006: Naked Link in Index (Context-aware)

```markdown
### [FINDING-05] FISS-R006: Missing Read Condition
- **Source**: fiss-lint CLI (mechanical)
- **Determination**: FAILED
- **Severity**: ERROR
- **Principle**: Context-aware
- **Location**: `FISS/knowledge/INDEX.md:18`
- **Quote**: `- [Database Schema](database.md)`
- **Evidence**: Index link is listed without any accompanying text specifying when or by whom it should be read.
- **Rule**: Every index link MUST have a read condition defining situational applicability.
- **Remedy Hint**: Add a second line: `  Read when: modifying persistence models or running migrations.`
```

### FISS-SEM-READ-002: Vague or Degenerate Read Condition (Context-aware)

```markdown
### [FINDING-06] FISS-SEM-READ-002: Vague Read Condition
- **Source**: fiss-validate (semantic)
- **Determination**: ADVISORY
- **Severity**: WARNING
- **Principle**: Context-aware
- **Location**: `FISS/knowledge/INDEX.md:22`
- **Quote**: `- [Coding Style](style.md) — Useful document for developers.`
- **Evidence**: Read condition lacks a situational trigger, role, or phase criteria.
- **Rule**: Read conditions SHOULD provide clear criteria for context selection.
- **Remedy Hint**: Refine the second line to specify a trigger, for example `  Read when: writing or refactoring TypeScript code.`
```

### FISS-SEM-LEAK-001: Index Content Leakage (Context-first)

```markdown
### [FINDING-07] FISS-SEM-LEAK-001: Procedural Code in Index
- **Source**: fiss-validate (semantic)
- **Determination**: ADVISORY
- **Severity**: WARNING
- **Principle**: Context-first
- **Location**: `FISS/knowledge/deployment/INDEX.md:34-95`
- **Quote**: ````bash\n#!/usr/bin/env bash\nset -euo pipefail...````
- **Evidence**: `INDEX.md` contains a 60-line executable deployment script and detailed manual instructions instead of delegating to a child area.
- **Rule**: Indexes navigate and select context; they MUST NOT be primary repositories for detailed procedural content.
- **Remedy Hint**: Extract deployment script and instructions into `deploy-procedure.md` and link it from `INDEX.md`.
```

### FISS-SEM-CLASS-001: Current State Leaking into Durable Knowledge (Classified)

```markdown
### [FINDING-08] FISS-SEM-CLASS-001: Sprint State Leakage
- **Source**: fiss-validate (semantic)
- **Determination**: ADVISORY
- **Severity**: WARNING
- **Principle**: Classified
- **Location**: `FISS/knowledge/domain/billing.md:88`
- **Quote**: `TODO: In Sprint 42 we are temporarily disabling PayPal due to API outage.`
- **Evidence**: Transient operational sprint status is embedded inside durable domain specification.
- **Rule**: Durable knowledge and current state SHOULD remain distinguishable as different information classes.
- **Remedy Hint**: Move temporary sprint state to current task notes or `FISS/state/`, keeping `billing.md` focused on invariant domain rules.
```

### FISS-R013: HMM Material Missing Derivation (Canonical)

```markdown
### [FINDING-09] FISS-R013: HMM Material Missing Derived Source Links
- **Source**: fiss-lint CLI (mechanical)
- **Determination**: FAILED
- **Severity**: ERROR
- **Principle**: Canonical
- **Location**: `FISS/human/hmm/architecture-summary.md:3`
- **Quote**: `# Architecture Overview for Stakeholders`
- **Evidence**: Document resides in the HMM area but contains no exact `Derived from:` marker followed by Markdown source links.
- **Rule**: Every HMM material MUST contain `Derived from:` and link to one or more source materials.
- **Remedy Hint**: Add `Derived from:` followed by direct links, for example `- [System Architecture](../../knowledge/project/architecture.md)`.
```

### FISS-SEM-CANON-002: Competing Authoritative Sources (Canonical)

```markdown
### [FINDING-10] FISS-SEM-CANON-002: Unresolved Competing Sources
- **Source**: fiss-validate (semantic)
- **Determination**: UNRESOLVED
- **Severity**: WARNING
- **Principle**: Canonical
- **Location**: `FISS/knowledge/decisions/`
- **Quote**: `adr-004-auth.md` states JWT tokens are mandatory; `adr-012-session.md` states session cookies are mandatory.
- **Evidence**: Two conflicting architectural decisions exist without a superseding link or project precedence rule.
- **Rule**: When sources conflict and no selection rule exists, the conflict requires an explicit project decision; `UNRESOLVED` is not itself a proven normative breach.
- **Remedy Hint**: Require human architect or maintainer to decide whether ADR-012 supersedes ADR-004 or document the distinction.
```

### FISS-R011: Tool-Named Override Navigation (Composable)

```markdown
### [FINDING-11] FISS-R011: Tool-Centric Override Navigation
- **Source**: fiss-lint CLI (mechanical)
- **Determination**: FAILED
- **Severity**: ERROR
- **Principle**: Composable
- **Location**: `FISS/overrides/INDEX.md:12`
- **Quote**: `- [Claude Code Rules](claude-code.md) — Read before using Claude Code`
- **Evidence**: Override navigation is organized and named by specific skill/tool name rather than by the rule's subject matter.
- **Rule**: Override navigation MUST be based on the subject of a rule, not on a skill name. An override read condition MUST NOT require a particular skill name.
- **Remedy Hint**: Re-organize navigation by rule subject using the strict two-line form: `- [Code Review Rules](code-review.md)` followed by `  Read when: conducting automated code reviews.`
```

### FISS-SEM-CONT-001: Missing Handoff Gate Closure Rule (Continuous)

```markdown
### [FINDING-12] FISS-SEM-CONT-001: Missing Handoff Gate Closure Rule
- **Source**: fiss-validate (semantic)
- **Determination**: WARNING
- **Severity**: WARNING
- **Principle**: Continuous
- **Location**: `FISS/knowledge/project/workflow.md:140`
- **Quote**: `2. Ready -> In progress: Agent transitions story status to In progress...`
- **Evidence**: Project workflow defines task transitions and handoff file, but lacks a mandatory rule requiring the handoff gate closure (`fiss synchronization: pending`) to be committed to Git before task implementation begins.
- **Rule**: Principle Continuous requires an observable operational mechanism ensuring that context transitions are anchored; the transition gate MUST be closed with an atomic Git commit prior to authoring task code.
- **Remedy Hint**: Update `workflow.md` or `FISS/overrides/` to mandate an atomic Git commit (`chore(handoff): close transition gate (fiss synchronization: pending)`) before task code commits.
```

### FISS-SEM-CONT-002: Bypassed Handoff Transition Gate (Continuous)

```markdown
### [FINDING-13] FISS-SEM-CONT-002: Bypassed Handoff Transition Gate
- **Source**: fiss-validate (semantic)
- **Determination**: WARNING
- **Severity**: WARNING
- **Principle**: Continuous
- **Location**: `FISS/state/fiss-handoff.md:4`
- **Quote**: `task status: in_progress, fiss synchronization: pending` (uncommitted working tree modification)
- **Evidence**: Substantive implementation commits exist on the branch, while the pending handoff transition was left uncommitted in the working tree, bypassing Git-level transition gate tracking.
- **Rule**: Hand-off transitions MUST be recorded in version control via atomic Git commits to provide an auditable timeline and prevent uncommitted state drift.
- **Remedy Hint**: Author an atomic Git commit recording the closed transition gate before authoring further implementation commits.
```

---

## 2. Sample Comprehensive Report

````markdown
# FISS Validation Report

- **Scope**: full
- **In-Scope Conformance**: FAILED
- **Space-wide Conformance**: FAILED
- **Continuous Discoverability**: FAILED
- **Total Findings**: 3 (FAILED: 2, UNRESOLVED: 0, ADVISORY: 1, INSUFFICIENT_EVIDENCE: 0)
- **Surveyed But Uninspected**: None (Full repository scan executed)

## Summary of Breaches

1. `FISS-R004` in `FISS/knowledge/INDEX.md:14` (Broken index link — source: fiss-lint)
2. `FISS-R009` in `FISS/knowledge/legacy/` (Unreachable composite used area — source: fiss-lint)
3. `FISS-SEM-LEAK-001` in `FISS/INDEX.md:40` (Index content leakage — source: fiss-validate)

## Findings

### [FIND-01] FISS-R004: Broken Index Link
- **Source**: fiss-lint CLI (mechanical)
- **Determination**: FAILED
- **Severity**: ERROR
- **Principle**: Continuous
- **Location**: `FISS/knowledge/INDEX.md:14`
- **Quote**: `[API Guide](api/guide.md)`
- **Evidence**: Target file `FISS/knowledge/api/guide.md` does not exist on disk.
- **Rule**: Every index link MUST resolve to an existing target.
- **Remedy Hint**: Verify target path or invoke `fiss-maintain` to recreate the missing target.

### [FIND-02] FISS-R009: Unreachable Used Area
- **Source**: fiss-lint CLI (mechanical)
- **Determination**: FAILED
- **Severity**: ERROR
- **Principle**: Continuous
- **Location**: `FISS/knowledge/legacy/INDEX.md:1`
- **Quote**: `# Legacy Systems Knowledge`
- **Evidence**: Composite area exists on disk but has zero inbound links from root index graph.
- **Rule**: Every used area MUST be reachable from root index through indexes.
- **Remedy Hint**: Link from `FISS/knowledge/INDEX.md` or archive area outside `FISS/`.

### [FIND-03] FISS-SEM-LEAK-001: Dense Prose in Root Index
- **Source**: fiss-validate (semantic)
- **Determination**: ADVISORY
- **Severity**: INFO
- **Principle**: Context-first
- **Location**: `FISS/INDEX.md:40-75`
- **Quote**: `## Comprehensive Setup Instructions...`
- **Evidence**: Root index contains 35 lines of procedural setup steps with zero child links.
- **Rule**: Indexes should navigate and select context, not store detailed tutorials.
- **Remedy Hint**: Move setup instructions to `FISS/knowledge/project/setup.md`.

## Diagnostic Payload (for fiss-maintain)

```json
[
  {
    "id": "FIND-01",
    "rule_id": "FISS-R004",
    "source": "fiss-lint",
    "determination": "FAILED",
    "severity": "ERROR",
    "file": "FISS/knowledge/INDEX.md",
    "line": 14,
    "quote": "[API Guide](api/guide.md)",
    "evidence": "Target file FISS/knowledge/api/guide.md does not exist on disk",
    "remedy_hint": "Verify target path or recreate target"
  },
  {
    "id": "FIND-02",
    "rule_id": "FISS-R009",
    "source": "fiss-lint",
    "determination": "FAILED",
    "severity": "ERROR",
    "file": "FISS/knowledge/legacy/INDEX.md",
    "line": 1,
    "quote": "# Legacy Systems Knowledge",
    "evidence": "Composite area exists on disk but has zero inbound links from root index graph",
    "remedy_hint": "Link from FISS/knowledge/INDEX.md or archive area"
  },
  {
    "id": "FIND-03",
    "rule_id": "FISS-SEM-LEAK-001",
    "source": "fiss-validate",
    "determination": "ADVISORY",
    "severity": "INFO",
    "file": "FISS/INDEX.md",
    "line": 40,
    "quote": "## Comprehensive Setup Instructions...",
    "evidence": "Root index contains 35 lines of procedural setup steps with zero child links",
    "remedy_hint": "Move setup instructions to FISS/knowledge/project/setup.md"
  }
]
```
````

---

## 3. Scoped Audit Example (`--scope navigation`)

When a developer refactors the index hierarchy, a targeted navigation audit avoids reading unaffected content while explicitly bounding its claim:

```markdown
# FISS Validation Report

- **Scope**: navigation
- **In-Scope Conformance**: VERIFIED
- **Space-wide Conformance**: NOT EVALUATED (Bounded Scope)
- **Continuous Discoverability**: VERIFIED (for index paths)
- **Total Findings**: 0
- **Surveyed But Uninspected**:
  - `FISS/knowledge/**/*.md` (Content and classification uninspected)
  - `FISS/human/hmm/**/*.md` (HMM derivation and drift uninspected)
  - `FISS/state/**/*.md` (Operational state uninspected)

## Result
Navigation topology, index link resolution, composite indexes, and used area reachability are verified clean.
```
