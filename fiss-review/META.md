# Provenance and Design Decisions: fiss-review

## Current Status and Validation

- **Specification Conformance:** `passed` by architectural review against the [Agent Skills Specification](https://agentskills.io/specification) and [FISS v1.0.0 Standard](https://fiss.vorozhko.ru/en/).
- **Structural & Design Validation:** `passed`. All critical invariants reside in `SKILL.md`; frontmatter description follows What + When + Differentiator without colons; no Workflow Summary Bypass; auxiliary files declare explicit reading contracts; zero references to temporary files or session drafts.
- **Static Artifact Validation:** `passed`. Code blocks are properly balanced, Markdown links resolve to existing headings/files, and no session paths are cited.
- **Behavioral Validation:** `not run`. Desk analysis and structural inspection confirm consistency of prompt directives, but empirical behavioral probing on an independent subagent harness with dedicated FISS test fixtures was not executed in this environment. Furthermore, the read-only boundary is enforced by instructional invariant prohibiting write tool invocation, not by platform-level sandbox isolation.
- **Capability Discovery:** Capability Discovery identified Environment State C for automated behavioral probe execution: no preconfigured isolated subagent harness with dedicated FISS test fixtures is available in the local environment. Structural and static validations were executed; behavioral probe status is honestly recorded as `not run`.
- **Canonical Sources:** Grounded in permanent upstream git repositories, peer-reviewed academic papers (DOI), and published specifications. No temporary paths (`_currenttask/*`, `scratch/*`) are cited.

---

## Provenance Format

Each substantial concept follows the structured traceability chain:
$$\text{Concept} \longrightarrow \text{Source} \longrightarrow \text{Evidence} \longrightarrow \text{Adaptation} \longrightarrow \text{Rationale} \longrightarrow \text{Decision} \longrightarrow \text{Validation}$$

---

## Synthesized Decisions and Sources

### 1. Claims Extraction and Critical Double-Checking

- **Concept:** Systematic extraction of material architectural statements, contracts, and operational guarantees, followed by an adversarial verification pass to cross-check claims against reality.
- **Source:** [awesome-copilot doublecheck](https://github.com/github/awesome-copilot/blob/main/skills/doublecheck/SKILL.md)
- **Evidence:** `doublecheck` isolates claims into distinct verifiable assertions and subjects them to rigorous cross-examination to prevent confirmation bias.
- **Adaptation:** Tailored to FISS intellectual spaces. Instead of extracting all natural language sentences (which bloats context), `fiss-review` extracts only *material architectural assertions* (invariants, interface contracts, performance SLAs, configuration requirements).
- **Rationale:** LLMs easily drown in verbose prose. Extracting discrete, testable claims makes verification tractable and repeatable.
- **Decision:** Implemented as Stage 2 (Substantive Claims Extraction) and Invariant 8 in `SKILL.md`.
- **Validation:** Desk analysis of claims extraction rules in `SKILL.md` and `DESIGN.md`; empirical behavioral probe not run.

### 2. Two-Sided Grounding and Bilateral Evidence Coordinates

- **Concept:** Every identified discrepancy must cite the exact coordinate and verbatim quote on both sides (documentation claim vs codebase evidence).
- **Source:** [review-skills docs-sync-audit](https://github.com/specialone0007/review-skills) (canonical upstream) and [awesome-copilot docs-sync-audit](https://github.com/github/awesome-copilot/tree/main/skills/docs-sync-audit)
- **Evidence:** "The line you cite must literally contain the thing you name; cite both the doc statement and the code reality."
- **Adaptation:** Applied to `FISS/` vs Project Reality. Discrepancies require `FISS/<path>:<line>` with verbatim quote and `<code_path>:<line>` with verbatim quote (or bounded search scope). Generalized to support intra-documentation contradictions, unevidenced guarantees, and missing context with explicit search perimeter bounds.
- **Rationale:** Eliminates hallucinated drift. Allows human maintainers to instantly verify findings without searching manually.
- **Decision:** Implemented as Invariant 3 in `SKILL.md` and Section 3 of `DESIGN.md`.
- **Validation:** Structural schema review: report format requires exact coordinates on both sides or explicit search bounds; empirical behavioral probe not run.

### 3. Evidentiary Triad: Intention, Implementation, and Behavior

- **Concept:** Disentangling code presence from runtime behavior and separating architectural intent from current implementation state. Code presence does not prove systemic operational guarantees; tests prove only executed paths.
- **Source:** [review-skills test-gap-audit](https://github.com/specialone0007/review-skills) and [Project State Governor](https://github.com/Ghost011118/project-state-governor)
- **Evidence:** `test-gap-audit` demonstrates that existence of unit tests or classes does not equal integration assurance; `project-state-governor` formalizes the boundary between recorded intent and living implementation.
- **Adaptation:** Formulated as the Evidentiary Triad in `DESIGN.md`. A caching class in code is recognized as implementation presence, but not as proof of an operational latency SLA.
- **Rationale:** Prevents false positive verification where an auditor conflates the mere presence of a class with the satisfaction of a systemic requirement.
- **Decision:** Implemented as Invariant 5 in `SKILL.md` and Section 2 of `DESIGN.md`.
- **Validation:** Desk scenario analysis: confirmed that unbenchmarked SLA claims are classified as `UNCONFIRMED_CLAIM` rather than verified; empirical behavioral probe not run.

### 4. Independent Accounting of Supporting and Refuting Evidence (Non-Averaging)

- **Concept:** Tracking supporting and contradicting evidence along independent axes without synthesizing fake numeric confidence percentages or arithmetic averages.
- **Source:** [awesome-copilot verify-agent-action](https://github.com/github/awesome-copilot/blob/main/skills/verify-agent-action/SKILL.md)
- **Evidence:** "Track positive support and negative contradiction as independent vectors. Do not average them into a probability."
- **Adaptation:** Implemented as the discrete 6-state Epistemic Determination Model. When evidence conflicts (e.g. ADR says X, recent commit says Y), `fiss-review` outputs `EPISTEMIC_CONFLICT` with both vectors exposed, rather than an arbitrary 50% score. Verified claims are aggregated as `NO_DEFECT_DETECTED` to preserve statistical parity in summary totals.
- **Rationale:** Architectural reality is qualitative and discrete. Numeric confidence scores mask epistemic conflict and create an illusion of statistical rigor.
- **Decision:** Implemented as Invariant 4 in `SKILL.md` and Section 4 of `DESIGN.md`.
- **Validation:** Structural schema review: output template enforces discrete 6-state enum and counting parity without numeric confidence fields.

### 5. Doubt-Driven Adversarial Review and Alternative Hypotheses

- **Concept:** Mandatory adversarial second pass that actively attempts to falsify preliminary findings by searching for alternative explanations, dynamic dispatch, or accepted trade-offs.
- **Source:** [addyosmani doubt-driven-development](https://github.com/addyosmani/agent-skills/blob/main/skills/doubt-driven-development/SKILL.md)
- **Evidence:** "Doubt your first conclusion. Seek alternative explanations, uninspected configurations, or contextual trade-offs before raising a defect."
- **Adaptation:** Integrated as Workflow Stage 4 (Adversarial Review). Auditors must check whether an alleged defect is an explicitly documented trade-off, a multi-environment setting, or a dynamically loaded component.
- **Rationale:** Dramatically reduces false positive rates and prevents friction with engineering teams who made conscious design decisions.
- **Decision:** Implemented as Invariant 10 and Stage 4 in `SKILL.md`.
- **Validation:** Desk scenario analysis: verified that trade-offs in Example 3 and registration in Example 5 are evaluated without kingmaking; empirical behavioral probe not run.

### 6. Parity Verification for Stable Identifier Sets

- **Concept:** Bidirectional set difference comparison between documented entities and implementation declarations for domains with stable identifiers.
- **Source:** [awesome-copilot mcp-release-qa](https://github.com/github/awesome-copilot/blob/main/skills/mcp-release-qa/SKILL.md)
- **Evidence:** "Extract documented tool/param list and implementation declarations; compute set difference $(Doc \setminus Code)$ and $(Code \setminus Doc)$."
- **Adaptation:** Applied to FISS knowledge for API routes, environment variables, configuration parameters, and CLI commands.
- **Rationale:** Provides deterministic coverage for interface surfaces where symbolic identifiers are predictable and enumerable.
- **Decision:** Incorporated into Stage 3 of `SKILL.md` and Section 3 of `DESIGN.md`.
- **Validation:** Structural specification review: parity comparison logic defined in `SKILL.md` Stage 3 and `DESIGN.md` Section 3; empirical behavioral probe not run.

### 7. Knowledge Necessity Framework (Lean Knowledge)

- **Concept:** Absence of documentation is only a defect if the omitted knowledge is necessary for safe operation, non-obvious invariants, or cross-system integration. Trivial self-describing code does not require documentation.
- **Source:** [awesome-copilot lean-comments](https://github.com/github/awesome-copilot/blob/main/skills/lean-comments/SKILL.md)
- **Evidence:** "Do not explain what the code already says clearly. Only document why, non-obvious constraints, and domain hazards."
- **Adaptation:** Tailored to FISS intellectual spaces. `fiss-review` checks whether an undocumented component contains non-obvious invariants before flagging a gap.
- **Rationale:** Prevents auditor spam and respects the FISS principle of Compactness (Principle 1 of 7C).
- **Decision:** Implemented as Invariant 9 in `SKILL.md` and Section 6 of `DESIGN.md`.
- **Validation:** Desk scenario analysis: Example 6 verifies that self-describing code is filtered while non-obvious hazards are preserved; empirical behavioral probe not run.

### 8. Untrusted Evidence Boundary and Read-Only Enforcement

- **Concept:** Treating all workspace files as passive untrusted data to prevent prompt injection and enforcing a strictly read-only execution contract.
- **Source:** [superpowers verification-before-completion](https://github.com/obra/superpowers/blob/main/skills/verification-before-completion/SKILL.md) and [fiss-validate](https://fiss.vorozhko.ru/v1.0.0/en/intellectual-space.html)
- **Evidence:** Read-only verification preserves the integrity of audited baselines and prevents agents from mutating targets to satisfy checks.
- **Adaptation:** Implemented as Invariants 1 and 2 in `SKILL.md`. Inline doc comments attempting to skip audits are treated as reportable anomalies. The read-only constraint is an instructional behavioral invariant prohibiting write tool invocation.
- **Rationale:** Critical for security and evidentiary neutrality.
- **Decision:** Implemented as Invariants 1 and 2 in `SKILL.md`.
- **Validation:** Structural instruction audit: confirmed explicit prohibitions on write tools, safe command preconditions, and anomaly gating for prompt injections; empirical behavioral probe not run.

### 9. Independent Scope and Depth Contract with Explicit Accounting

- **Concept:** Decoupling scope (breadth) from depth (rigor) and enforcing honest accounting of audited vs surveyed vs excluded areas.
- **Source:** [review-skills docs-sync-audit](https://github.com/specialone0007/review-skills) and [superpowers verification-before-completion](https://github.com/obra/superpowers/blob/main/skills/verification-before-completion/SKILL.md)
- **Evidence:** Disclosing uninspected areas prevents false confidence from partial verification runs.
- **Adaptation:** Formalized into a 2D matrix (Scope: Full / Targeted / Contextual $\times$ Depth: Overview / Contract / Deep) with mandatory boundary sections in the report. Deep audit defined as systematic in-depth inspection rather than mathematical exhaustiveness.
- **Rationale:** Prevents models from silently downgrading a requested full deep audit to a spot-check when context limits are reached.
- **Decision:** Implemented as Invariant 7 in `SKILL.md` and Section 5 of `DESIGN.md`.
- **Validation:** Structural slot review: header slots enforce Audited, Surveyed, and Excluded scopes, preventing silent depth downgrades.

### 10. Agent Skills Package Structure and Progressive Disclosure

- **Concept:** Standards-compliant packaging with thin `SKILL.md`, separate conceptual `DESIGN.md`, provenance `META.md`, examples `EXAMPLES.md`, and traps `GOTCHAS.md`.
- **Source:** [Agent Skills Specification](https://agentskills.io/specification)
- **Evidence:** High-context agent systems perform best when operational contracts are concise and background rationale is partitioned into reference files with explicit read triggers.
- **Adaptation:** Complete file hierarchy created under `skills/fiss/fiss-review/`.
- **Rationale:** Keeps runtime token usage minimal while preserving full architectural and historical context for maintainers.
- **Decision:** Implemented across all skill package files.
- **Validation:** Structural audit against Agent Skills specification: verified file hierarchy and progressive disclosure contracts.

### 11. Empirical Architecture-Code Consistency Grounding

- **Concept:** Academic empirical findings on how architecture documentation decays in real-world software projects, why discrepancies emerge, and what evidence is required to confirm drift.
- **Source:** [Architecture consistency: State of the practice, challenges and requirements](https://doi.org/10.1007/s10664-017-9515-3) (Empirical Software Engineering, 2017)
- **Evidence:** Demonstrates that documentation drift is primarily caused by unrecorded emergency changes, gradual architectural erosion, and ambiguous ownership rather than simple developer negligence.
- **Adaptation:** `fiss-review` treats divergence as an objective architectural phenomenon requiring root-cause hypotheses (deliberate drift vs accidental erosion) rather than immediate blame.
- **Rationale:** Aligns the tool's cognitive tone with professional software engineering realities.
- **Decision:** Incorporated into Section 2 of `DESIGN.md` and Divergence Analysis in report schema.
- **Validation:** Desk review of cognitive reporting tone in `DESIGN.md` and report template.

### 12. Automated Inconsistency Detection and Cross-Validation (CASCADE)

- **Concept:** Empirical research on detecting code-document inconsistencies using cross-validation techniques and semantic grounding.
- **Source:** [CASCADE: Detecting Inconsistencies between Code and Documentation with Automatic Test Generation](https://doi.org/10.1145/3808175) (*Proceedings of the ACM on Software Engineering*, PACMSE / FSE, Vol. 3, Issue FSE, pp. 3816–3838, 2026. Authors: Tobias Kiecker, Jan Arne Sparka, Martin Reuter et al.)
- **Evidence:** Demonstrates that multi-phase cross-checking (extract claims $\to$ identify code anchors $\to$ evaluate alignment) drastically cuts false positive rates compared to single-pass text generation.
- **Adaptation:** Directly mapped to the 5-stage workflow in `SKILL.md`: Claims Extraction $\to$ Grounding & Parity $\to$ Adversarial Review.
- **Rationale:** Provides an academically validated sequence for minimizing hallucinated drift findings.
- **Decision:** Implemented as Stages 2, 3, and 4 in `SKILL.md`.
- **Validation:** Methodological alignment with multi-phase cross-validation workflow.

### 13. Mechanical Baseline Delegation (`fiss-lint`)

- **Concept:** Offloading deterministic AST and markdown link checks to compiled CLI tooling, keeping the AI skill focused on semantic reality verification.
- **Source:** [fiss-lint Repository](https://github.com/AndreyVorozhko/fiss-lint)
- **Evidence:** `fiss-lint` evaluates 18 deterministic rules (FISS-R001 through FISS-R018) via an autonomous Go CLI utility supporting `--format text|json` and `--strict`.
- **Adaptation:** `fiss-review` invokes `fiss-lint` during Stage 1 only after confirming safe, read-only CLI semantics, to establish a mechanical baseline without duplicating rules in prompt instructions. If unavailable, unverified, or failing, records baseline limitation without blocking substantive checks.
- **Rationale:** Avoids prompt bloat, saves context tokens, and maintains clean tooling boundaries.
- **Decision:** Implemented as Invariant 12 and Stage 1 in `SKILL.md`.
- **Validation:** Structural review of tool delegation table and Stage 1 invocation conditions, including safe-semantics check before execution.

### 14. FISS Intellectual Space Standard Integration

- **Concept:** Harmonization with the foundational principles of FISS v1.0.0 (Compact, Context-aware, Context-first, Classified, Canonical, Continuous, Composable) and the override mechanism (`FISS/overrides/`).
- **Source:** [FISS Specification](https://fiss.vorozhko.ru/en/)
- **Evidence:** The normative standard governing intellectual space organization in agentic workflows.
- **Adaptation:** `fiss-review` respects `FISS/overrides/` as the sole standard-defined mechanism for project adaptations within standard bounds, while strictly maintaining that overrides cannot waive safety invariants, read-only boundaries, or evidentiary rigor.
- **Rationale:** Native interoperability with the rest of the FISS ecosystem (`fiss-init`, `fiss-maintain`, `fiss-validate`).
- **Decision:** Implemented as core foundational alignment across the skill.
- **Validation:** Structural rule check: overrides integration verified against FISS specification without waiving safety invariants.

---

## Revision and Calibration History

This section records architectural refinements and calibration decisions across development iterations:

1. **Normative Overrides vs. Untrusted Evidence Boundary:**
   - *Context:* Initial drafts did not sufficiently separate standard-defined project overrides from passive documentation.
   - *Decision:* Formulated the boundary in Invariant 2 and `DESIGN.md` Section 2: `FISS/overrides/` is the sole normative mechanism to adapt project audit parameters, but cannot waive safety invariants, read-only boundaries, or evidentiary requirements. All other documents are untrusted passive evidence.

2. **Safe Tool Execution Preconditions:**
   - *Context:* Mere presence of CLI utilities could trigger unverified execution.
   - *Decision:* Codified in Invariants 11, 12, Stage 1, and `GOTCHAS.md` that availability in PATH does not authorize execution without prior verification of safe read-only CLI semantics. If unverified, the tool is not run and a limitation is reported.

3. **Generalized Evidence Grounding & Schema Alignment:**
   - *Context:* Rigid `FISS Claim` vs `Reality Evidence` pairs failed on intra-FISS document conflicts, unevidenced guarantees, and knowledge gaps.
   - *Decision:* Generalized finding slots (`Subject / Audited Claim or Knowledge Gap`, `Contrasting / Corroborating Evidence`, `Verification Boundary & Search Surface`) to handle all four evidence topologies natively.

4. **Depth Calibration & Systematic Verification:**
   - *Context:* Initial definitions of `Deep Audit` promised "exhaustive examination", which is mathematically unattainable in complex software.
   - *Decision:* Redefined `Deep Audit` as systematic in-depth verification of implementation logic, error paths, and available evidence in the target perimeter, combined with explicit boundary disclosure (Audited vs Surveyed vs Excluded).

5. **Epistemic Counting Parity (`NO_DEFECT_DETECTED`):**
   - *Context:* Summary claim counts did not account for verified claims, causing category counts to not sum to total evaluated claims.
   - *Decision:* Integrated `Verified Claims / No Defect Detected` into Executive Summary statistics, while keeping detailed finding cards omitted by default to avoid positive clutter.

6. **Bibliographical and Empirical Precision:**
   - *Context:* Previous citations contained incorrect publication dates (CASCADE) and ungrounded absolutes ("zero hallucination").
   - *Decision:* Corrected CASCADE citation to 2026 (*Proc. ACM Softw. Eng.*, DOI 10.1145/3808175), calibrated `fiss-lint` description to actual 18 rules and CLI options, and strictly separated desk analysis from empirical validation.

7. **Refinements Post Independent Peer Review:**
   - *Read-Only Enforcement Model:*
     - *Observation:* Review noted the prohibition is normative/instruction-level, not platform-level sandboxing.
     - *Analysis:* Portable agent skills operating across heterogeneous runtimes cannot guarantee OS-level capability removal. Adding platform-specific mechanisms would reduce portability across standard agent frameworks.
     - *Decision:* Clarified in `SKILL.md` (Invariant 1) and `DESIGN.md` (Section 2) that the read-only contract is enforced normatively via agent instructions and tool invocation constraints, eliminating any false impression of sandbox isolation without weakening the strict prohibition.
   - *Negative Assertion Bounding Operationalization:*
     - *Observation:* Review considered the negative assertion rule underspecified for practical execution (evaluating dynamic dispatch, aliases, codegen).
     - *Analysis:* Complete procedural checklists in `SKILL.md` would cause prompt bloat and rigidity. Concrete falsification examples already existed in `EXAMPLES.md` (Example 5) and `GOTCHAS.md` (Gotcha 4), but cross-links and progressive search heuristics were not explicit in `SKILL.md`.
     - *Decision:* Added progressive search heuristics (identifier variations, decorators/hooks, configuration dispatch, codegen outputs) and explicit cross-links to `GOTCHAS.md` and `EXAMPLES.md` directly into `SKILL.md` (Invariant 8, Stage 4).
   - *Contextual Evidence Evaluation (`EPISTEMIC_CONFLICT`):*
     - *Observation:* Review pointed out that "equally credible sources" was underspecified and lacked guidance when sources diverge.
     - *Analysis:* Introducing a rigid universal hierarchy ("code always beats docs" or "tests always beat code") violates the Evidentiary Triad and kingmaking prohibition. However, guidance was needed on evaluating evidence context without arbitrary scoring.
     - *Decision:* Formulated five contextual dimensions in `SKILL.md` (Invariant 6, Stage 4) and `DESIGN.md` (Section 2): Directness/Provenance, Scope of Affirmation, Temporal Context/Supersession, Target Environment Applicability, and Reproducibility. Un-reconciled contradictions remain `EPISTEMIC_CONFLICT` without synthetic confidence percentages.
   - *Mechanical Delegation Clarity (`fiss-lint`):*
     - *Observation:* Review noted tension between non-duplication and conditional execution of `fiss-lint`.
     - *Analysis:* `fiss-lint` is an optional compiled accelerator, not a mandatory prerequisite for substantive auditing.
     - *Decision:* Codified gap acceptance policy in `SKILL.md` (Invariant 12, Stage 1) and `DESIGN.md` (Section 9): when `fiss-lint` is unavailable or unverified, record `NOT RUN` in Section 1 of the report, do not duplicate mechanical checks manually, and proceed directly with the substantive reality audit without blocking.

8. **Empirical Calibration Post Initial Repository Audit (`fiss-lint`):**
   - *Command Side-Effect Misclassification:*
     - *Observed Behavior:* Audit report labeled `make clean && make build-all && make test && make test-install` and `bin/fiss-lint . > fiss-lint-audit.json` as "Safe Actions for Operator Authorization (Read-Only Verification)".
     - *Error Mechanism:* The model conflated non-modification of application source code with "read-only", ignoring that `make clean` deletes files, `make build` creates binaries/caches, and `>` writes files to the audited repository.
     - *Decision & Rationale:* Codified strict Command Side-Effect Classification in `SKILL.md` (Invariants 1, 11), `DESIGN.md` (Sections 2, 7), and `GOTCHAS.md` (Gotcha 11). Actively executing state-altering commands in the audited repository is prohibited. Follow-up recommendations for builds/tests must explicitly disclose mutating side effects and require execution in an **isolated workspace** (temporary container, separate temporary clone, or scratch directory).
     - *Modified Files:* `SKILL.md`, `DESIGN.md`, `GOTCHAS.md`, `EXAMPLES.md`, `META.md`.
     - *Validation & Limitations:* Consistency of instructional constraints verified across all package files; actual behavioral compliance across arbitrary LLM models requires future automated test harness probes.
   - *Unwarranted Demotion of Proven Contract Discrepancy (Go Version):*
     - *Observed Behavior:* In finding `REV-001`, the discrepancy between `go.mod` (`go 1.23.1`) and documentation (`Go 1.21+` / `Go 1.22+`) was demoted to `PROBABLE_ISSUE` because the Go compiler was unavailable to test compilation.
     - *Error Mechanism:* The model conflated declarative requirement contradictions with empirical runtime execution. Under official Go module specifications (https://go.dev/doc/modules/gomod-ref), the `go` directive specifies a mandatory minimum required version starting in Go 1.21. Stated requirements are in factual, proven contradiction regardless of whether code happens to compile on older versions.
     - *Decision & Rationale:* Formulated the Manifest & Specification Grounding rule in `SKILL.md` (Invariant 6), `DESIGN.md` (Sections 2, 7), `GOTCHAS.md` (Gotcha 12), and `EXAMPLES.md` (Example 7): unavailability of dynamic execution does not invalidate or demote findings definitively proven by normative specifications and static manifests. Statically proven contract contradictions remain `CONFIRMED_ISSUE`; residual uncertainty about empirical code compatibility is noted as an audit limitation/note without weakening the finding.
     - *Modified Files:* `SKILL.md`, `DESIGN.md`, `GOTCHAS.md`, `EXAMPLES.md`, `META.md`.
     - *Validation & Limitations:* Conceptual and prompt consistency verified; empirical validation of model classification under varied prompt contexts remains unexecuted.
   - *Phantom Verification Without Direct Inspection (`NO_DEFECT_DETECTED`):*
     - *Observed Behavior:* In finding `REV-008`, rule `FISS-R010` (directive in `AGENTS.md`) was stamped `NO_DEFECT_DETECTED` while explicitly admitting: "Actual `AGENTS.md` content not read during audit... presence per system-reminder... satisfies the requirement".
     - *Error Mechanism:* The model substituted indirect presence (file in directory tree, linter code implementing rule) for direct evidence of compliance, falsely confirming an unread file.
     - *Decision & Rationale:* Codified the Direct Evidence Precondition for `NO_DEFECT_DETECTED` in `SKILL.md` (Invariant 3, Status Table, Accounting Section, Anti-Rationalization Guard), `DESIGN.md` (Sections 4, 7), `GOTCHAS.md` (Gotcha 13), and `EXAMPLES.md` (Example 8). `NO_DEFECT_DETECTED` strictly requires direct inspection of the specific content. If a file was not read or content was uninspected, it MUST be classified as `AUDIT_GAP`. Absence of inspection is never proof of compliance.
     - *Modified Files:* `SKILL.md`, `DESIGN.md`, `GOTCHAS.md`, `EXAMPLES.md`, `META.md`.
     - *Validation & Limitations:* Instructional invariants and anti-rationalization guards verified for consistency; behavioral verification on an independent probe harness not executed.

---

## Catalog Routing Analysis & Differentiators

To guarantee zero routing collisions in the agent skills catalog:

```text
┌─────────────────┬───────────────────────────────────────────────────────────────────┐
│ Skill           │ Unique Routing Trigger & Differentiator                           │
├─────────────────┼───────────────────────────────────────────────────────────────────┤
│ fiss-lint       │ Deterministic CLI: AST, paths, link targets, marker syntax.      │
│ fiss-validate   │ Formal audit: Spec compliance, 7C principles, handoff protocol.   │
│ fiss-review     │ Substantive audit: Knowledge truth vs code, tests, and reality.   │
│ fiss-maintain   │ Mutator & continuity: Applies edits, refreshes context, handoff.  │
│ fiss-init       │ Bootstrapper: 0-to-1 initialization of new FISS space.            │
└─────────────────┴───────────────────────────────────────────────────────────────────┘
```

The `description` field of `fiss-review` strictly enforces:
- **What:** Substantively audits FISS intellectual spaces against factual reality (code, tests, configs, observable behavior).
- **When:** Used when evaluating knowledge accuracy, detecting documentation drift, identifying unverified architectural guarantees, or uncovering unstated assumptions.
- **Differentiator:** Strictly read-only, checks truth of knowledge against code reality without mutating files (unlike `fiss-maintain`) and without limiting itself to formal standard syntax (unlike `fiss-validate`).
- **Syntax Check:** No colons (`:`) within description value; no workflow summary steps.

---

## Rejected Alternatives and Design Boundaries

1. **Persistent `bugs.md` or Defect Register in FISS:**
   - *Why Considered:* Some audit skills maintain a persistent markdown issue tracker.
   - *Why Rejected:* Violates the Strict Read-Only Boundary. Adding a living issue register introduces merge conflicts, pollutes the intellectual space, and blurs boundaries with issue trackers (GitHub Issues, Jira).
2. **Autonomous Auto-Fix / Code Mutation:**
   - *Why Considered:* Natural temptation to patch discovered drift immediately.
   - *Why Rejected:* Violates evidentiary neutrality. Patching code or docs without human architectural consensus frequently codifies regressions or destroys intended designs. All mutations belong to `fiss-maintain` upon human decision.
3. **Mandatory Subagent per Finding:**
   - *Why Considered:* Spawning an independent subagent to review each individual issue.
   - *Why Rejected:* Unbounded token explosion and unportable across agent environments that lack nested subagent APIs. Optional single adversarial pass within the primary agent is sufficient and portable.
4. **Exhaustive Sentence-by-Sentence Extraction:**
   - *Why Considered:* Creating an AST of every statement in FISS.
   - *Why Rejected:* Overwhelms context with trivial noise. The Knowledge Necessity Framework focuses reasoning on material architectural invariants.
5. **Numeric Confidence Scores (e.g. 85% confidence):**
   - *Why Considered:* Common in statistical classifiers.
   - *Why Rejected:* Architecturally meaningless and deceptive. Replaced with the 6-state discrete Epistemic Determination Model.
6. **Universal Linear Source Hierarchy (Kingmaking):**
   - *Why Considered:* Automatically deciding whether code or ADR wins.
   - *Why Rejected:* Fundamentally unsound. Code may be an accidental bug; ADR may be an obsolete draft. Divergence must be presented to human authorities.
7. **Custom Deterministic Link Checking Scripts in `scripts/`:**
   - *Why Considered:* Writing python/bash scripts for link validation.
   - *Why Rejected:* Already comprehensively solved by the compiled `fiss-lint` utility. Adding redundant scripts adds maintenance debt without architectural value.
