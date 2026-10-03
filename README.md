# FISS Skills

[Русский](README.ru.md) | **English**

A collection of Agent Skills for working with intellectual spaces under the [FISS (File-based Intellectual Space Standard)](https://fiss.vorozhko.ru/en/).

This repository is part of the FISS tooling ecosystem alongside the [fiss-lint](https://github.com/AndreyVorozhko/fiss-lint) static analyzer.

---

## Skills in Repository

### 1. [`fiss-init`](fiss-init/SKILL.md)
**0-to-1 space bootstrapper.** Responsible for initializing a conforming FISS space from scratch in a repository.

* **Minimalist Root Scaffolding:** Creates `FISS/INDEX.md` and `FISS/BOOTSTRAP.md` with strict navigation links.
* **Continuity Anchoring:** Establishes the standard Principle 6 continuity mechanism (7-point context refresh audit) and the three-phase Git-committed handoff gate (`Lock` -> `Prepare` -> `Release`).
* **External Integration:** Configures agent entry points (`AGENTS.md`) and resolves the logical handoff representation without competing with external trackers.

### 2. [`fiss-maintain`](fiss-maintain/SKILL.md)
**Operational maintainer and mutator.** Responsible for Day 2 context continuity, knowledge integration, and structural integrity across ongoing task lifecycles.

* **Three-Phase Handoff Gate:** Manages `Phase 1: Lock` (closing gate via Git commit before task implementation), `Phase 2: Prepare` (7-point context refresh, `fiss-lint --strict` verification, outcome staging, and gate authorization policy check), and `Phase 3: Release` (opening gate via Git commit upon authorization).
* **Gate Authorization Policy:** Supports project-level policies via `FISS/overrides/` (e.g. human confirmation protocols like Human Review Surface) while maintaining autonomous execution by default.
* **Integrating Changes:** Places new knowledge correctly, builds navigation indexes with situational triggers (`Read when:`), and marks derived representations (`Derived from:`).

### 3. [`fiss-validate`](fiss-validate/SKILL.md)
**Strictly read-only auditor.** Verifies space conformance against the FISS specification and its [7 architectural principles (7C)](https://fiss.vorozhko.ru/v1.0.0/en/intellectual-space.html#principles).

* **Evidence-Based Audit:** Verifies reachability of used areas, consistency of canonical sources, validity of overrides (`FISS/overrides/`), and adherence to the handoff protocol.
* **Five-Status Model:** Classifies findings into granular verdicts (`VERIFIED`, `FAILED`, `UNRESOLVED`, `INSUFFICIENT_EVIDENCE`, `ADVISORY`) with exact file and line citations.
* **Tooling Delegation:** Offloads deterministic mechanical checks to the `fiss-lint` CLI, focusing on semantic evaluation.

---

## Division of Responsibility

| Tool | Role | Mutates files? | Responsibility |
|---|---|:---:|---|
| **[fiss-lint](https://github.com/AndreyVorozhko/fiss-lint)** | CLI linter | No | Deterministic mechanical checks: paths, syntax, link validity, and required markers |
| **`fiss-validate`** | AI audit skill | No | Semantic audit, context integrity, and evidence-backed verification of architectural principles |
| **`fiss-init`** | AI initialization skill | **Yes** | 0-to-1 bootstrapping: scaffolds root files, establishes entry points and Principle 6 continuity |
| **`fiss-maintain`** | AI maintenance skill | **Yes** | Day 2 maintenance: manages 3-phase handoff gate, integrates task outcomes, updates navigation |

---

## Links

* [FISS Standard](https://fiss.vorozhko.ru/en/)
* [7 Architectural Principles (7C)](https://fiss.vorozhko.ru/v1.0.0/en/intellectual-space.html#principles)
* [fiss-lint CLI](https://github.com/AndreyVorozhko/fiss-lint)
* [License (MIT)](LICENSE)
