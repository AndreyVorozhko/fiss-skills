# Design Model

## Responsibility

`fiss-maintain` is an operational continuity mechanism. A completed task can leave a valid diff while losing durable context, current state, a navigation edge, or the location of an operational artifact. The skill therefore treats task completion as a possible synchronization event and keeps two outcomes separate:

1. **Synchronization completeness**: every durable result is captured, delegated, or explicitly classified as not requiring persistence.
2. **FISS conformance**: the affected space still satisfies applicable FISS requirements.

Conformance alone cannot prove continuity. A space can be structurally valid and still omit the result that the next task needs.

## Input to result model

```text
task result
  -> establish repository/FISS/task baseline
  -> capture and classify durable context
  -> identify FISS impact and affected invariants
  -> preserve-first decision
  -> minimal integration mutation
  -> scoped verification
  -> synchronization and conformance status
  -> blocking transition gate
  -> next-task handoff
```

The baseline is read-only evidence. Decisions explicitly connect evidence to a mutation or to a no-op. Mutation is the smallest change that restores the affected invariant or places the captured context.

## Ownership boundaries

Specialized skills own semantic content such as ADRs, risks, open questions, and subject knowledge. `fiss-maintain` owns where that content is integrated, how it is navigated, what its read conditions are, and whether its canonical or derived status is clear. New knowledge is canonical by default; a representation based on existing knowledge uses `Derived from:` with direct source links. `fiss-lint` owns all deterministic AST and graph verification (links, anchors, reachability, syntax). `fiss-maintain` invokes `fiss-lint --strict` as its mechanical verification engine rather than implementing ad-hoc parsers. `fiss-validate` owns comprehensive semantic answers to “does the existing space conform”; `fiss-maintain` invokes or consumes its reports for broad topology migrations but does not become a duplicate validator.

## Modes and freedom

Initialization is a narrow bridge because it creates entry-point invariants. It creates only `FISS/INDEX.md` and `FISS/BOOTSTRAP.md`, then discovers the task workflow, identifies canonical task/state sources, and resolves the logical handoff representation. If no project mechanism or repository convention resolves a location, the default is `FISS/state/fiss-handoff.md`; `FISS/state/` is not otherwise required. It ensures the required entry context communicates the standard state transitions. Crucially, if the user or project has not specified a custom Handoff Gate & Intellectual Space Refresh mechanism, initialization autonomously establishes a standard, robust 7-point audit checklist adapted to project context (in workflow documentation, overrides, or agent entry context) to satisfy FISS Principle 6 (Continuous: timely capturing and preserving useful context changes), scaffolds the handoff artifact with the three outcome classes, and explicitly warns the user in the initialization report. It does not pre-create every conventional area or migrate the repository wholesale.

Maintenance is a guided corridor operating strictly within the established project workflow and handoff gate; it does not re-inject or alter workflow mechanisms during routine tasks. It permits evidence-backed mechanical changes and scoped repairs, while requiring a plan or human decision for destructive, semantic, or topology-changing operations. The skill does not impose hard line-count thresholds: fragmentation follows applicability, lifecycle, consumers, and navigation needs.

## Preservation-first

Structural maintenance starts with what must survive: knowledge, historical meaning, links, read conditions, canonicality, applicability, relationships, and reachability. `Expand -> Migrate -> Contract` makes intermediate states inspectable and assigns consumer migration to the initiator. This prevents a new structure from being created while old navigation silently remains broken.

Canonicality is not assigned by area. One source owns particular knowledge, while every intentional summary, map, diagram, or other derived representation identifies all material sources through the single marker `Derived from:`. `human/knowledge` may therefore contain canonical knowledge, while `human/hmm` always contains derived Human Mental Model representations. A canonical-source change creates a review edge to its derived materials; it does not authorize automatic semantic rewriting.

## Handoff risk

`synchronized`, `pending`, and `unresolved` are FISS synchronization states, not task statuses. The logical handoff may use an existing canonical state mechanism or one resolved operational artifact; `FISS/state/fiss-handoff.md` is only the default representation when no project path or convention applies. Task identity and status remain with their canonical source.

To prevent silent working-tree drift, loss of state across branch context, or unobservable transitions, the transition gate operates through two distinct, observable Git commits:
1. **Gate Closure Commit:** Before any substantive task code or documentation is authored, the transition from `synchronized` to `pending` is committed to Git. This prevents uncommitted state drift in working directories and provides an immutable timeline of when a task assumed control of the space. Leaving `pending` uncommitted across task execution is prohibited.
2. **Gate Opening Commit:** Upon task completion, verified conformance (`fiss-lint --strict`), and durable context refresh (7-point audit), the transition from `pending` to `synchronized` is committed to Git, opening the gate for subsequent tasks.

The identified work may continue while `pending`; different independent work is blocked by `pending` or `unresolved`. Completion returns to `synchronized` only after FISS-relevant outcomes are accounted for. The standard agent fallback requires no hooks or CI, and the skill does not manufacture a competing registry.

## Validation scope

Validation follows affected invariants and is anchored in compiled verification:
- **Mechanical Verification**: Post-mutation verification executes `fiss-lint --strict <workspace>`. An exit code of 0 provides conclusive evidence that all link targets resolve, composite indexes exist, read conditions are syntactically present, reachability is unbroken, and derivation markers conform to the standard. Any non-zero exit code halts the handoff until the broken invariants are repaired.
- **Semantic Review**: For significant migrations or topology restructuring, `fiss-maintain` invokes `fiss-validate` for deep qualitative 7C audit (checking situational trigger clarity, absence of content leakage, classified domain separation, and canonical conflict resolution).
- Drift is a signal for triage, not an automatic edit. Fresh evidence from `fiss-lint` or `fiss-validate` is mandatory before completion claims.
