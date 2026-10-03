# Provenance and Design Decisions

## Status

- `fiss-lint` CLI delegation and autonomous provisioning: `passed` by document review. All deterministic post-mutation verification is delegated to `fiss-lint --strict`, eliminating ad-hoc manual link checks and syntax scrapers.
- Principle 6 Continuity Mechanism update: static/design validation `passed`. Aligned `fiss-maintain` with FISS Principle 6 (Continuous: timely capturing and preserving useful context changes) by autonomously establishing a standard 7-point audit checklist adapted to project context when initializing new intellectual spaces where no custom refresh mechanism is specified.
- Current FISS Handoff Protocol update: static/design validation `passed`; behavioral validation `not run` at the user's request. The change aligns task-start behavior, synchronization states, logical artifact resolution, and project-mechanism independence with the normative specification.
- Static/design validation: `passed` for the canonical-by-default model, exact `Derived from:` contract, `human/hmm` migration, source-change review behavior, and absence of temporary-source references.
- Behavioral validation: `passed` after a focused independent re-probe. The first probe selected the correct sources and derivation model but produced an unverified relative-path example; after the contract explicitly required resolving links from the derived material's actual directory, the re-probe produced and checked the correct paths.
- The source material for this design was the FISS specification and the repository/web research supplied for this task. Temporary task files are not runtime or provenance sources.

## Provenance format

Each entry uses `Concept -> Source -> Adaptation -> Rationale -> Decision`. GitHub links point to the permanent upstream skill or reference that supplied the idea. FISS-native requirements point to the published standard.

### FISS-native continuity and integrity

- **Concept** -> canonical sources, external mechanism independence, root/index/bootstrap invariants, read conditions, reachability, overrides, operational-artifact resolution, and agent entry point.
- **Source** -> [FISS specification](https://fiss.vorozhko.ru/llms.txt) and [FISS documentation](https://fiss.vorozhko.ru/en/).
- **Adaptation** -> These are treated as the normative boundary of the skill. The skill adds a task handoff contract without redefining FISS lifecycle, tracker, skill format, or storage conventions.
- **Rationale** -> FISS defines the space and its invariants, while continuity requires checking what task results survive between tasks.
- **Decision** -> Implemented in `SKILL.md` as the primary responsibility, invariants, handoff states, and ownership boundary.

### Baseline and repository policy gate

- **Concept** -> establish repository baseline, read policy, isolate user changes, and discover validation.
- **Source** -> [agentic-awesome-skills repo-maintainer](https://github.com/sickn33/agentic-awesome-skills/blob/main/skills/repo-maintainer/SKILL.md).
- **Adaptation** -> Add FISS root/index/bootstrap, applicable overrides, task state, and operational-artifact resolution to the baseline.
- **Rationale** -> Mutation without a known baseline can overwrite user work or violate project policy.
- **Decision** -> Adopted as `Establish`; baseline collection is read-only before mutation.

### Evidence, decision, mutation separation

- **Concept** -> independent evidence collection followed by a prioritized decision set and authorized repairs.
- **Source** -> [repo-maintainer](https://github.com/sickn33/agentic-awesome-skills/blob/main/skills/repo-maintainer/SKILL.md).
- **Adaptation** -> FISS audit lanes become topology/navigation, links/read conditions, canonicality, overrides, and operational artifacts.
- **Rationale** -> Separating observation from editing limits scope creep and inconsistent cascades.
- **Decision** -> Adopted in `Analyze before mutation`.

### Expand, migrate, contract and churn ownership

- **Concept** -> additive structural migration followed by consumer migration and delayed removal; the initiator owns consumer migration.
- **Source** -> [deprecation-and-migration](https://github.com/addyosmani/agent-skills/blob/main/skills/deprecation-and-migration/SKILL.md).
- **Adaptation** -> Applied to FISS single-file to composite-area transitions, including preservation of `read condition` and zero old-path references before contract.
- **Rationale** -> FISS requires incoming links to move to a composite area's new index.
- **Decision** -> Adopted as a mandatory migration gate in `SKILL.md`.

### Superseding decisions

- **Concept** -> preserve accepted historical decisions and supersede them instead of rewriting their meaning.
- **Source** -> [documentation-and-adrs](https://github.com/addyosmani/agent-skills/blob/main/skills/documentation-and-adrs/SKILL.md).
- **Adaptation** -> Applied to FISS durable knowledge and project decisions; factual corrections remain possible when historical meaning is preserved.
- **Rationale** -> Current state and historical rationale are different information classes.
- **Decision** -> Adopted; no universal append-only filesystem rule is imposed where the project has another decision lifecycle.

### Chesterton's Fence and preservation-first

- **Concept** -> understand the reason for an apparently redundant structure before simplifying it.
- **Source** -> [code-simplification](https://github.com/addyosmani/agent-skills/blob/main/skills/code-simplification/SKILL.md).
- **Adaptation** -> Require history and incoming-reference inspection before deletion, merge, index changes, or override changes; make preservation explicit before change.
- **Rationale** -> Unexplained structures may encode applicability boundaries, hidden constraints, or historical context.
- **Decision** -> Adopted as the `Preservation-first record` and a stop condition for unclear destructive changes.

### Graph-based validation

- **Concept** -> classify orphan, missing, broken, and isolated graph defects without destructive auto-resolution.
- **Source** -> [mini-context-graph lint reference](https://github.com/github/awesome-copilot/blob/main/skills/mini-context-graph/references/lint.md).
- **Adaptation** -> Add FISS-specific checks for missing read conditions, composite indexes, reachable children, canonical-source markings, and backlinks; scope checks to affected invariants.
- **Rationale** -> Graph defects threaten context discoverability, but detection does not establish the correct semantic repair.
- **Decision** -> Adopted as targeted verification; full graph audit is reserved for topology changes or explicit requests.

### Scoped post-edit validation

- **Concept** -> validate edited files and their immediate link boundaries after mutation.
- **Source** -> [fix-broken-links hook](https://github.com/github/awesome-copilot/tree/main/hooks/fix-broken-links).
- **Adaptation** -> Validate relative Markdown paths, anchors, backlinks, and FISS read conditions rather than assuming HTTP-link checks are sufficient.
- **Rationale** -> Local checks reduce cost while catching the blast radius of a local edit.
- **Decision** -> Adopted with affected-invariant selection; no global scan after every atomic edit.

### Dependency-aware work packets

- **Concept** -> phased packets with owned files, preserved invariants, and intermediate validation.
- **Source** -> [orchestrate-batch-refactor](https://github.com/sickn33/agentic-awesome-skills/blob/main/skills/orchestrate-batch-refactor/SKILL.md).
- **Adaptation** -> Require a plan only for batch, destructive, or topology-changing operations and include FISS reachability, read conditions, and canonicality.
- **Rationale** -> Large migrations need explicit boundaries and recovery points; ordinary edits should not carry that bureaucracy.
- **Decision** -> Adopted conditionally in `Maintain`.

### Fresh verification before completion

- **Concept** -> no completion claim without a fresh command/result matched to the claim.
- **Source** -> [verification-before-completion](https://github.com/obra/superpowers/blob/main/skills/verification-before-completion/SKILL.md).
- **Adaptation** -> Require separate synchronization and conformance statuses plus the evidence paths and outputs.
- **Rationale** -> A valid-looking diff is not proof that links, reachability, or handoff completeness hold.
- **Decision** -> Adopted as a critical invariant and final reporting contract.

### Canonical source authority and conflict surfacing

- **Concept** -> apply an established source hierarchy; surface unresolved conflicts instead of choosing silently.
- **Source** -> [source-driven-development](https://github.com/addyosmani/agent-skills/blob/main/skills/source-driven-development/SKILL.md).
- **Adaptation** -> FISS source rules take precedence. Knowledge is canonical by default; intentional secondary representations use the exact marker `Derived from:` with direct links to all material sources. Absent a project rule for competing sources, emit `CONFLICT DETECTED` and escalate.
- **Rationale** -> Silent reconciliation creates an untraceable competing truth.
- **Decision** -> Adopted for knowledge, derived representations including `human/hmm`, external systems, current state, and overrides. `FISS/state/` receives no separate derivation model.

### Canonical-by-default knowledge and HMM

- **Concept** -> one canonical source per particular knowledge item, derivation only by explicit `Derived from:`, and Human Mental Model representations in `human/hmm`.
- **Source** -> [FISS specification](https://fiss.vorozhko.ru/llms.txt).
- **Adaptation** -> Maintenance classifies new knowledge as canonical by default, marks summaries, restatements, maps, diagrams, and other derived representations with direct source links, and treats source changes as review triggers for related derived material.
- **Rationale** -> A single mechanism prevents independent duplicate maintenance without assigning canonical status to directories.
- **Decision** -> Implemented in the invariants, capture classification, pre-mutation analysis, and verification contract. HMM remains an application of the general derived-knowledge rule rather than a separate management workflow.

### Documentation drift

- **Concept** -> timestamp/interface drift as evidence that documentation may need review.
- **Source** -> [gc-templates](https://github.com/sickn33/agentic-awesome-skills/blob/main/skills/ecl-harness-engineer/references/gc-templates.md).
- **Adaptation** -> Treat drift as advisory triage only; do not rewrite FISS because a source is old or code changed.
- **Rationale** -> Age and implementation churn are signals, not proof that durable knowledge changed.
- **Decision** -> Adopted as a non-mutating signal in verification.

### Traceability and link preservation

- **Concept** -> preserve useful relationships while recognizing that formal link systems have maintenance cost.
- **Source** -> [Traceability in software engineering](https://arxiv.org/abs/2108.02133), [Maintaining traceability links during software evolution](https://arxiv.org/abs/1807.06684), and [Google cross-references guidance](https://developers.google.com/style/cross-references).
- **Adaptation** -> Use ordinary FISS Markdown links, indexes, read conditions, and backlink checks; do not introduce a typed relationship language.
- **Rationale** -> FISS explicitly treats concise Markdown relationships as sufficient.
- **Decision** -> Adopted as preservation of existing navigation, not as a new metadata registry.

### Small safe transformations

- **Concept** -> prefer small, testable transformations and minimal diffs.
- **Source** -> [Refactoring by Martin Fowler](https://www.martinfowler.com/books/refactoring.html).
- **Adaptation** -> Apply to FISS content, navigation, and structure while allowing a planned packet for changes whose dependencies require several files.
- **Rationale** -> Smaller changes reduce blast radius and make evidence easier to attribute.
- **Decision** -> Adopted as minimal mutation; global regeneration is rejected.

### Task handoff synchronization

- **Concept** -> a completed task must explicitly account for durable outcomes before the next task.
- **Source** -> [FISS preservation principle](https://fiss.vorozhko.ru/en/intellectual-space) plus the adapted continuity synthesis in the task design.
- **Adaptation** -> Operationalize `Capture here / Delegate / No persistence`, a resolved logical handoff artifact, and a blocking default transition gate without imposing a project-specific task-state path. The skill default is `FISS/state/fiss-handoff.md`; external trackers retain task identity and task status, while the handoff artifact records the FISS synchronization state.
- **Rationale** -> FISS preserves discoverable useful context but does not define a universal task lifecycle.
- **Decision** -> This was the initial synthesis, implemented in `Establish`, `Initialize`, `Capture and handoff`, `Verify and hand off`, and the explicit transition gate. The fixed-path and mandatory-bootstrap-enforcement implications were superseded by the FISS Handoff Protocol alignment below: standard agent fallback is sufficient, while project mechanisms remain optional.

### FISS Handoff Protocol alignment

- **Concept** -> Standardize FISS synchronization semantics without imposing a fixed artifact path, task lifecycle, or enforcement infrastructure.
- **Source** -> [FISS specification](https://fiss.vorozhko.ru/llms.txt), FISS Handoff Protocol.
- **Adaptation** -> The skill now requires a new independent work item from `synchronized` to be recorded as `pending` before substantive work; continuation is allowed for the identified current work. The logical handoff can use an existing canonical mechanism, with `FISS/state/fiss-handoff.md` only as a fallback location. Task identity/status remain external where externally canonical.
- **Rationale** -> The prior contract checked the preceding state but did not require pre-registering the next work, and overstated the need for a dedicated artifact and bootstrap enforcement.
- **Decision** -> Updated the task-start gate, initialization, state model, and capture flow. No hook, CI, VCS, or task tracker is required. Static/design validation passed; behavioral validation was not run at the user's request.

### Autonomous establishment of Principle 6 continuity mechanism during initialization

- **Concept** -> Ensure that newly initialized intellectual spaces actively satisfy FISS Principle 6 (Continuous context synchronization) by introducing an operational handoff gate and refresh protocol if the user or project has not defined one.
- **Source** -> FISS Principle 6 (Continuous: timely capturing and preserving useful context changes), user requirements.
- **Adaptation** -> During `Initialize` mode only, if no custom handoff gate or refresh protocol is specified, `fiss-maintain` autonomously establishes the standard 7-point audit checklist (subject knowledge, project knowledge, ADRs, risks, open questions, subject terms, project terms) adapted to project context (workflow documentation, overrides, or agent entry context), scaffolds the handoff template with outcome classifications, and explicitly notifies the user in the initialization report. In routine `Maintain` mode, the skill operates strictly within the established workflow without altering project policies or re-injecting mechanisms.
- **Rationale** -> Without an explicit refresh mechanism established at initialization, context changes easily get lost between tasks, leading to semantic drift and violating Principle 6. Applying this only during initialization avoids unnecessary churn or policy mutation during routine tasks.
- **Decision** -> Adopted as Principle 6 Initialization Invariant, Initialize Step 6, Maintain boundary restriction, and updated reporting template.

### Delegation of deterministic post-mutation verification to fiss-lint

- **Concept** -> Replace manual file reading, link extraction regexes, and graph traversals with compiled, zero-token CLI verification before task completion.
- **Source** -> User request (User Story #43), compiler-level verification pattern ([fiss-lint](https://github.com/avorozhko/fiss-lint)).
- **Adaptation** -> `fiss-maintain` discovers or installs `fiss-lint` and executes `fiss-lint --strict <workspace>` during the `Verify and hand off` stage. Manual link-checking loops and syntax validation were removed from the skill.
- **Rationale** -> Compiled verification is instant, deterministic, eliminates LLM hallucinations on file existence and markdown edge cases, and provides verifiable proof (exit code 0) before declaring `fiss synchronization: synchronized`.
- **Decision** -> Adopted in `SKILL.md` (Establish, Invariants, Verify and hand off), `DESIGN.md` (Ownership, Validation scope), and `EXAMPLES.md` (Verification report).

## Rejected or constrained candidates

- Manual link checking and syntax parsing during maintenance when `fiss-lint` is available was rejected because compiled execution is faster, immune to LLM hallucination, and costs zero tokens.
- A mandatory fixed path `FISS/state/currenttask.md` was rejected because FISS leaves task lifecycle and operational paths external and because an external canonical tracker must not gain a competitor. A logical handoff artifact with the default location `FISS/state/fiss-handoff.md` was adopted instead, resolved through FISS operational-artifact precedence; the project may explicitly choose another location.
- A permissive default where a different independent task may proceed from `pending` or `unresolved` based only on low risk was rejected. The identified work may continue from `pending`; another independent work item remains blocked until synchronization or required resolution.
- A third `Gate` runtime mode was rejected. The skill defines and initializes the transition contract, while the project's required entry workflow applies it before ordinary work. This avoids making `fiss-maintain` an implicit orchestrator for every unrelated task.
- Programmatic enforcement as a prerequisite was rejected. The standard agent fallback is sufficient; hooks, CI, and wrappers remain optional project mechanisms.
- Injecting or modifying the Principle 6 continuity mechanism on every routine task was rejected because routine maintenance must not mutate project workflows or impose unexpected procedural overhead; autonomous introduction is strictly constrained to the `Initialize` mode when setting up a new intellectual space.
- Fixed line-count fragmentation thresholds were rejected in favor of applicability, lifecycle, consumers, and navigation signals.
- Full graph audits after every edit, blind regeneration, automatic orphan deletion, and automatic canonical conflict resolution were rejected as unsafe or unnecessarily expensive.
- A permanent journal for every maintenance decision was rejected; only significant decisions need durable logging in the project's chosen owner.
