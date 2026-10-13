# FISS Review: Concrete Examples

## Reading Contract

Read this file when uncertain about how to formulate two-sided evidence anchors, how to classify findings under the 6-state epistemic model, how to balance intent against implementation without kingmaking, or how to document explicit scope boundaries in report headers. Skip it during routine audit execution when formats are already clear.

---

## Example 1: Grounded Discrepancy Finding (Contract Drift)

This example shows an interface drift between an API contract documented in FISS and the default Python implementation, citing exact coordinates and verbatim snippets, without overstating production runtime certainty.

```markdown
### [REV-001] Token Expiration Window Default Inconsistency
- **Epistemic Status:** CONFIRMED_ISSUE
- **Priority:** HIGH
- **Category:** Contract Drift
- **Subject / Audited Claim or Knowledge Gap:**
  - Location: `FISS/knowledge/services/auth.md:42`
  - Statement / Invariant: `"All access tokens have an immutable TTL of 15 minutes (900 seconds) to mitigate token theft risk."`
- **Contrasting / Corroborating Evidence:**
  - Location: `src/auth/jwt.py:28`
  - Observation / Evidence: `"ACCESS_TOKEN_EXPIRE_MINUTES: int = 60  # Default extended for dev convenience"`
- **Verification Boundary & Search Surface:**
  - Searched `src/auth/jwt.py`, `src/config.py`, and Docker compose environments.
- **Divergence Analysis:**
  The implementation defines a default access token lifetime of 60 minutes instead of the documented 15 minutes. A code comment suggests this was adjusted for developer convenience. Unless overridden by environment variables in runtime deployments, access tokens will live for 60 minutes instead of the documented 15. The discrepancy in default configuration is confirmed, while actual production runtime behavior depends on deployment environment configuration.
- **Impact & Blast Radius:**
  Downstream services and client refresh-token loops relying on a 15-minute rotation window may exhibit unexpected behavior or desynchronized token refreshes.
- **Alternative Explanations & Context:**
  May be overridden in production Kubernetes secrets or `.env` files not committed to this repository.
- **Audit Limitations:**
  Production deployment secrets and active runtime tokens were not directly observed; the finding is grounded on repository configuration defaults.
- **Recommended Action Direction:**
  Confirm whether the 15-minute security invariant remains binding. If yes, update `src/auth/jwt.py` default to 15; if 60 minutes is the approved project standard, update `FISS/knowledge/services/auth.md` via `fiss-maintain`.
```

---

## Example 2: Unconfirmed Systemic Guarantee (Mechanism Present, SLA Unproven)

This example demonstrates the separation of Implementation Presence from Systemic Guarantee. The code has a cache class, but the claim of a 99.9% sub-millisecond SLA is unverified.

```markdown
### [REV-002] Unsupported Sub-Millisecond Cache Latency Guarantee
- **Epistemic Status:** UNCONFIRMED_CLAIM
- **Priority:** MEDIUM
- **Category:** Operational Guarantee
- **Subject / Audited Claim or Knowledge Gap:**
  - Location: `FISS/knowledge/architecture/caching.md:15`
  - Statement / Invariant: `"The L1 in-memory LRU cache guarantees sub-millisecond retrieval (p99.9 < 1ms) under sustained concurrency of 10,000 req/s."`
- **Contrasting / Corroborating Evidence:**
  - Location: `src/cache/lru.go:34`
  - Observation / Evidence: `"type LRUCache struct { mu sync.RWMutex; items map[string]*entry; capacity int }"`
- **Verification Boundary & Search Surface:**
  - Searched `tests/`, `benchmarks/`, and CI workflows for load or latency benchmarks. No benchmark tests exist for `LRUCache`.
- **Divergence Analysis:**
  The repository contains a standard mutex-guarded in-memory LRU cache implementation (`src/cache/lru.go`). However, no benchmark suites (`go test -bench`), load tests, or telemetry artifacts substantiate the p99.9 < 1ms guarantee under 10k req/s concurrency. Under high contention, the shared RWMutex may experience lock contention not reflected in the documentation.
- **Impact & Blast Radius:**
  Capacity planners and dependent real-time trading components may rely on an unproven latency budget, leading to unexpected tail-latency spikes under production load.
- **Alternative Explanations & Context:**
  Benchmarks may have been run in an external testbed or private environment without preserving the benchmark scripts in this repository.
- **Audit Limitations:**
  Active benchmark execution was not performed (strictly prohibited without authorization).
- **Recommended Action Direction:**
  Either check in the benchmark suite validating this SLA into `tests/benchmarks/`, or soften the FISS claim from a hard guarantee to an architectural design target.
```

---

## Example 3: Epistemic Conflict between ADR and Code (No Linear Kingmaking)

This example illustrates how to report a divergence between recorded architectural intent and current implementation without taking sides or declaring the document automatically obsolete.

```markdown
### [REV-003] Inter-Service Billing Dispatch Mechanism Divergence
- **Epistemic Status:** EPISTEMIC_CONFLICT
- **Priority:** HIGH
- **Category:** Architecture Invariant
- **Subject / Audited Claim or Knowledge Gap:**
  - Location: `FISS/knowledge/decisions/adr-005-async-billing.md:24`
  - Statement / Invariant: `"All billing event dispatching MUST be fully asynchronous via RabbitMQ topic 'billing.events' to prevent checkout request blocking."`
- **Contrasting / Corroborating Evidence:**
  - Location: `src/billing/client.py:51`
  - Observation / Evidence: `"response = requests.post(f'{BILLING_SERVICE_URL}/api/charge', json=payload, timeout=5.0)"`
- **Verification Boundary & Search Surface:**
  - Inspected `src/billing/**`, `docker-compose.yml`, and git commit history for `src/billing/client.py`.
- **Divergence Analysis:**
  ADR-005 explicitly mandates asynchronous queue-based event dispatching for billing to decouple checkout from billing outages. In direct contrast, `src/billing/client.py` implements a synchronous blocking HTTP POST call with a 5-second timeout. Git blame reveals the HTTP call was introduced 3 months after ADR-005 was accepted.
- **Competing Hypotheses:**
  - *Hypothesis A (Intentional unrecorded pivot):* The engineering team encountered RabbitMQ delivery latency and switched to synchronous HTTP for immediate checkout confirmation, neglecting to record a superseding ADR.
  - *Hypothesis B (Emergency deviation / Architectural drift):* A developer implemented a quick HTTP integration for a new billing provider without reviewing ADR-005, unintentionally re-introducing checkout blocking hazards.
- **Impact & Blast Radius:**
  If the billing service experiences downtime or network latency, checkout transactions will block for up to 5 seconds and potentially cascade into frontend timeouts.
- **Audit Limitations:**
  Meeting transcripts and PR review comments from the commit are not part of the local repository.
- **Recommended Action Direction:**
  Architectural review required. If synchronous HTTP is accepted, author `adr-012-sync-billing-migration.md` to supersede ADR-005. If ADR-005 remains binding, open an issue to refactor `src/billing/client.py` back to RabbitMQ.
```

---

## Example 4: Explicit Scope and Depth Bounding in Report Header

This example illustrates how an auditor honestly reports boundary constraints when a `Full x Deep` audit was requested, preventing silent downgrades.

```markdown
# FISS Review Report: E-Commerce Microservices Workspace

## 1. Audit Perimeter & Methodology
- **Requested Scope:** Full Project
- **Requested Depth:** Deep Audit
- **Audited Scope (Deeply Inspected):**
  - `FISS/knowledge/services/auth.md` $\longleftrightarrow$ `src/auth/**`, `tests/auth/**`
  - `FISS/knowledge/services/billing.md` $\longleftrightarrow$ `src/billing/**`, `tests/billing/**`
  - `FISS/knowledge/architecture/caching.md` $\longleftrightarrow$ `src/cache/**`
- **Surveyed Scope (Surface Inspected):**
  - `FISS/knowledge/services/inventory.md` $\longleftrightarrow$ Verified endpoint declarations and high-level DTOs; internal database transaction logic was not deeply traced.
  - `FISS/knowledge/services/shipping.md` $\longleftrightarrow$ Scanned interface definitions; carrier webhook integrations were not deeply traced.
- **Excluded / Inaccessible Scope:**
  - `src/legacy_crawler/` (excluded: explicitly deprecated subsystem marked for deletion).
  - Production monitoring infrastructure (inaccessible: Datadog / Prometheus telemetry metrics not present in local workspace).
- **Mechanical Baseline:**
  - `fiss-lint`: Executed; 0 errors, 0 warnings (clean mechanical baseline within implemented rule set).
```

---

## Example 5: Adversarial Review Disproving a False Positive and Epistemic Decomposition

This example illustrates how Workflow Stage 4 (Adversarial Review) challenges a preliminary finding, avoids a false alarm caused by dynamic handler registration, and decomposes the audited claim into distinct static vs runtime dimensions with explicit status accounting.

```text
[PRELIMINARY SUSPICION]:
Auditor searched with `grep -rn "def on_payment_success"` across `src/` to verify
FISS claim in `FISS/knowledge/events.md:18`:
"Payment success events trigger automatic inventory reservation."
Search yielded 0 matches. Auditor was tempted to flag:
"CONFIRMED_ISSUE: Payment success handler is completely missing."

[ADVERSARIAL CHALLENGE]:
1. Did I check decorator-based dynamic registration?
2. Did I check class-based subscriber interfaces or enum event identifiers?

[COUNTER-EVIDENCE DISCOVERY]:
Auditor ran `grep -rn "@event_subscriber" src/` and found:
`src/inventory/listeners.py:33`:
    @event_subscriber(EventType.PAYMENT_COMPLETED)
    def reserve_stock(event: PaymentCompletedEvent):
        ...

[ADVERSARIAL RESOLUTION & EPISTEMIC DECOMPOSITION]:
The function name is `reserve_stock`, registered via enum constant `EventType.PAYMENT_COMPLETED`.
The preliminary finding alleging complete absence of the handler is disproven as a FALSE POSITIVE.

Crucial distinction: Discovering the static registration does NOT prove runtime behavioral execution.
The high-level FISS statement is decomposed into two distinct verifiable claims:

Claim 5.1 (Static Component Presence & Wiring):
- Statement: Dedicated handler exists and registers for the payment completion event.
- Grounding: Verified via AST / code inspection at `src/inventory/listeners.py:33`.
- Epistemic Status: NO_DEFECT_DETECTED
- Accounting: Aggregated into Executive Summary under "Verified Claims / No Defect Detected (+1)".
  No individual finding card emitted (Lean Reporting default).

Claim 5.2 (Runtime Execution Guarantee):
- Statement: Emitted payment events reliably trigger reservation execution end-to-end at runtime.
- Grounding: Unobserved. No integration test exercises the event bus dispatch pipeline,
  and message broker runtime telemetry is inaccessible in the local workspace.
- Epistemic Status: AUDIT_GAP
- Accounting: Emitted as finding card [REV-003] and recorded under "Audit Gaps (+1)".

[EXECUTIVE SUMMARY ACCOUNTING EFFECT]:
------------------------------------------------------------
## 2. Executive Summary
- Total Claims Evaluated: 2
- Confirmed Issues: 0
- Probable Issues: 0
- Unconfirmed Claims: 0
- Epistemic Conflicts: 0
- Audit Gaps: 1                           <-- Claim 5.2
- Verified Claims / No Defect Detected: 1  <-- Claim 5.1
*(Note: Sum: 0 + 0 + 0 + 0 + 1 + 1 = 2 == Total Claims Evaluated)*
------------------------------------------------------------
Result: False positive is avoided without granting false confidence in unverified runtime dynamics.
```

---

## Example 6: Knowledge Necessity Assessment (Lean Knowledge)

This example shows the contrast between rejecting a trivial documentation complaint and identifying a genuine material knowledge gap.

```text
Scenario A: Trivial Code (REJECTED AS FINDING)
Code: `src/utils/formatting.ts` contains `export function formatCurrency(amount: number): string`.
FISS: Contains no mention of `formatCurrency` in any document.
Auditor Analysis:
- Does this code involve cross-service invariants? No.
- Does it embody non-obvious security, concurrency, or performance trade-offs? No.
- Is it self-describing standard library wrapper? Yes.
Verdict: Lean Knowledge applies. Discard without reporting. Omission is correct.

Scenario B: Non-Obvious Invariant (CONFIRMED FINDING)
Code: `src/db/connection.ts` sets `max_connections: 5`, with an inline comment:
"Must never exceed 5 due to legacy Postgres connection pool starvation on shared RDS instance."
FISS: `FISS/knowledge/infrastructure/database.md` makes no mention of the 5-connection limit and advises scaling connection pool size to 50 for high throughput.
Auditor Analysis:
- Is this an external constraint and non-obvious hazard? Yes.
- Could an engineer or agent cause an outage if guided by FISS? Yes.
Verdict: Material Knowledge Gap / Confirmed Discrepancy. Emit Finding with HIGH priority.
```

---

## Example 7: Contract Drift vs. Dynamic Validation (Go Version Discrepancy)

This example illustrates how to classify a manifest-level contradiction when dynamic execution (compilation) is unavailable, distinguishing between proven contract drift and empirical runtime compatibility.

```text
[AUDITED CLAIM]:
FISS claim in `FISS/knowledge/project/architecture.md:4`:
"Language: Go (1.22+)."
FISS claim in `FISS/knowledge/project/workflow.md:56` and `README.md:56`:
"If Go 1.21 or later is installed on your system:"

[CONTRASTING EVIDENCE]:
Project manifest in `go.mod:3`:
"go 1.23.1"

[NORMATIVE SPECIFICATION CONTEXT]:
Per official Go module reference (https://go.dev/doc/modules/gomod-ref):
Starting with Go 1.21, the `go` directive in `go.mod` specifies a mandatory minimum required Go version.
Toolchains running Go 1.21 or Go 1.22 will refuse to compile a module specifying `go 1.23.1` unless
configured to download a newer toolchain.

[EPISTEMIC CLASSIFICATION]:
- Erroneous Demotion Trap: "The Go compiler is not installed in the audit environment, so I cannot run `go build` to verify whether it fails with Go 1.21; therefore, I must classify this as PROBABLE_ISSUE." (WRONG)
- Correct Classification: CONFIRMED_ISSUE (Category: Contract Drift).
  The contradiction between the stated documentation requirements ("Go 1.21+", "Go 1.22+") and the declarative manifest requirement (`go 1.23.1`) is an indisputable, static fact under normative Go language rules. Unavailability of dynamic compilation does NOT demote proven contract drift.

[AUDIT LIMITATION DISCLOSURE]:
"Audit Limitation: Unable to compile codebase with Go 1.21/1.22 toolchains to determine whether source code empirically uses 1.23-exclusive standard library APIs. The contract discrepancy between go.mod and documentation is confirmed regardless of source-level compatibility."
```

---

## Example 8: Direct Evidence Requirement for NO_DEFECT_DETECTED vs. AUDIT_GAP

This example illustrates the strict direct evidence precondition required before declaring that no defect was detected.

```text
[AUDITED CLAIM]:
Rule `FISS-R010`: "If AGENTS.md exists in the project root, it MUST direct agents to FISS/INDEX.md without duplicating space content."

[EVIDENTIARY SCENARIOS]:

Scenario A: Phantom Verification (EPISTEMIC DEFECT)
- Observation: Auditor sees `AGENTS.md` listed in `ls` or environment summary.
- Observation: Auditor inspects `internal/linter/agents.go` and confirms the linter has code checking `AGENTS.md`.
- Action: Auditor never executes a read tool on `AGENTS.md`.
- Erroneous Verdict: NO_DEFECT_DETECTED ("AGENTS.md exists and linter rule is implemented, so requirement is satisfied.")
- Why it is Fatal: Stamping compliance without reading content is an epistemic hallucination. The presence of a file or code checking it does NOT prove the file's content conforms to the rule.
- Correct Verdict: AUDIT_GAP ("AGENTS.md exists in repository, but its actual content was not inspected; compliance unverified.")

Scenario B: Legitimate Verification (PROVEN COMPLIANCE)
- Observation: Auditor explicitly opens and reads `/path/to/AGENTS.md`.
- Observation: Verbatim text confirms: "See \[FISS/INDEX.md\](FISS/INDEX.md) for architectural knowledge."
- Correct Verdict: NO_DEFECT_DETECTED. Content was directly read, evaluated against rule criteria, and proven compliant.
```

---

## Example 9: Command Side-Effect Classification and Operator Guidance

This example illustrates how to classify command execution side effects and structure follow-up recommendations without violating the read-only contract.

```text
[UNAUDITED RUNTIME VERIFICATION NEED]:
Auditor notes that cross-compilation targets (`make build-all`) and unit tests (`make test`) were not executed.

[COMMAND CLASSIFICATION]:
1. `make clean`: Mutating / State-Altering (deletes build directories and files from disk).
2. `make build-all`: Mutating / State-Altering (generates binary executables, writes build cache).
3. `make test-install`: Mutating / State-Altering (executes install shell script, may write to system/user paths).
4. `bin/fiss-lint . > audit.json`: Mutating / State-Altering (shell redirection writes/truncates file in repo).

[REPORT SECTION 4 STRUCTURING]:

Incorrect Guidance (VIOLATES CONTRACT):
"Safe Actions for Operator Authorization (Read-Only Verification):
 make clean && make build-all && make test && make test-install
 bin/fiss-lint --strict --format json . > fiss-lint-audit.json"
(Fatal: Calls state-altering, file-deleting, and file-creating commands 'read-only' in the audited repo).

Correct Guidance (RESPECTS CONTRACT):
"Recommended Follow-Up Operator Actions (Requires Isolated Workspace):
 The following verification actions alter filesystem state and MUST NOT be executed directly in the audited repository:
 1. Cross-compilation & test suite execution:
    Run in an isolated environment (e.g. temporary container or disposable git clone):
    `make clean && make build-all && make test && make test-install`
    (Side effects: removes old artifacts, compiles 6 platform binaries, executes install scripts).
 2. Linter baseline execution:
    Execute against standard output:
    `bin/fiss-lint --strict --format json .`
    (Do NOT redirect with `> audit.json` into repository to avoid creating untracked files)."
```
