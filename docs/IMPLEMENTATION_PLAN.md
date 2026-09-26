# Implementation Plan — AI-Configured Enterprise-Grade WMS for SMB

Status: **Stage 3 draft — pending independent review and Stage 4 design**  
Owner: Product Manager Agent  
Date: 2026-09-26  
Blueprint: `docs/PRODUCT_BLUEPRINT.md`  
Repository: `igarikg/wmsnext`

## 1. Planning constraints

This plan implements the bounded P0 from the Product Blueprint. It MUST NOT expand into a broad enterprise-WMS feature race.

Planning assumptions:
- approximately 20 hours/week of founder time;
- approximately one year to reach a client-usable paid production pilot;
- AI-assisted development with Codex / Spec Kit;
- first target is a single-site SMB e-commerce/distribution warehouse;
- reliability and inventory correctness take precedence over feature breadth;
- the customer-facing AI configurator is not P0;
- Stage-2 commercial validation was waived, so real pilot/payment evidence becomes a mandatory product-level gate.

## 2. Traceability map

| Task | Blueprint areas | Primary acceptance criteria |
| --- | --- | --- |
| T01 Constitution + repo baseline | Architecture / release gates | AC-18 |
| T02 Domain model + inventory invariants | P0-03, §9–10 | AC-01, AC-02, AC-03 |
| T03 Auth + tenant isolation | P0-01, §11 | AC-11 |
| T04 Warehouse master data | P0-02 | AC-12 |
| T05 Typed/versioned configuration | P0-12 | AC-12, AC-17 |
| T06 Receiving vertical slice | P0-04 | AC-04, AC-10 |
| T07 Putaway vertical slice | P0-05 | AC-05, AC-10 |
| T08 Outbound input + reservation | P0-06 | AC-06 |
| T09 Picking vertical slice | P0-07 | AC-07 |
| T10 Packing/completion vertical slice | P0-08 | AC-08 |
| T11 Exceptions + adjustments | P0-09 | AC-09 |
| T12 Barcode/mobile operation | P0-10 | AC-16 |
| T13 Auditability + observability | P0-15 | AC-10 |
| T14 Backup/recovery/deployment safety | P0-16 | AC-14, AC-15 |
| T15 Bounded data exchange | P0-17 | pilot readiness |
| T16 Automated UAT/regression harness | P0-14 | AC-13 |
| T17 AI-operable application boundary | P0-13 | AC-17 |
| T18 UX hardening | P0-11, §12 | AC-16 |
| T19 Security hardening | §11 | AC-11 |
| T20 Pilot readiness + live warehouse | release gates | AC-19 |
| T21 Paid pilot / payment evidence | §14–16 | AC-20 |

## 3. Implementation sequence

### Phase A — Architecture and executable warehouse core
Target: Months 1–2.

#### T01 — Constitution and repository baseline

**Task**  
Initialize Spec Kit and repository governance without changing the approved product scope.

**Expected output**
- committed Spec Kit Constitution;
- `AGENTS.md`;
- `README.md`;
- `docs/PRODUCT_BLUEPRINT.md`;
- `docs/IMPLEMENTATION_PLAN.md`;
- `docs/DECISION_LOG.md`;
- `docs/TEST_PLAN.md`;
- documented build/lint/test/run commands.

**Definition of done**
A clean clone can install dependencies and run the baseline checks.

**Escalate if**
Spec Kit-generated artifacts weaken or conflict with the Blueprint or Constitution.

---

#### T02 — Domain model and inventory invariants

**Task**  
Design and implement the smallest authoritative inventory domain capable of supporting P0.

**Required domain concepts**
- tenant;
- warehouse;
- location/bin;
- SKU/product;
- inventory quantity by location;
- reservation;
- warehouse transaction / movement;
- inbound instruction;
- outbound order/task;
- immutable identifiers and transaction correlation.

**Required invariants**
Use the ten invariants in Blueprint §9 as executable domain rules and tests.

**Definition of done**
- inventory changes cannot bypass the domain/application boundary;
- idempotency and concurrency strategy is explicit;
- unit/domain tests prove the core invariants;
- failure cases do not create partial inventory state.

**Escalate if**
The architecture requires a material product-scope decision, event sourcing, CQRS, or another major pattern whose cost/benefit is unclear.

---

#### T03 — Authentication, tenant isolation, and authorization

Implement authenticated tenant-scoped access and role boundaries before multiple customer environments are possible.

Evidence must prove:
- tenant A cannot read/write tenant B;
- unauthorized user cannot post privileged inventory adjustments;
- background jobs and APIs enforce the same tenant boundary.

Maps to AC-11.

---

#### T04 — Warehouse master data

Implement:
- one warehouse;
- zones/areas;
- bins/locations;
- SKU/product;
- barcode(s);
- base UOM;
- approved simple case-pack rule if retained after design.

Do not add lots/serials/expiry or complex packaging hierarchy.

---

#### T05 — Typed, validated, versioned configuration

Create explicit configuration schemas for the small P0 rule set.

Requirements:
- schema validation;
- version ID;
- active version;
- change history;
- actor;
- safe activation;
- comparison between current/proposed versions where practical.

This becomes the future AI-agent configuration contract.

### Phase B — Inbound production slice
Target: Months 3–4.

#### T06 — Receiving vertical slice

Build one complete receiving flow from UI/input to durable warehouse transaction.

Must include:
- happy path;
- invalid SKU/state/quantity;
- retry/idempotency;
- audit trail;
- observable failure;
- automated test fixture;
- mobile-friendly interaction.

Maps to AC-04.

---

#### T07 — Putaway vertical slice

Implement one deterministic putaway strategy and execution flow.

Do not introduce a general strategy engine beyond what the first ICP needs.

Evidence:
- suggested destination is deterministic;
- scan/confirmation prevents wrong destination where applicable;
- move is atomic;
- inventory totals remain consistent.

Maps to AC-05.

### Phase C — Outbound production slice
Target: Months 5–6.

#### T08 — Outbound order input and minimal reservation

Implement bounded order creation/import and the smallest reservation rule sufficient for picking.

Required:
- deterministic result;
- no double consumption;
- explicit shortage state;
- concurrency tests.

Maps to AC-06.

---

#### T09 — Picking vertical slice

Implement one production-grade picking workflow.

Required:
- task/order context;
- source location;
- item validation;
- quantity validation;
- short-pick exception;
- inventory/reservation consistency;
- mobile-friendly scanning.

Maps to AC-07.

---

#### T10 — Packing and completion

Implement order-content verification and explicit completion.

Do not add carrier shopping, label generation, cartonization, or TMS.

Maps to AC-08.

### Phase D — Operational safety and pilot usability
Target: Months 7–9.

#### T11 — Exceptions and controlled adjustments

Support only the exceptions required by real P0 workflows:
- receive discrepancy;
- pick short;
- damaged/missing stock;
- authorized adjustment.

Every adjustment requires a reason and audit evidence.

Maps to AC-09.

---

#### T12 — Barcode/mobile operator experience

Optimize supported flows for real warehouse handheld/mobile browser use.

Scanner/device handling must remain outside core domain logic.

No offline commitment in P0.

---

#### T13 — Auditability and observability

Implement:
- transaction correlation IDs;
- structured logs;
- operational metrics;
- error categories;
- audit-query support;
- support diagnostics sufficient to reconstruct material failures.

Raw secrets and unnecessary customer data must not enter logs/analytics.

Maps to AC-10.

---

#### T14 — Backup, restore, migrations, and safe deployment

Before real inventory:
- automated backups or equivalent managed guarantee are documented;
- restore is executed in a controlled test;
- migrations are tested;
- deployment/rollback procedure is rehearsed;
- failed deployment cannot leave inventory ambiguous.

Maps to AC-14/15.

---

#### T15 — Bounded data exchange

Implement only the data path required to run a first pilot:
- manual entry and/or CSV;
- generic documented API if needed.

Do not build customer-specific one-off integrations unless the CEO explicitly approves a pilot dependency.

### Phase E — Automated proof and AI-ready surface
Target: Months 8–10, overlaps D.

#### T16 — Automated UAT and regression harness

Create deterministic scenario harnesses that can:
1. create known initial state;
2. execute warehouse commands/workflows;
3. inspect intermediate/final state;
4. return machine-readable PASS/FAIL evidence.

Mandatory scenarios:
- Receiving → Putaway → Inventory Verification;
- Order → Reservation → Picking → Packing → Inventory Verification;
- retry/idempotency;
- concurrency conflict;
- invalid location/SKU/quantity;
- failure/recovery without partial state.

Bug-fix policy:
material production defects should receive a regression scenario when practical.

Maps to AC-13.

---

#### T17 — AI-operable application boundary

Without building a customer-facing AI assistant, ensure the application layer exposes structured capabilities for future authorized agents:
- state/query operations;
- typed commands;
- configuration schemas;
- validation;
- simulation/test trigger;
- result verification;
- audit evidence.

Protocol adapters (including MCP) must call this boundary, not the database directly.

Maps to AC-17.

### Phase F — UX and security hardening
Target: Months 9–10.

#### T18 — UX hardening

Run representative-user tests on:
- first warehouse setup;
- receiving;
- putaway;
- picking;
- packing;
- supported exception resolution.

Measure:
- time to task;
- errors;
- help requests;
- confusion points.

Fix core-loop friction before adding features.

Maps to AC-16.

---

#### T19 — Security hardening

Verify:
- tenant isolation;
- role enforcement;
- secrets handling;
- transport security;
- privileged action audit;
- import validation;
- common web application attack surfaces.

Any cross-tenant defect is release-blocking.

### Phase G — Real warehouse pilot and payment
Target: Months 11–12.

#### T20 — First production pilot

Select one customer whose needs fit the P0 exclusions.

Pilot entry conditions:
- independent QA evidence for AC-01..18;
- no open critical inventory/security defect;
- UAT passes;
- backups/restores verified;
- monitoring live;
- known limitations documented.

Pilot measurement:
- daily receiving/picking/packing usage;
- inventory discrepancies;
- transaction/error rate;
- support hours;
- operator friction;
- uptime/incident log;
- workflows the customer still cannot complete.

Maps to AC-19.

---

#### T21 — Paid production evidence

Obtain one of:
- paying customer;
- paid production pilot;
- signed/binding payment commitment tied to production use.

Free praise is not AC-20 evidence.

## 4. Spec Kit workflow

The Product Blueprint remains the product-level source of truth.

After Blueprint approval and before feature implementation:
1. commit the approved Constitution using `/speckit.constitution`;
2. use `/speckit.specify` for bounded feature/process specifications derived from this Blueprint;
3. use `/speckit.clarify` when requirements are ambiguous;
4. use `/speckit.plan` for technical planning;
5. use `/speckit.tasks` for traceable implementation work;
6. run `/speckit.analyze` before implementation where useful;
7. use convergence/review against Blueprint + Constitution after implementation.

Spec Kit MUST NOT create a second competing product scope. If a generated spec conflicts with the Blueprint, the conflict must be resolved before coding.

## 5. Independent QA rule

Coding Agent implementation evidence is not final acceptance.

For every production-complete vertical slice:
- Coding Agent runs automated checks;
- QA independently reruns relevant checks;
- acceptance evidence references the Blueprint AC;
- only then is the slice considered Done.

## 6. Scope-change rule

Any addition to P0 must satisfy at least one:
- required to meet an existing acceptance criterion;
- real pilot evidence proves the supported core job cannot be completed without it;
- required for safety/security/privacy/reliability;
- explicitly approved by the CEO.

Customer requests alone do not automatically enter P0.

## 7. Open implementation decisions

These are intentionally not fixed in Stage 3:
- programming language/runtime;
- database vendor;
- cloud provider;
- web/mobile UI framework;
- hosting architecture;
- exact scanner integration;
- event sourcing vs transactional ledger implementation;
- exact API style;
- MCP implementation timing;
- billing implementation.

Coding Agent may propose options during planning, but choices must preserve the Constitution and Blueprint.

## 8. Stage 3 completion criteria

Stage 3 is ready for design handoff only when:
- Product Blueprint receives independent review;
- Implementation Plan receives independent review;
- CEO accepts P0/non-goals if material ambiguity exists;
- repository is confirmed as the canonical product repository;
- Constitution is committed or explicitly queued for Spec Kit initialization;
- accepted Stage-2 waiver risks remain visible.

Next owner after Stage 3 acceptance: **UX/UI Designer Agent — Stage 4**.
