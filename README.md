# FISS Skills

[Русский](README.ru.md) | **English**

A collection of Agent Skills for working with intellectual spaces under the [FISS (File-based Intellectual Space Standard)](https://fiss.vorozhko.ru/en/).

This repository is part of the FISS tooling ecosystem alongside the [fiss-lint](https://github.com/AndreyVorozhko/fiss-lint) static analyzer.

---

## Skills in Repository

### 1. [`fiss-maintain`](fiss-maintain/SKILL.md)
**Space operator and mutator.** Responsible for context continuity and structural integrity of the FISS space across tasks.

* **Initialization:** Scaffolds the minimal conforming FISS structure (`FISS/INDEX.md`, `BOOTSTRAP.md`, handoff protocol) when absent.
* **Integrating Changes:** Places new knowledge correctly, builds navigation indexes with situational triggers (`Read when:`), and marks derived representations (`Derived from:`).
* **Context Continuity (Handoff):** Manages task transition states (`pending` / `synchronized` / `unresolved`) between agent runs.

### 2. [`fiss-validate`](fiss-validate/SKILL.md)
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
| **`fiss-maintain`** | AI maintenance skill | **Yes** | Initializes FISS, integrates task outcomes, updates navigation, and preserves continuity |

---

## Links

* [FISS Standard](https://fiss.vorozhko.ru/en/)
* [7 Architectural Principles (7C)](https://fiss.vorozhko.ru/v1.0.0/en/intellectual-space.html#principles)
* [fiss-lint CLI](https://github.com/AndreyVorozhko/fiss-lint)
* [License (MIT)](LICENSE)
