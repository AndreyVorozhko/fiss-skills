---
name: fiss-init
description: Initializes a new conforming FISS intellectual space from scratch in a repository. Scaffolds root files (FISS/INDEX.md, BOOTSTRAP.md), establishes entry points, discovers task workflows, configures handoff mechanisms and Principle 6 continuity policies, and verifies the baseline with fiss-lint. Use when a repository does not have a conforming FISS space. Unlike fiss-maintain, owns 0-to-1 space initialization rather than Day 2 maintenance.
---

# FISS Init

## Responsibility

Initialize a conforming File-based Intellectual Space Standard (FISS v1.0.0) space from scratch in a repository. The skill owns the 0-to-1 bootstrap boundary: creating the root entry points, discovering existing task and state mechanisms, resolving the logical handoff representation, establishing standard Principle 6 continuity mechanisms, configuring agent entry points, and verifying the initial baseline with `fiss-lint`.

Use this skill when:
- The repository does not yet have a FISS intellectual space (`FISS/` directory, `FISS/INDEX.md`, or `FISS/BOOTSTRAP.md` is absent).
- A partial or non-conforming root exists and must be bootstrapped into a conforming FISS foundation.

Once the initial space is verified and synchronized, subsequent Day 2 operations (integrating task outcomes, updating navigation, and managing task-to-task context continuity) belong exclusively to `fiss-maintain`. Auditing without mutation belongs to `fiss-validate`. Mechanical validation belongs to `fiss-lint`.

## Non-negotiable Invariants

1. **Root Scaffolding Invariant:** A conforming FISS space MUST contain `FISS/INDEX.md` and `FISS/BOOTSTRAP.md`. The root index MUST link to bootstrap with a strict two-line item containing the exact English marker `Read when:` requiring it before project work (e.g. `Read when: starting any work or navigating the intellectual space`). The marker MUST NOT be localized or overridden.
2. **Minimalist Bootstrap Invariant:** Scaffolding creates ONLY the minimum necessary conforming files. Do NOT pre-create empty directories or placeholder files for `knowledge`, `state`, `overrides`, `human`, or other conventional areas unless the discovered project workflow explicitly requires them immediately.
3. **Partitioning Rules:**
   - If durable knowledge is created, it MUST be partitioned strictly into `FISS/knowledge/subject/` and/or `FISS/knowledge/project/` (as directories with `INDEX.md` or as single files `subject.md`/`project.md`). Never create loose files directly in `FISS/knowledge/`.
   - If human-oriented materials are created, they MUST be partitioned strictly into `FISS/human/knowledge/` and/or `FISS/human/hmm/` (never loose files directly in `FISS/human/`).
   - If state registries (ADRs, risks, open questions) are maintained, structure them as composite areas with `INDEX.md` or consolidated single files (never scattered loose files in `FISS/state/`).
4. **Canonical Source Invariant:** The handoff representation records FISS synchronization, not a competing task tracker. If the repository uses an external task or state mechanism (e.g. GitHub Issues, Taiga, Jira), reference that canonical task identity rather than duplicating backlogs or statuses in FISS.
5. **Principle 6 Continuity Invariant:** When initializing a new FISS intellectual space, if the user or project has not specified a custom Handoff Gate & Intellectual Space Refresh mechanism, `fiss-init` MUST autonomously introduce a standard, robust mechanism adapted to the project context (in workflow documentation, overrides, agent entry files, or handoff templates) to satisfy FISS Principle 6 (Continuous: timely capturing and preserving useful context changes). This mechanism MUST prescribe:
   - The two-phase Git-committed handoff gate protocol:
     - **Phase 1 (Lock):** Closing the gate with an atomic Git commit (`fiss synchronization: pending`) before starting task implementation;
     - **Phase 2 (Prepare):** Capturing/refreshing durable context across 7 dimensions (subject knowledge, project knowledge, ADRs, risks, open questions, subject terminology, project terminology), verifying with `fiss-lint --strict`, and checking gate release policy;
     - **Phase 3 (Release):** Opening the gate with an atomic Git commit (`fiss synchronization: synchronized`) once authorized (autonomously or upon required human/external confirmation).
   - Scaffolding the handoff artifact with the three outcome classifications (`Capture here`, `Delegate`, `No persistence`).
   - An explicit notification to the user in the initialization report.
6. **Agent Entry Integration Invariant:** If `AGENTS.md`, `CLAUDE.md`, `.cursorrules`, or another agent configuration file exists in the repository root, verify or configure it to mandate that agents read `FISS/INDEX.md` and strictly consult `FISS/BOOTSTRAP.md` before project work.
7. **Verification Invariant:** Initialization is never complete without fresh observable evidence from `fiss-lint --strict .` confirming zero errors and zero warnings.

---

## Establish

Before mutating files, inspect the repository environment:

1. **Verify Tooling Availability (`fiss-lint`):**
   - Run `command -v fiss-lint` (or check local project binaries `./bin/fiss-lint`, `~/.local/bin/fiss-lint`).
   - If missing, provision it:
     - If Go toolchain is available: run `go install github.com/AndreyVorozhko/fiss-lint/cmd/fiss-lint@latest` (or compile locally if in `fiss-lint` repo).
     - If Go is unavailable: download the prebuilt binary from GitHub Releases for current OS/architecture into `~/.local/bin/fiss-lint` and run `chmod +x`.
     - If installation is blocked, report `INSUFFICIENT_EVIDENCE` and request installation from the operator.
2. **Inspect Existing Repository State:**
   - Check if `FISS/` directory already exists. If `FISS/INDEX.md` and `FISS/BOOTSTRAP.md` exist and conform, halt: initialization is already complete; instruct the agent to use `fiss-maintain`.
   - Inspect repository documentation (`README.md`, `CONTRIBUTING.md`, `docs/`, `AGENTS.md`) for established task trackers, branching conventions, and developer workflows.
   - Record Git baseline: confirm repository status is clean or isolate existing user changes.

---

## Initialization Procedure

Execute initialization in six sequential steps:

### Step 1: Scaffold Root Files

Create the minimal conforming root structure:

1. **Create Directory:** Ensure `FISS/` directory exists at repository root.
2. **Create `FISS/INDEX.md`:**
   - Include standard title and brief description.
   - Include recommended link to the official standard: `[FISS Specification](https://fiss.vorozhko.ru)` (or official GitHub mirror `https://github.com/AndreyVorozhko/fiss`).
   - Link to `FISS/BOOTSTRAP.md` with strict two-line formatting:
     ```markdown
     - [Bootstrap Context](BOOTSTRAP.md)
       Read when: starting any work or navigating the intellectual space.
     ```
3. **Create `FISS/BOOTSTRAP.md`:**
   - State the project mission, high-level scope, and navigation instructions.
   - Explicitly instruct agents and developers to consult `FISS/INDEX.md` for situational routing and to respect the handoff gate.

### Step 2: Resolve Logical Handoff Representation

Resolve the logical handoff representation using the operational-artifact cascade:
1. If project conventions or configuration specify a path, use it.
2. Otherwise, use the standard FISS default: `FISS/state/fiss-handoff.md`.
3. If creating `FISS/state/fiss-handoff.md`:
   - Initialize with:
     ```markdown
     # FISS Task Handoff

     task: initial-space-setup
     task status: completed
     fiss synchronization: synchronized

     ## Outcomes Summary
     - Capture here: Root FISS files initialized (INDEX.md, BOOTSTRAP.md, state/fiss-handoff.md)
     - Delegate: None
     - No persistence: None
     ```
   - Register the handoff file in `FISS/INDEX.md` (or in `FISS/state/INDEX.md` if `FISS/state/` becomes a composite area):
     ```markdown
     - [Task Handoff](state/fiss-handoff.md)
       Read when: checking task continuity, handoff state, or transition gate status.
     ```

### Step 3: Establish Principle 6 Continuity Mechanism

Check whether the project specifies a custom handoff gate or context refresh protocol. If not, establish the standard, robust mechanism:
1. **Document Workflow Protocol:** In project workflow documentation (e.g., `FISS/knowledge/project/workflow.md` or directly in `FISS/BOOTSTRAP.md`), record:
   - **The Three-Phase Git-Committed Gate Protocol:**
     - **Phase 1 (Lock):** Before starting substantive implementation commits, transition handoff to `pending` and commit immediately (`chore(handoff): close transition gate (pending)`).
     - **Phase 2 (Prepare):** During work, capture outcomes across 7 dimensions (subject/project knowledge, ADRs, risks, open questions, glossaries); verify with `fiss-lint --strict`; stage classified outcomes in handoff; check gate release policy.
     - **Phase 3 (Release):** Upon authorization (autonomous or human confirmation per project policy), transition to `synchronized` and commit (`chore(handoff): open transition gate (synchronized)`).
   - **The 7-Point Pre-Synchronization Audit Checklist:**
     1. New subject knowledge (domain rules, facts, constraints)
     2. New project knowledge (technical facts, architectural patterns, conventions)
     3. New ADRs (architectural decisions, tradeoffs, lineage)
     4. New risks (potential failure modes, impacts, mitigations)
     5. New open questions (unresolved uncertainties, research gaps)
     6. New subject terminology (domain glossary entries)
     7. New project terminology (code/architecture glossary entries)
2. **Document Gate Authorization Policy:**
   - If the project requires human confirmation before opening the gate (e.g. via Human Review Surface or operator approval), create an override document (e.g. `FISS/overrides/handoff.md`) and register it in `FISS/overrides/INDEX.md`.
   - If the project permits autonomous opening by default, note this standard behavior.

### Step 4: Configure Agent Entry Points

Ensure agent configuration directs all agents to FISS:
1. If `AGENTS.md` exists, ensure it contains the standard instruction:
   ```markdown
   This project follows the [File-based Intellectual Space Standard (FISS)](https://fiss.vorozhko.ru).
   Before beginning any task, agents MUST navigate to and read:
   - [FISS Index](FISS/INDEX.md)
   and strictly read [Bootstrap Context](FISS/BOOTSTRAP.md) before starting project work.
   ```
2. If `AGENTS.md` does not exist, create it with this standard directive.

### Step 5: Verify Mechanical Conformance

1. Run deterministic validation:
   ```bash
   fiss-lint --strict .
   ```
2. Confirm exit code `0` (zero errors, zero warnings). If issues arise, repair immediately.

### Step 6: Commit and Report

1. Stage and commit the initialized FISS intellectual space:
   ```bash
   git add FISS/ AGENTS.md
   git commit -m "chore(fiss): initialize FISS intellectual space v1.0.0"
   ```
2. Emit the standardized initialization report.

---

## Reporting Contract

Conclude execution with the standardized initialization report:

```text
Initialization: initialized
FISS conformance: verified (fiss-lint --strict: clean)
Handoff representation: <resolved path, e.g. FISS/state/fiss-handoff.md>
Principle 6 continuity mechanism: established (<documentation path>) | custom
Gate release policy: autonomous | human confirmation required (<override path>)
Evidence: fiss-lint --strict . (exit code 0: clean)
Affected paths:
- FISS/INDEX.md
- FISS/BOOTSTRAP.md
- FISS/state/fiss-handoff.md
- <other configured files>
Notice: Standard Principle 6 context refresh checklist and 3-phase handoff gate protocol established.
```

---

## Additional Materials

- **`DESIGN.md`** — Architectural model of 0-to-1 initialization, separation from Day 2 maintenance, and Principle 6 anchoring.
- **`EXAMPLES.md`** — Concrete examples of greenfield initialization, existing repository adoption, and custom gate configuration.
- **`META.md`** — Provenance, rationale, and design history of `fiss-init`.
