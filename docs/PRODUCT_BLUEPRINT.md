# Product Blueprint — AI-Configured Enterprise-Grade WMS for SMB

Status: **Stage 3 draft — pending independent review**  
Owner: Product Manager Agent  
Date: 2026-09-26  
Idea Vault: **#66**  
Repository: `igarikg/wmsnext`  
CEO Gate A: **SELECT FOR VALIDATION — 2026-09-03**  
CEO Gate B: **GO — 2026-09-26; Stage 2 kill-test intentionally waived by CEO**

Canonical decision evidence:
- Gate A Decision Brief: https://docs.google.com/document/d/1U7T9m5nCvgJK2MsSJuxa-6Kety-7GPXQqZfqWZKO_bA/edit
- Gate B Decision: https://docs.google.com/document/d/1hzpEU5ADtK7ZASwOW7HgTMPnvqku1bGCQ8K9xpUf99o/edit

> **Vision:** WMS designed from day one for humans and AI agents to operate, configure, test, diagnose, and evolve safely.

## 1. Strategic context

The product is a production WMS for growing SMB and lower-mid-market warehouses that need serious warehouse execution without the implementation burden and consulting dependency typical of enterprise WMS projects.

The long-term product thesis is broader than the first MVP:
- deterministic enterprise-quality warehouse execution;
- self-explanatory and increasingly self-service administration;
- typed, auditable configuration;
- architecture that can later be operated and configured safely by AI agents;
- automated reproducibility and UAT as first-class capabilities;
- future expansion toward broader Supply Chain Execution where evidence supports it.

The founder's strongest domain advantage is warehouse-process expertise. That advantage should be used to make warehouse behavior correct and practical, not to justify uncontrolled scope.

### Accepted validation risk

Stage 2 was explicitly waived by the CEO. Therefore willingness to pay, distribution, onboarding economics, and the commercial value of AI-led configuration remain **accepted but unproven** risks. This Blueprint must not state or imply that those hypotheses were validated.

## 2. First-year objective

Within approximately one year at roughly 20 hours/week, produce a WMS MVP that can be trusted by real customers for a bounded daily warehouse flow.

### Product-level PASS

PASS only when:
1. at least one real customer warehouse can use the supported WMS flow productively in day-to-day operations;
2. the customer trusts the inventory state and warehouse transactions;
3. the supported flow is stable enough that routine work does not require constant developer intervention;
4. at least one customer is willing to pay for the product or a paid production pilot.

### Product-level FAIL

FAIL if:
- supported warehouse functionality is too narrow to replace the customer's current operational workflow;
- inventory correctness or transaction reliability cannot be trusted;
- the system is operationally unstable;
- onboarding/support burden makes the product effectively a consulting project;
- target customers are not willing to pay.

## 3. P0 target customer

### Primary ICP

A single-site SMB / lower-mid-market e-commerce or distribution warehouse with approximately:
- 5–30 warehouse employees;
- barcode-driven piece inventory;
- conventional bins/locations;
- stable network connectivity;
- straightforward inbound and outbound flows;
- no requirement for manufacturing execution or advanced 3PL multi-client separation in P0.

Primary users:
- warehouse receiver;
- putaway operator;
- picker;
- packer;
- warehouse supervisor;
- WMS administrator.

Economic buyer:
- owner;
- COO;
- Head of Operations;
- Warehouse Manager;
- Head of Supply Chain.

### P0 customer exclusions

Do not select the first production customers if their operation requires any of the following as a hard dependency:
- multi-client 3PL accounting/segregation;
- multiple warehouses as one orchestration network;
- manufacturing / production orders;
- mandatory offline operation;
- lot/expiry or serial-number traceability;
- catch weight / variable-weight inventory;
- highly automated material-handling equipment;
- advanced wave/cluster/voice/robotic picking;
- regulated validation requirements beyond the product's verified scope.

## 4. Core job

> Run the essential warehouse flow **Receiving → Putaway → Picking → Packing** with correct inventory changes, clear operator guidance, complete traceability, and production-grade reliability.

The first MVP must prove the warehouse engine before expanding feature breadth.

## 5. Product promise

For the supported P0 process, the WMS should let a new customer configure the warehouse, load inventory/orders through bounded inputs, execute daily warehouse work with barcode-assisted workflows, and always understand:
- what stock exists;
- where it exists;
- what is available or reserved;
- what warehouse transaction changed it;
- what the operator must do next;
- what failed when an operation cannot be completed.

## 6. P0 scope

### P0-01 — Tenant, users, roles, and authorization

The system must support one isolated tenant/customer account with:
- users;
- warehouse roles;
- least-privilege authorization;
- explicit permissions for inventory-changing and administrative actions.

Cross-tenant access is prohibited.

### P0-02 — Warehouse master data

Support:
- one warehouse per tenant;
- zones / areas;
- storage and pick bins/locations;
- SKU/product master;
- primary barcode(s);
- base unit of measure;
- optional simple case-pack conversion when explicitly configured.

Exclude advanced UOM graphs, catch weight, pallet license-plate management, packaging hierarchies, and customer-specific master-data extensions.

### P0-03 — Authoritative inventory core

The WMS must maintain an authoritative, auditable inventory transaction model capable of deriving or validating:
- on-hand quantity;
- available quantity;
- reserved quantity;
- quantity by location.

All stock-changing operations must go through the same domain rules. Direct inventory mutation outside the domain/application boundary is prohibited.

The exact persistence architecture is an implementation decision, but the resulting inventory history must support reconstruction and audit.

### P0-04 — Receiving

Support a bounded inbound-receiving flow:
1. create/import an inbound receipt instruction;
2. identify SKU by scan/search;
3. enter/scan received quantity;
4. validate over/under receipt according to explicit rules;
5. post the receipt atomically;
6. create stock that is awaiting or eligible for putaway;
7. retain a complete audit trail.

P0 does not include full purchasing, supplier management, invoice matching, ASN/EDI ecosystems, or yard scheduling.

### P0-05 — Putaway

Support:
- deterministic destination suggestion using a small approved rule set;
- operator confirmation by scanning source/item/destination as required by UX;
- atomic movement from receiving/staging to storage/pick location;
- explicit error handling for invalid destination, quantity, or state.

P0 putaway rules must remain intentionally small and typed.

### P0-06 — Outbound order input and minimal reservation

Support creation/import of outbound orders through bounded product interfaces.

The WMS must prevent two operators/orders from consuming the same stock.

P0 may use a deliberately simple deterministic reservation/allocation rule sufficient to support picking. Advanced allocation optimization, prioritization engines, shortages orchestration, fair-share allocation, wave planning, and promise-date logic are non-goals.

### P0-07 — Picking

Support one production-grade picking workflow for the initial ICP.

Requirements:
- clear pick task/order;
- source location;
- SKU/barcode validation;
- requested and completed quantity;
- prevention of over-pick and wrong-SKU confirmation;
- deterministic inventory/reservation changes;
- explicit exception state when the expected stock cannot be picked.

Do not add multiple picking strategies merely for feature breadth.

### P0-08 — Packing and completion

Support:
- review of picked order contents;
- barcode-assisted verification where applicable;
- detection of missing/wrong quantity before completion;
- explicit pack/dispatch confirmation;
- final inventory/order-state consistency.

P0 does not require carrier-rate shopping, carrier label generation, parcel optimization, or transportation management.

### P0-09 — Basic operational exceptions and inventory adjustment

The system must provide controlled, authorized handling for unavoidable exceptions such as:
- receipt discrepancy;
- pick short;
- damaged/missing stock discovered during work;
- explicit inventory correction by authorized user.

Every adjustment must have a reason, actor, before/after state, and audit record.

A full cycle-counting module is not P0.

### P0-10 — Barcode-first warehouse operation

The core operator flows must work effectively on a mobile/browser form factor and support barcode scanning.

Scanning technology must be abstracted from warehouse business logic.

P0 does not promise offline operation.

### P0-11 — Self-explanatory UX

Operator and administrator workflows must:
- use clear warehouse terminology;
- show current system state;
- prevent avoidable mistakes;
- provide actionable error messages;
- minimize configuration and training burden;
- avoid exposing implementation concepts that are irrelevant to warehouse work.

The UX must be usable without a long consulting-led onboarding program.

### P0-12 — Typed, validated, versioned configuration foundation

Material warehouse configuration must be:
- schema-defined;
- machine-readable;
- typed;
- validated;
- versioned;
- auditable.

P0 configuration covers only the rules needed by the supported warehouse flow.

### P0-13 — AI-ready architecture, not AI feature scope

P0 **does not include the AI configurator as a customer-facing feature**.

However the architecture must make future idea #73 / AI WMS Engineer feasible without rewriting the warehouse core:
- structured read/query interfaces;
- structured command interfaces;
- machine-readable configuration;
- explicit schemas and stable identifiers;
- protocol-agnostic domain/application boundary;
- observable state and audit evidence;
- automated scenario execution.

MCP or another agent protocol may be implemented as an adapter, but no protocol may become the warehouse domain model.

### P0-14 — Automated UAT and regression foundation

Critical flows must be reproducible from known initial state and executable automatically.

At minimum, the repository must support deterministic automated scenarios for:
- Receiving → Putaway → Inventory Verification;
- Outbound Order → Reservation → Picking → Packing → Inventory Verification;
- retry/idempotency cases;
- concurrency/conflict cases;
- invalid scan/quantity/location cases;
- recovery from failed operation without silent partial state.

### P0-15 — Auditability and observability

For all material operations the system must expose enough evidence to answer:
- what happened;
- who/what initiated it;
- when;
- what state existed before;
- what state exists after;
- why the operation succeeded or failed.

Production diagnostics must include structured logs, metrics, and traceable transaction identifiers without leaking secrets or unnecessary customer data.

### P0-16 — Backup, recovery, and safe deployment

Before production pilot:
- backup behavior must be defined;
- restore must be tested;
- deployment must support rollback or an equivalent safe recovery strategy;
- database/schema changes must be migration-tested;
- a failed deployment must not leave inventory state ambiguous.

### P0-17 — Bounded data exchange

P0 must support a minimal way to exchange inbound/outbound business data without committing the core to a specific ERP.

Allowed first-release mechanisms:
- manual entry for bounded test/pilot cases;
- CSV import/export;
- a documented generic API where required by the pilot.

System-specific ERP, carrier, marketplace, and EDI integrations are outside P0 unless explicitly approved as required for a paying pilot.

## 7. Explicit P0 non-goals

The following are not P0 unless the CEO explicitly expands scope:
- AI configurator / AI implementation engineer as a customer-facing feature;
- replenishment;
- cycle counting;
- sophisticated allocation;
- multi-warehouse orchestration;
- 3PL multi-client functionality;
- manufacturing;
- returns/RMA workflow;
- lots, expiry, serials;
- advanced UOM/catch weight;
- FEFO/FIFO policy engine beyond what the basic process needs;
- advanced wave/batch/cluster/zone picking;
- slotting optimization;
- labor management;
- yard management;
- TMS/carrier platform;
- shipping-label generation;
- cartonization;
- robotics/automation control;
- offline-first mobile operation;
- customer-specific code forks;
- broad external integrations.

## 8. Main workflows

### Workflow A — Warehouse setup

1. Admin creates warehouse zones/bins.
2. Admin imports/creates SKUs and barcodes.
3. Admin configures the minimal rules required by P0.
4. System validates configuration before activation.
5. Configuration version and actor are recorded.

### Workflow B — Receiving → Putaway

1. Receiver opens inbound instruction.
2. Receiver scans/chooses SKU and confirms quantity.
3. WMS posts receiving transaction atomically.
4. WMS shows suggested putaway destination.
5. Operator confirms source/item/destination.
6. WMS posts movement atomically.
7. Final quantity/location state is verifiable from audit history.

### Workflow C — Outbound → Picking → Packing

1. Outbound order enters the WMS.
2. Minimal deterministic reservation prevents double consumption.
3. WMS creates the supported picking task.
4. Picker scans/confirms location and SKU.
5. Picking transaction updates reservation/inventory consistently.
6. Packer verifies contents.
7. Pack/dispatch confirmation completes the supported order workflow.
8. Final order and inventory states are mutually consistent.

### Workflow D — Exception correction

1. Operator reports an exception.
2. WMS blocks unsafe silent continuation.
3. Authorized role chooses an explicit supported resolution.
4. WMS records reason and state change.
5. Audit trail remains queryable.

## 9. Core domain invariants

The following are P0 invariants and must be enforced in the domain layer:

1. Inventory cannot appear or disappear without an explicit transaction.
2. A stock-changing command cannot be applied twice because of retry or duplicate delivery.
3. A failed stock-changing operation cannot leave a partially applied state.
4. Reserved quantity cannot exceed eligible inventory according to the supported rule set.
5. Two concurrent operations cannot successfully consume the same units.
6. A movement must keep source/destination and total quantity consistent.
7. Picking/packing cannot silently create or destroy inventory.
8. Adjustments require explicit authorization and reason.
9. Audit history for material transactions cannot be optional.
10. AI or external adapters cannot bypass the same invariants used by the UI/API.

## 10. Architecture requirements

The warehouse domain core must remain independent of:
- UI framework;
- database vendor;
- scanner technology;
- commerce platform;
- ERP;
- carrier;
- AI protocol.

UI, external API, future MCP/agent interfaces, and integrations must call the same application/domain rules.

No interface may reimplement a separate version of inventory, allocation, putaway, or picking logic.

## 11. Security and data protection

P0 must include:
- authenticated access;
- tenant isolation;
- role-based authorization;
- least privilege;
- protected secrets;
- encrypted transport;
- appropriate encryption at rest for production data;
- audit trail for privileged actions;
- safe handling of imported order/customer data;
- no sensitive operational data in analytics by default.

Security defects that can expose another tenant's data are release blockers.

## 12. UX and usability requirements

### Operator UX

Core warehouse actions should be optimized for fast, low-ambiguity use:
- scan-first where appropriate;
- one clear primary action;
- large mobile touch targets;
- visible SKU/location/quantity;
- immediate feedback;
- explicit blocking errors;
- no reliance on color alone;
- safe recovery path.

### Administrator UX

Configuration must:
- use warehouse language rather than internal technical language;
- explain consequences of material settings;
- validate before activation;
- show current version/state;
- avoid requiring direct database or code changes.

### Usability target

A representative operator should be able to execute the supported core flow after brief orientation without continuous developer/consultant assistance.

## 13. Product metrics

### Reliability / correctness

Track:
- inventory reconciliation discrepancies;
- transaction failures by reason;
- duplicate/retry suppression;
- concurrency conflicts;
- failed/rolled-back transactions;
- backup/restore evidence;
- open critical defects.

### Core-job completion

Track:
- receipts completed;
- putaways completed;
- picks completed;
- packs completed;
- end-to-end order completion rate;
- exception rate;
- median/p90 transaction latency for scan-driven actions.

### Self-service / support

Track:
- time to first configured warehouse;
- onboarding/support hours per customer;
- operator training time;
- administrator configuration errors;
- percentage of core actions completed without support.

### Commercial

Track:
- pilot warehouses;
- active warehouse users;
- repeat weekly use;
- paid pilots/customers;
- MRR/ARR;
- gross margin;
- support cost per customer.

## 14. Monetization hypothesis

Initial serious SMB WMS pricing hypothesis remains approximately **USD 299–499/month** for an early production tier, with later expansion by warehouse/users/order volume/modules.

A paid pilot may use a simpler commercial arrangement. The first milestone is evidence that a real warehouse will pay for a trustworthy bounded WMS, not pricing optimization.

## 15. Acceptance criteria

### AC-01 — Inventory integrity
Every supported stock-changing flow produces the expected inventory state and an auditable transaction record. No known path may silently create, lose, or duplicate stock.

### AC-02 — Idempotency
Retrying the same idempotent stock-changing command does not apply the business effect twice.

### AC-03 — Concurrency
Conflicting concurrent actions cannot both consume the same eligible stock or leave inconsistent reservations/quantities.

### AC-04 — Receiving
A valid inbound line can be received; invalid SKU/quantity/state is rejected explicitly; successful receipt produces the correct stock state and audit evidence.

### AC-05 — Putaway
A valid putaway moves the intended quantity to an allowed destination atomically; invalid location/quantity/state is blocked.

### AC-06 — Outbound reservation
The supported reservation rule prevents double consumption and produces a deterministic result for the same state/configuration.

### AC-07 — Picking
The supported picking workflow prevents wrong-SKU and over-pick confirmation and updates stock/reservation consistently.

### AC-08 — Packing
Packing verifies picked contents and cannot mark the supported order complete while required items remain unresolved.

### AC-09 — Adjustment
Authorized adjustment requires a reason and records actor, before/after quantity and transaction reference.

### AC-10 — Auditability
For every material P0 transaction, support can reconstruct what happened from durable system evidence.

### AC-11 — Tenant isolation
One customer cannot access another customer's warehouse data through UI, API, background jobs, or future agent interface.

### AC-12 — Configuration integrity
Material configuration is typed, validated, versioned and auditable. Invalid configuration cannot be activated silently.

### AC-13 — Automated UAT
The primary inbound and outbound flows run from known initial state to deterministic PASS/FAIL evidence in automated tests.

### AC-14 — Recovery
A tested backup/restore procedure exists before real production inventory is entrusted to the system.

### AC-15 — Safe deployment
Schema/deployment failure cannot leave an ambiguous inventory state; rollback/recovery procedure is tested.

### AC-16 — UX
Representative warehouse users can complete the supported flow after brief orientation without continuous expert intervention.

### AC-17 — AI readiness
Core state, configuration, commands, validation and tests have structured application boundaries sufficient for a future authorized AI agent without direct database manipulation.

### AC-18 — Scope protection
No replenishment, cycle-counting module, advanced allocation, 3PL, broad integrations, lot/serial/expiry, or customer-facing AI configurator enters P0 without explicit CEO approval.

### AC-19 — Real warehouse pilot
At least one real warehouse runs the supported production flow for a meaningful continuous period without a critical inventory-integrity defect.

### AC-20 — Payment evidence
At least one real customer pays, signs a paid pilot, or provides equivalent binding payment commitment for the production WMS.

## 16. Release gates

### Ready for Stage 4 — UX/UI

- this Blueprint receives independent review/approval;
- P0/non-goals are accepted;
- no unresolved ambiguity materially changes the four core warehouse processes;
- accepted Stage-2 waiver risks remain visible.

### Ready for implementation after CEO Gate C

- approved design specification exists for core operator/admin journeys;
- Spec Kit Constitution is committed;
- feature specification(s) preserve Blueprint scope;
- implementation plan remains traceable to acceptance criteria;
- architecture review confirms inventory invariants and testability.

### Ready for first production pilot

- AC-01 through AC-18 have independent QA evidence for the supported scope;
- no open critical inventory-integrity/security defect;
- automated inbound/outbound UAT passes;
- monitoring is live;
- backup/restore is tested;
- known limitations are documented and accepted by the pilot customer.

### Product-level PASS after pilot

- real productive warehouse usage;
- trusted inventory state;
- supported flow stable enough for daily work;
- no critical unresolved inventory-correctness issue;
- at least one paying customer / paid production pilot.

## 17. Open risks carried forward

1. Stage-2 market validation was waived.
2. Willingness to pay remains unproven until a paid pilot.
3. Distribution may become consulting-heavy.
4. A narrow P0 may still be insufficient for some warehouses.
5. WMS correctness/concurrency may consume more of the first year than expected.
6. Integration requests may pressure scope.
7. Incumbents can add AI configuration.
8. AI differentiation is intentionally deferred from customer-facing P0; the architecture, not the first MVP feature list, preserves that option.

## 18. Product evolution principle

The MVP is not a miniature checklist version of every enterprise WMS feature.

It is a trustworthy production warehouse engine for one bounded process family.

After real paid usage proves the core, later modules may add replenishment, cycle counting, richer allocation, returns, lot/serial/expiry, 3PL, multi-warehouse, integrations, and the AI WMS Engineer according to customer evidence and explicit CEO scope decisions.
