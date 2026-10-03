# Examples

## Example 1: Greenfield Initialization (Standard Autonomous Gate)

In a new project without any existing intellectual space:

1. **Invoke `fiss-init`**.
2. **Resulting File Structure:**
   ```text
   .
   ├── AGENTS.md
   └── FISS/
       ├── INDEX.md
       ├── BOOTSTRAP.md
       └── state/
           └── fiss-handoff.md
   ```
3. **`FISS/INDEX.md`:**
   ```markdown
   # Intellectual Space Index

   Entry point for navigating the project's intellectual space under [FISS v1.0.0](https://fiss.vorozhko.ru).

   - [Bootstrap Context](BOOTSTRAP.md)
     Read when: starting any work or navigating the intellectual space.
   - [Task Handoff](state/fiss-handoff.md)
     Read when: checking task continuity, handoff state, or transition gate status.
   ```
4. **`FISS/BOOTSTRAP.md`:**
   ```markdown
   # Bootstrap Context

   ## Project Scope
   Backend service for customer notifications.

   ## Principle 6 Continuous Context Refresh & Handoff Gate Protocol
   This project strictly enforces the three-phase Git-committed handoff gate protocol:
   1. **Phase 1 (Lock):** Before starting task implementation, transition handoff to `pending` and commit immediately (`chore(handoff): close transition gate (pending)`).
   2. **Phase 2 (Prepare):** Upon completing task code, conduct the 7-point context refresh audit, verify with `fiss-lint --strict`, stage outcomes in `fiss-handoff.md`, and check gate release policy.
   3. **Phase 3 (Release):** Transition handoff to `synchronized` and commit (`chore(handoff): open transition gate (synchronized)`).
   ```
5. **Report Output:**
   ```text
   Initialization: initialized
   FISS conformance: verified (fiss-lint --strict: clean)
   Handoff representation: FISS/state/fiss-handoff.md
   Principle 6 continuity mechanism: established (FISS/BOOTSTRAP.md)
   Gate release policy: autonomous
   Evidence: fiss-lint --strict . (exit code 0: clean)
   Affected paths:
   - FISS/INDEX.md
   - FISS/BOOTSTRAP.md
   - FISS/state/fiss-handoff.md
   - AGENTS.md
   Notice: Standard Principle 6 context refresh checklist and 3-phase handoff gate protocol established.
   ```

---

## Example 2: Project with Human Confirmation Gate Override

When initializing a project whose governance requires explicit human review and approval (such as Human Review Surface) before opening the handoff gate:

1. **Scaffold root structure and standard handoff.**
2. **Add Gate Policy Override `FISS/overrides/handoff.md`:**
   ```markdown
   # Handoff Gate Human Confirmation Protocol

   ## Applicability
   Applies to all tasks concluding execution in this repository.

   ## Rule
   Automated agents MUST NOT transition `fiss synchronization` from `pending` to `synchronized` autonomously.
   Upon concluding task implementation and passing verification (`fiss-lint --strict`), the agent MUST:
   1. Author a Phase 2 commit retaining `fiss synchronization: pending` (e.g. `chore(handoff): prepare context refresh and outcomes (pending confirmation)`);
   2. Present the Human Review Surface to the operator;
   3. Wait for explicit human confirmation before authoring the Phase 3 `synchronized` commit.
   ```
3. **Register in `FISS/overrides/INDEX.md` and link from `FISS/INDEX.md`:**
   ```markdown
   - [Handoff Gate Override](overrides/handoff.md)
     Read when: closing tasks, managing handoff gates, or transitioning synchronization state.
   ```
4. **Report Output:**
   ```text
   Initialization: initialized
   FISS conformance: verified (fiss-lint --strict: clean)
   Handoff representation: FISS/state/fiss-handoff.md
   Principle 6 continuity mechanism: established (FISS/BOOTSTRAP.md)
   Gate release policy: human confirmation required (FISS/overrides/handoff.md)
   Evidence: fiss-lint --strict . (exit code 0: clean)
   Affected paths:
   - FISS/INDEX.md
   - FISS/BOOTSTRAP.md
   - FISS/state/fiss-handoff.md
   - FISS/overrides/INDEX.md
   - FISS/overrides/handoff.md
   - AGENTS.md
   ```

---

## Example 3: Repository with External Issue Tracker (e.g. Taiga / GitHub Issues)

When initializing a project already tracked in Taiga:

1. **`FISS/state/fiss-handoff.md` links to external tracker:**
   ```markdown
   # FISS Task Handoff

   task: https://taiga.example.com/project/myproject/us/42
   task status: completed
   fiss synchronization: synchronized

   ## Outcomes Summary
   - Capture here: None (initialization only)
   - Delegate: None
   - No persistence: Routine repository bootstrap
   ```
2. FISS does NOT create a competing issue list or duplicate backlog in `state/`. It maintains only the bridge to the active canonical work item.
