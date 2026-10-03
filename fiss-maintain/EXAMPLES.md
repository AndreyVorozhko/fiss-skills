# Examples

## Reading contract

Read this file when a maintenance case is ambiguous or when preparing a handoff report. Skip it when `SKILL.md` directly determines the action. These examples illustrate the contract and add no normative requirements.

## No FISS change

A formatting-only source-code change does not alter project knowledge, current state, rules, navigation, or task context. When that FISS-independent classification is supported by project evidence, classify the task result as no persistence and report:

```text
FISS change: not required
Synchronization: synchronized
Reason: no durable result or affected FISS invariant identified
```

This task may be reported as synchronized without a FISS mutation when the classification is supported. Before a different independent task starts, its identity is recorded with state `pending`. No separate FISS-independent exception is needed to apply the standard fallback.

## Delegated semantic content

An architecture decision belongs to an ADR owner. Classify it as `Delegate`, send the decision content to that skill, and integrate the resulting ADR into the applicable FISS index. Do not invent the decision or duplicate it in `state/`.

## Single-file to composite migration

```text
Preserve: old content, all backlinks, read condition, historical meaning
Change: create area/INDEX.md, extract coherent child documents, repoint links
Acceptable loss: none without project approval
```

Keep the old file until link and reachability evidence proves the new index is the live route. Then contract according to project policy.

## Canonical conflict

If an external tracker and `FISS/state/` disagree and no source rule exists, report:

```text
CONFLICT DETECTED
Sources: external tracker, FISS/state/current.md
Decision needed: select canonical source or define a selection rule
Mutation: blocked
```

Do not merge the values or select the newer file by timestamp.

## Derived representation

When a stakeholder architecture map summarizes several existing sources, place it in `FISS/human/hmm/` and include:

```markdown
Derived from:
- [System architecture](../../knowledge/project/architecture.md)
- [Billing domain](../../knowledge/subject/billing.md)
```

Do not add a separate canonical marker. If the architecture source later changes, treat the map as requiring review rather than silently maintaining both descriptions independently.

## Verification report

```text
Synchronization: synchronized
FISS conformance: verified
FISS change: applied
Principle 6 continuity mechanism: not applicable (maintain mode)
Evidence: fiss-lint --strict . (exit code 0: clean), backlink check
Affected paths: FISS/INDEX.md, FISS/knowledge/project/area/INDEX.md
```

Use `UNRESOLVED` when a required check was not run or a required decision remains open.

## Verification failure and repair

When `fiss-lint` reports broken invariants after a mutation:

```bash
fiss-lint --strict .
# Output:
# FISS/knowledge/INDEX.md:14: [FISS-R004] (error) Index link 'project/area/missing.md' does not resolve to an existing file
# Exit code: 1
```

`fiss-maintain` halts the handoff, applies the necessary repair, and reruns verification until exit code 0:

```bash
fiss-lint --strict .
# Output: clean
# Exit code: 0
```

## Initialization with autonomous Principle 6 continuity mechanism

When initializing a new FISS intellectual space where the user or project has not specified a custom Handoff Gate or context refresh protocol:

1. `fiss-maintain` creates conforming root files (`FISS/INDEX.md`, `FISS/BOOTSTRAP.md`), resolves `FISS/state/fiss-handoff.md`, and inspects project workflows.
2. It detects no custom context-refresh gate, and autonomously establishes the standard 7-point audit checklist in `workflow.md` (or `FISS/overrides/` / `FISS/BOOTSTRAP.md`).
3. It scaffolds `FISS/state/fiss-handoff.md` with the 3 outcome classes and checklist items.
4. It runs `fiss-lint --strict .` to prove structural compliance.
5. It outputs an explicit notice and report:

```text
Synchronization: synchronized
FISS conformance: verified
FISS change: applied
Principle 6 continuity mechanism: established (workflow.md, FISS/state/fiss-handoff.md)
Notice: Standard 7-point pre-synchronization audit checklist autonomously established to satisfy FISS Principle 6 (Continuous: timely capturing and preserving useful context changes).
Evidence: fiss-lint --strict . (exit 0); created FISS/INDEX.md, FISS/BOOTSTRAP.md, FISS/state/fiss-handoff.md; updated workflow.md
Affected paths: FISS/INDEX.md, FISS/BOOTSTRAP.md, FISS/state/fiss-handoff.md, workflow.md
```

## Blocking handoff

When synchronization is blocked by a decision, record `unresolved`. If outcomes simply remain to be integrated or checked, keep the state `pending`:

```text
fiss synchronization: unresolved
current work: <canonical work-item reference>
blocker: <decision that must be made>
```

Continue the identified work while it is `pending`. Do not start a different independent work item from `pending` or `unresolved`; low risk alone does not change the state.
