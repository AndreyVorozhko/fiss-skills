# Examples

## Reading Contract

Read this file when a maintenance case is ambiguous, when configuring the three-phase handoff gate, or when preparing a handoff report. Skip it when `SKILL.md` directly determines the action. These examples illustrate the contract and add no normative requirements.

---

## Example 1: Three-Phase Git-Committed Gate Lifecycle (Human Confirmation Policy)

In a project where `FISS/overrides/handoff.md` mandates human confirmation before opening the gate:

### Phase 1: Lock (Before Starting Task Implementation)
1. Update `FISS/state/fiss-handoff.md`:
   ```markdown
   # FISS Task Handoff

   task: https://taiga.example.com/project/myproject/us/51
   task status: in_progress
   fiss synchronization: pending
   ```
2. Commit immediately before authoring substantive code:
   ```bash
   git add FISS/state/fiss-handoff.md
   git commit -m "chore(handoff): close transition gate (fiss synchronization: pending) . T-51"
   ```

### Implementation Phase
- Implement task code, write tests, author documentation commits.

### Phase 2: Prepare (Context Refresh & Verification)
1. Conduct 7-point context refresh audit.
2. Update FISS knowledge documents.
3. Run verification:
   ```bash
   fiss-lint --strict .
   # Output: clean (exit code 0)
   ```
4. Update `FISS/state/fiss-handoff.md` with classified outcomes:
   ```markdown
   # FISS Task Handoff

   task: https://taiga.example.com/project/myproject/us/51
   task status: in_progress
   fiss synchronization: pending

   ## Outcomes Summary
   - Capture here: Updated FISS/knowledge/project/architecture.md and workflow.md
   - Delegate: None
   - No persistence: Routine install scripts test
   ```
5. Commit the prepared outcomes:
   ```bash
   git add FISS/
   git commit -m "chore(handoff): prepare context refresh and outcomes (pending confirmation) . T-51"
   ```
6. Present the Human Review Surface to the operator and await explicit approval. Do NOT open the gate autonomously!

### Phase 3: Release (Upon Receiving Human Confirmation)
1. Upon user saying "Task approved / Задачу принимаю":
2. Update `FISS/state/fiss-handoff.md`:
   ```markdown
   # FISS Task Handoff

   task: https://taiga.example.com/project/myproject/us/51
   task status: completed
   fiss synchronization: synchronized
   ```
3. Commit the opened gate:
   ```bash
   git add FISS/state/fiss-handoff.md
   git commit -m "chore(handoff): open transition gate (fiss synchronization: synchronized) . T-51"
   ```

---

## Example 2: Three-Phase Gate Lifecycle (Standard Autonomous Flow)

In a project without human confirmation overrides:

1. **Phase 1 (Lock):** Update `fiss-handoff.md` to `pending` and author lock commit.
2. **Implementation:** Author code and tests.
3. **Phase 2 (Prepare):** Audit 7 context dimensions, verify via `fiss-lint --strict .`, stage outcomes in `fiss-handoff.md`. Evaluates gate release policy $\rightarrow$ autonomous release permitted.
4. **Phase 3 (Release):** Update `fiss-handoff.md` to `synchronized` and `task status: completed`, author concluding release commit:
   ```bash
   git add FISS/
   git commit -m "chore(handoff): open transition gate (fiss synchronization: synchronized) . T-50"
   ```

---

## Example 3: Invocation on Uninitialized Repository

When `fiss-maintain` is invoked in a repository lacking `FISS/INDEX.md`:

```text
Status: BLOCKED
Finding: FISS NOT INITIALIZED
Evidence: FISS/INDEX.md not found in repository root.
Remedy Hint: Invoke skill `fiss-init` to bootstrap a conforming FISS space.
```

---

## Example 4: No FISS Change

A formatting-only source-code change does not alter project knowledge, current state, rules, navigation, or task context:

```text
FISS change: not required
Synchronization: synchronized
Reason: no durable result or affected FISS invariant identified
```

---

## Example 5: Delegated Semantic Content

An architecture decision belongs to an ADR owner:
1. Classify as `Delegate`.
2. Hand decision content to `adr-maintain`.
3. Integrate the resulting ADR into `FISS/state/adr/INDEX.md`.

---

## Example 6: Single-File to Composite Migration

```text
Preserve: old content, all backlinks, read condition, historical meaning
Change: create area/INDEX.md, extract coherent child documents, repoint links
Acceptable loss: none without project approval
```

Keep old file until link and reachability evidence proves the new index is live. Then contract according to project policy.

---

## Example 7: Canonical Conflict

If an external tracker and `FISS/state/` disagree and no source rule exists:

```text
CONFLICT DETECTED
Sources: external tracker, FISS/state/current.md
Decision needed: select canonical source or define a selection rule
Mutation: blocked
```

Do not merge the values or select the newer file by timestamp.

---

## Example 8: Verification Failure and Repair

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
