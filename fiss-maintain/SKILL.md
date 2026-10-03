---
name: fiss-maintain
description: Maintains continuity and structural integrity of a FISS intellectual space across task changes. Use when initializing FISS, establishing or checking a task handoff before work, or when work may leave durable context, change FISS files, navigation, links, overrides, canonical sources, or operational-artifact locations. Unlike fiss-validate, it defines the handoff contract and integration change rather than only checking conformance.
---

# FISS Maintain

## Responsibility

Maintain continuity between completed work and the project's FISS intellectual space. The skill owns integration into the space: placement, navigation, links, read conditions, reachability, canonical-source clarity, and synchronization evidence. A specialized skill owns domain content; `fiss-maintain` does not decide domain truth, architecture, risk acceptance, ADR content, or open-question semantics.

Use this skill in one of two modes:

- **Initialize** when the repository does not have a conforming FISS entry point.
- **Maintain** after work, or when a requested change can affect FISS continuity or integrity.

`fiss-lint` performs deterministic mechanical validation of the space, while `fiss-validate` provides full mechanical and semantic verification. This skill answers what must be handed off, what must change, and how to leave the space synchronized. Use `fiss-lint` or `fiss-validate` for conformance evidence; do not perform manual link-by-link checking or duplicate mechanical validation.

## Non-negotiable invariants

- Before mutation, establish the repository baseline and read applicable project instructions, `FISS/INDEX.md`, `FISS/BOOTSTRAP.md`, and the `FISS/overrides/INDEX.md` entry point when it exists. Read only override documents whose subject and read conditions apply.
- A conforming root contains `FISS/INDEX.md` and `FISS/BOOTSTRAP.md`; the root index links to bootstrap with a read condition requiring it before project work.
- Every used area is reachable from the root index through indexes. Every composite area has `INDEX.md`. Every index link is formatted as a strict two-line item with the exact English read condition marker `Read when:` and a non-empty condition, resolving to an existing target. The marker MUST NOT be localized or overridden.
- Durable knowledge under `FISS/knowledge/` MUST be partitioned strictly into `subject` and/or `project` areas (as directories or `.md` files). Never create loose files directly in `FISS/knowledge/` (e.g. place architecture in `knowledge/project/` or `knowledge/project.md`, never directly in `knowledge/architecture.md`).
- Human-oriented materials under `FISS/human/` MUST be partitioned strictly into `knowledge` and/or `hmm` areas (as directories or `.md` files). Never create loose files directly in `FISS/human/`.
- In `FISS/state/`, standalone files (such as `fiss-handoff.md`) may reside directly in `state/`. Collections or registries of items (such as ADRs, risks, open questions) MUST NOT be scattered loosely across `FISS/state/`; structure them as composite areas with `INDEX.md` or consolidated single files.
- Indexes navigate and select context. Do not turn an index into the primary store for detailed content.
- New knowledge is canonical by default. A summary, restatement, map, diagram, projection, or other representation derived from existing knowledge MUST contain the exact, non-localized marker `Derived from:` followed by Markdown links to all material sources. Resolve each relative link from the derived material's actual containing directory and verify that it reaches the intended source. Do not introduce another derivation marker.
- The same knowledge MUST NOT be maintained independently in multiple places. Preserve one canonical source, mark every intentional derived representation, and do not silently choose between conflicting sources or create a competing task/state registry.
- Content materials in the composite area `FISS/human/hmm/`, and the single-file area `FISS/human/hmm.md`, are derived Human Mental Model representations, not independent sources. Every such HMM material MUST contain `Derived from:` and may link directly to relevant sources in any area of the intellectual space. A composite area's navigation-only `INDEX.md` is not itself an HMM representation.
- Preserve useful context, historical meaning, incoming links, read conditions, applicability boundaries, and relationships before changing structure.
- The component that changes structure owns migration of its consumers. Do not leave backlinks or navigation repair to a later task.
- Do not mutate project policy, authority, autonomy, approval, escalation, required checks, or other semantic behavior in `FISS/overrides/` without the project's required human approval.
- Do not claim synchronization or conformance without fresh verification evidence. Static reasoning and an intended command are not evidence.
- Two-Phase Git-Committed Transition Gate Invariant: Every transition of the FISS handoff gate MUST be immutably recorded in version control via an atomic Git commit:
  1. **Gate Closure (Before Task Implementation):** Before starting substantive work or authoring any implementation commits for a new independent task, transition the handoff record from `synchronized` to `pending` (declaring the new canonical task identity and `task status: in_progress`) and **immediately author an atomic Git commit recording the closed gate** (e.g., `chore(handoff): close transition gate (pending) . T-<id>`). Leaving `fiss synchronization: pending` uncommitted in the working tree across implementation commits is strictly prohibited. From `pending`, continue only the work item already identified there; from `unresolved`, do not start different work until the blocker is resolved. If state or work identity cannot be determined, stop and resolve the ambiguity. Do not mistake continuation for a new task.
  2. **Gate Opening (Upon Task Completion & Verification):** A completed task remains `pending` until FISS-relevant outcomes are captured, delegated, or explicitly classified as requiring no persistence, and fresh evidence from `fiss-lint --strict` confirms zero defects. Only then transition the handoff record to `synchronized` (with `task status: completed`) and **immediately author an atomic Git commit recording the opened gate and refreshed outcomes** (e.g., `chore(handoff): open transition gate (synchronized) . T-<id>`). Use `unresolved` when a decision is required. Do not infer synchronization from task completion, low risk, or confidence.
- Principle 6 Initialization Invariant: When initializing a new FISS intellectual space, if the user has not specified a custom Handoff Gate & Intellectual Space Refresh mechanism, fiss-maintain MUST autonomously introduce a standard, robust mechanism adapted to the project context (in workflow documentation, overrides, agent entry files, or handoff templates) to satisfy FISS Principle 6 (Continuous: timely capturing and preserving useful context changes). This mechanism MUST prescribe the mandatory two-phase Git-committed handoff gate protocol: (1) closing the gate with an atomic Git commit (`fiss synchronization: pending`) before starting task implementation, and (2) opening the gate with an atomic Git commit (`fiss synchronization: synchronized`) upon completing the 7-point context refresh audit. fiss-maintain MUST explicitly notify the user in the initialization report.
- Mechanical verification of the intellectual space is delegated to `fiss-lint` (or `fiss-validate`). `fiss-maintain` MUST NOT perform manual link-by-link checking or duplicate linter rules; before declaring a space synchronized, run `fiss-lint --strict` (or `fiss-validate`) to obtain fresh observable verification evidence.

## Establish

Probe the environment before assuming paths or tooling.

1. **Ensure `fiss-lint` Availability:**
   - Verify `fiss-lint` CLI availability: run `command -v fiss-lint` (or `which fiss-lint`).
   - If available: check version via `fiss-lint --version`.
   - If not found in PATH:
     - Check candidate local project binaries: `./bin/fiss-lint`, `/workspace/bin/fiss-lint`, `~/go/bin/fiss-lint`, `~/.local/bin/fiss-lint`.
     - If local binary exists, invoke it directly or add its containing directory to `PATH`.
     - If not found locally:
       - If Go toolchain is available (`command -v go`): install into the system via `go install github.com/AndreyVorozhko/fiss-lint/cmd/fiss-lint@latest` (or if inside the `fiss-lint` repository, compile with `go build -o ~/.local/bin/fiss-lint ./cmd/fiss-lint`).
       - If Go is unavailable: download the prebuilt binary from GitHub Releases for current OS/architecture into `~/.local/bin/fiss-lint` (or `/usr/local/bin/`) and run `chmod +x`.
       - If installation is blocked (e.g. no network, read-only filesystem), report `INSUFFICIENT_EVIDENCE` and request `fiss-lint` installation from the operator.
2. Identify repository root, applicable instruction files, current task/handoff state, user changes, available validation commands, and the logical location of operational artifacts. Resolve the handoff artifact in this order: explicit project path, shared task-artifact directory plus its default name, repository convention, then this skill default location `FISS/state/fiss-handoff.md`. Never create a second instance because another location was expected.
3. Check whether `FISS/` exists and whether its root files are present. If FISS exists, inspect the root index, bootstrap, applicable overrides, and only the linked branches needed for this task.
4. Record a read-only baseline before mutation: relevant status/diff, affected paths, and observable evidence. Do not overwrite unrelated user changes.
5. If the baseline is ambiguous, an applicable rule is unavailable, or a source conflict is found, stop the affected mutation and report the ambiguity.
6. Before a new independent task, determine the state using the resolved handoff representation. Apply an applicable project-defined transition mechanism; otherwise, from `synchronized`, transition the handoff record to `pending` before substantive work, and **immediately author an atomic Git commit recording the closed gate (`fiss synchronization: pending`)**. From `pending`, proceed only as a continuation of its identified work. From `unresolved`, stop different independent work until the required resolution. Do not leave the pending handoff uncommitted in the working tree across implementation commits. Do not create a duplicate record if no artifact exists at the expected path; resolve the logical representation first. If the record or state cannot be determined, stop and report the uncertainty.

## Initialize

When `FISS/INDEX.md` or `FISS/BOOTSTRAP.md` is absent:

1. Inspect the existing task workflow, canonical task/state source, and project entry points.
2. Create only the minimum conforming root files, with `FISS/INDEX.md` linking to `FISS/BOOTSTRAP.md` and requiring it before project work.
3. Resolve the logical FISS handoff representation using the operational-artifact cascade. If no project mechanism or repository convention exists, use `FISS/state/fiss-handoff.md` as the default representation and expose it from the applicable index. Record FISS synchronization state and a canonical work-item reference; do not create a competing task tracker. The standard agent fallback is sufficient; no enforcement infrastructure is required.
4. Ensure the required agent entry context and workflow documentation explain the two-phase Git-committed transitions: a new independent task starts from `synchronized`, closes the gate by recording `pending` with an immediate Git commit before substantive work, and cannot start from `pending` or `unresolved`; the already identified work may continue from `pending`. A project may define another transition mechanism.
5. If the project has `AGENTS.md` or another agent entry file, verify that it directs agents to `FISS/INDEX.md` and that the required entry workflow exposes the applicable transition rule before work begins. Hooks, CI, or wrappers are optional.
6. If the user or project has not specified a custom Handoff Gate & Intellectual Space Refresh mechanism, establish a standard, robust mechanism adapted to the project context to satisfy FISS Principle 6 (Continuous: timely capturing and preserving useful context changes):
   - Define a mandatory pre-synchronization completion audit in the project workflow documentation (e.g. `workflow.md`), project overrides (`FISS/overrides/`), or agent entry context (`AGENTS.md` / `FISS/BOOTSTRAP.md`).
   - The standard audit requires checking, before transitioning `fiss synchronization` from `pending` to `synchronized` (and before or alongside human review):
     1. New subject knowledge (domain rules, facts, constraints)
     2. New project knowledge (technical facts, architectural patterns, conventions)
     3. New ADRs (architectural decisions, tradeoffs, lineage)
     4. New risks (potential failure modes, impacts, mitigations)
     5. New open questions (unresolved uncertainties, research gaps)
     6. New subject terminology (domain glossary entries)
     7. New project terminology (code/architecture glossary entries)
     If any of these emerged during the task, integrate them into FISS using the corresponding specialized skill and `fiss-maintain`.
   - Scaffold the resolved handoff artifact (e.g. `FISS/state/fiss-handoff.md`) with the three outcome classifications (`Capture here`, `Delegate`, `No persistence`) reflecting these check items.
   - Explicitly warn/notify the user in the initialization report that this standard mechanism was introduced to ensure FISS Principle 6.

Do not pre-create `knowledge`, `state`, `overrides`, `human`, or other conventional areas unless the inspected workflow requires them. When knowledge is created, place it strictly within `subject` and/or `project`; when human materials are created, place them strictly within `knowledge` and/or `hmm` (never loose files directly in `knowledge/` or `human/`); when state registries are maintained, structure them as composite areas with `INDEX.md` or consolidated single files (never scattered loose files in `state/`).

### Handoff state and transition gate

The handoff artifact records FISS synchronization, not a second task tracker. It may reference the task identity held by an external canonical tracker:

```text
task: <canonical task reference>
task status: in_progress | completed
fiss synchronization: pending | synchronized | unresolved
```

Use these synchronization states and transitions:

```text
 synchronized -> new independent work recorded as pending (GIT COMMIT: Gate Closed)
       ^                                  |
       |                                  | work completes; synchronize FISS outcomes
       |                                  v
       +----------------------------- synchronized (GIT COMMIT: Gate Opened)
                                          |
                          blocker requiring decision -> unresolved
                                          |
                             resolution -> pending -> synchronized
```

#### Two-Phase Git-Committed Gate Protocol

To guarantee full auditability and prevent silent working-tree drift across tasks, transition gate state changes MUST be recorded in version control through atomic Git commits:

1. **Closing the Gate (`pending`):**
   - **Trigger:** Transitioning from `synchronized` to start work on a new independent task.
   - **Action:** Update the handoff artifact (`task status: in_progress`, `fiss synchronization: pending`, pointing to the new canonical task reference).
   - **Commit:** **Immediately author an atomic Git commit** before any substantive implementation commits:
     ```bash
     git add FISS/state/fiss-handoff.md
     git commit -m "chore(handoff): close transition gate (fiss synchronization: pending)"
     ```
     *(In projects with task tracking conventions, use `<type>(<scope>): <summary> . T-<id>`, e.g., `chore(handoff): закрытие transition gate перед началом работы . T-50`)*.
   - **Invariant:** It is strictly prohibited to leave `fiss synchronization: pending` uncommitted in the working tree across implementation commits.
2. **Opening the Gate (`synchronized`):**
   - **Trigger:** All constituent tasks, verifications (`fiss-lint --strict`), and durable context refresh (7-point audit) are complete.
   - **Action:** Update the handoff artifact (`task status: completed`, `fiss synchronization: synchronized`, documenting outcomes across all 7 dimensions).
   - **Commit:** **Author the concluding atomic Git commit**:
     ```bash
     git add FISS/state/fiss-handoff.md
     git commit -m "chore(handoff): open transition gate (fiss synchronization: synchronized)"
     ```
     *(e.g., `chore(handoff): фиксация результатов Story #50 и открытие transition gate . T-62`)*.

The record identifies the current work item or links to its external canonical identity; it does not own task status or a task backlog. Continue the identified work while `pending`; only a different independent work item is blocked. Write `synchronized` only when every FISS-relevant outcome is captured, delegated, or explicitly classified as requiring no persistence and applicable relationships are updated. `unresolved` blocks different independent work until the required decision. A project may define an alternate transition mechanism, but it cannot redefine the state meanings or imply synchronization while outcomes remain unaccounted for.

## Capture and handoff

Treat work completion as `pending` until handoff is complete. Inspect the actual result, not only the requested file diff. For every result with plausible durable value, assign exactly one class:

| Class | Action | Ownership |
|---|---|---|
| **Capture here** | Place or update it in FISS and integrate its navigation and source rule. Durable knowledge belongs in `knowledge/subject/` (`subject.md`) or `knowledge/project/` (`project.md`), never directly in `knowledge/`. Standalone human-oriented knowledge may belong in `human/knowledge/`; an HMM representation belongs in the composite area `human/hmm/` or single-file area `human/hmm.md` and is always derived. State registries belong in composite areas (e.g. `state/adr/INDEX.md`) or consolidated single files, never as loose files in `state/`. | `fiss-maintain` owns placement/integration. |
| **Delegate** | Hand semantic content to the specialized skill and ensure its resulting artifact can be integrated into FISS. | Specialized skill owns meaning; this skill owns integration. |
| **No persistence** | Record the explicit rationale in the current task report; do not create a permanent record merely to prove the classification. | No durable artifact required. |

No persistence is never an implicit “probably unimportant” choice. It requires observable grounds that the result cannot affect durable knowledge, current state, rules, decisions, navigation, canonical sources, operational-artifact relationships, or other FISS-relevant context. Record the explicit rationale in the current task report; do not create a permanent record merely to prove the classification. If classification cannot be made without deciding domain truth, escalate and leave the handoff `unresolved`; do not start the next task. A result is not synchronized until every durable result is captured or delegated and every no-persistence result is explicitly classified.

If the project has an external canonical task or state mechanism, use it for task identity and task status. Do not create a competing task tracker. Maintain one logical FISS handoff representation, resolved by the operational-artifact cascade, for synchronization state and the canonical work-item reference. The default location is `FISS/state/fiss-handoff.md`; `FISS/state/` is not required when the project resolves the representation elsewhere or uses an existing canonical mechanism. When state registries (such as ADRs, risks, or open questions) are maintained in `FISS/state/`, structure them as composite areas with `INDEX.md` or consolidated single files, never as loose files directly in `FISS/state/`.

## Analyze before mutation

Separate **evidence**, **decision**, and **mutation**. During evidence collection, do not edit files. Then produce a small decision set containing affected paths, affected invariants, preservation obligations, selected action, required approval, and verification command or observable check.

Use the smallest justified scope:

- If no durable result belongs in FISS and no FISS invariant is affected, finish with **FISS CHANGE NOT REQUIRED** and provide the classification evidence.
- For new knowledge, treat the selected material as canonical unless it is based on existing knowledge. If it is a restatement, summary, map, diagram, projection, or other derived representation, add `Derived from:` and direct Markdown links to every material source.
- For content changes, update only the canonical owner. If the changed reality is outside FISS, update the external source or report drift; do not rewrite FISS by age or guesswork. Search for materials that derive from the changed source and review them for required updates.
- If the same knowledge is maintained in several places, identify the established canonical source, convert intentional secondary representations to derived knowledge, and eliminate independent maintenance. If no source rule establishes the canonical source, report a conflict and stop the affected mutation.
- For navigation changes, preserve target, read condition, parent index, reachability, and backlinks.
- For a possible split, merge, deletion, or move, first inspect why the existing structure exists, including history and incoming references. A size signal alone is not a reason to fragment.
- For competing sources, apply the project's established source rule. Without one, emit **CONFLICT DETECTED**, show the conflicting sources and choices, and escalate; never synthesize a winner silently.

### Preservation-first record

Before a structural mutation, state:

```text
Preserve: knowledge, historical meaning, incoming links, read conditions,
          canonicality, applicability, relationships, and reachability.
Change:   exact files and paths within the task scope.
Acceptable loss: only explicitly approved and documented loss.
```

## Maintain

Operate strictly within the established project workflow and handoff gate; do not inject new workflow mechanisms or alter project workflow policy during routine task maintenance. Apply one minimal logical mutation at a time. Keep indexes concise and use existing project conventions. Do not globally regenerate or re-style the space for a local defect.

For a single-file area becoming a composite area, use this gate:

1. **Expand**: create the directory and its `INDEX.md`; preserve the old file.
2. **Migrate**: move or extract content, preserve applicable read conditions, and update every incoming link to the new index. The initiator owns this migration.
3. **Contract**: only after fresh evidence shows the new route is reachable and no required reference targets the old file, remove or archive the old file according to project policy.

For historical decisions, preserve historical meaning. Supersede or deprecate with a new decision and a status link when the project supports that lifecycle; do not rewrite the old decision as if it always meant the new state. Correct factual errors only when the correction does not falsify the historical decision.

Mechanical link/path/navigation repairs may be autonomous when evidence is sufficient. Changes to authority, autonomy, approval, escalation, mandatory checks, or project behavior require the project's approval threshold. Deletion, unresolved canonical conflict, and destructive topology changes are stop conditions unless explicitly authorized.

For a batch or destructive migration, use a work packet with `Objective`, `Scope`, `Changes`, `Invariants`, `Preconditions`, `Validation`, `Stop conditions`, and `Recovery`. Validate the packet before execution and keep ownership of overlapping files unambiguous.

## Verify and hand off

Choose verification based on the affected invariants and delegate mechanical checks directly to `fiss-lint`:

1. **Deterministic Verification via `fiss-lint`:**
   - Execute:
     ```bash
     fiss-lint --strict [target_path]
     ```
   - Exit code `0` confirms that all mechanical invariants (root structure, index syntax, link resolution, composite area indexes, reachability, area partitioning, and derivation markers) are clean.
   - If `fiss-lint` emits errors or warnings, resolve them before proceeding. Do not attempt manual link verification in place of the linter.
2. **Semantic and State Verification:**
   - Verify that changed artifacts exist, contain intended durable content, and are integrated into navigation.
   - For complex semantic audits or whole-space assessment, invoke the `fiss-validate` skill.
   - Verify that all durable outcomes are explicitly classified (`Capture here`, `Delegate`, `No persistence`) per the Principle 6 continuity mechanism.
   - Verify precedence, applicability, and semantic risk when overrides were changed.

Only after fresh command output from `fiss-lint` (or `fiss-validate`) confirms zero defects and all outcomes are classified may you report `SYNCHRONIZED`. Otherwise report `UNRESOLVED`; this blocks the next task by default. A project-defined exception must be named and evidenced in the report. Report separately:

```text
Synchronization: synchronized | unresolved
FISS conformance: verified | not verified | failed
FISS change: applied | not required | blocked
Principle 6 continuity mechanism: established (<path>) | custom | not applicable (maintain mode)
Evidence: commands/checks (e.g. fiss-lint --strict), affected paths, and relevant results
```

## Additional materials

- **`DESIGN.md`** — read when designing or revising the skill, explaining its conceptual model. Do not read for ordinary maintenance execution when this contract is sufficient.
- **`META.md`** — read when reviewing provenance, rationale, rejected alternatives, or validation status. It is not a runtime policy source.
- **`EXAMPLES.md`** — read when the task involves an ambiguous handoff classification, migration, canonical conflict, or verification report. Skip it for an unambiguous local change.

These files explain or illustrate the contract. They never replace the critical invariants above.
