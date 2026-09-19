<div align="center">

# Agent Accountability Standard (AAS)
### Cross-Organizational Agent Action Attestation Schema (CAAAS)
### Full Specification: Discussion Draft 2.1

*[← Back to the short overview](README.md)*

**Discussion Draft 2.1 · 16 September 2026**

[![License: Apache 2.0](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)
[![Status](https://img.shields.io/badge/status-discussion%20draft-orange.svg)](#document-status)
[![Schema](https://img.shields.io/badge/schema-draft%20v0.1-lightgrey.svg)](aas-action-event.schema.json)

**Kay Yoo** · [Naru Advisory](https://naruadvisory.com) · [kay@naruadvisory.com](mailto:kay@naruadvisory.com)

</div>

---

### Document status

This is an independent discussion draft for public review. It is **not an adopted industry standard**, does not represent the views of any standards body or reviewer, and makes no claim of conformance, certification, or endorsement. Normative requirements are provisional until there are independent implementations and a documented governance process.

## Table of Contents

- [Abstract](#abstract)
- [1. Scope](#1-scope)
- [2. Design Principles](#2-design-principles)
- [3. Ecosystem Position as of September 2026](#3-ecosystem-position-as-of-september-2026)
- [4. Threat Model and Evidence Semantics](#4-threat-model-and-evidence-semantics)
- [5. Action Lifecycle](#5-action-lifecycle)
- [6. CAAAS Core Envelope](#6-caaas-core-envelope)
- [7. Exchange and Enforcement](#7-exchange-and-enforcement)
- [8. Evidence Infrastructure](#8-evidence-infrastructure)
- [9. Privacy, Retention, and Access](#9-privacy-retention-and-access)
- [10. Physical-Action Profile](#10-physical-action-profile)
- [11. Conformance Profiles](#11-conformance-profiles)
- [12. Regulatory and Assurance Alignment](#12-regulatory-and-assurance-alignment)
- [13. Governance and Adoption](#13-governance-and-adoption)
- [14. Open Questions](#14-open-questions)
- [Repository Layout](#repository-layout)
- [References](#references)
- [Changes from the May 2026 Concept Paper](#changes-from-the-may-2026-concept-paper)

---

## Abstract

AI agents increasingly invoke tools, delegate work, move money, access data, and affect physical environments across organizational boundaries. Existing protocols address important parts of this stack: MCP connects applications to tools and data; A2A supports agent-to-agent interaction; OAuth and workload-identity systems authenticate and authorize actors; AP2, Verifiable Intent, x402, MPP, and Visa TAP address agentic commerce; OpenTelemetry records operational traces; and SCITT, Certificate Transparency, Sigstore, and related systems provide reusable transparency and attestation primitives.

The remaining interoperability problem is narrower than "accountability is missing." Evidence exists, but it is fragmented across organizations, protocols, policy engines, payment systems, and logs. There is no broadly adopted, domain-neutral event envelope and lifecycle for linking who acted, on whose authority, under which policy, against which target, with what result, and where independently verifiable evidence can be found.

The Agent Accountability Standard (AAS) proposes that envelope. Its machine-readable component, the Cross-Organizational Agent Action Attestation Schema (CAAAS), represents a consequential action as a linked sequence of signed claims (such as authorization, dispatch, acceptance, execution, result, failure, revocation, and compensation) rather than as one record that overclaims what happened. AAS is designed to reuse existing identity, authorization, observability, payment, signature, and transparency-log standards. It does not replace them.

## 1. Scope

### 1.1 Problem statement

When an agent operated by one organization acts in another organization's environment, each party may retain a different record. One system may store an OAuth decision, another an A2A task, a third an AP2 mandate, a fourth a tool-call trace, and a fifth a settlement receipt. These records may use incompatible identifiers, retention rules, signing formats, and disclosure models. After an incident, neither an auditor nor a counterparty can assume that a single party's application log is complete.

AAS specifies a portable evidence envelope that links those records without requiring their sensitive contents to be made public. It is intended for actions with material financial, legal, privacy, security, safety, or operational consequences.

### 1.2 In scope

AAS defines:

- a common event envelope and identifiers for an action lifecycle;
- the minimum claims needed to attribute an event to an issuer and an operating organization;
- references to authorization, delegation, policy, payment, protocol, model, tool, and result evidence;
- integrity, privacy, retention, replay-protection, and transparency-receipt requirements;
- bilateral or multi-party receipts for cross-organizational actions;
- conformance profiles for core, transparency-backed, bilateral, and physical-action deployments.

### 1.3 Out of scope

AAS does not:

- decide whether an action is ethically or legally permissible;
- replace OAuth, OpenID Connect, SPIFFE, DIDs, Verifiable Credentials, AP2, A2A, MCP, or payment-network rules;
- prove that a signed claim is true, only who signed it and whether it changed;
- require a blockchain or a single global ledger;
- require publication of prompts, personal data, trade secrets, credentials, or full payloads;
- make legal conclusions about liability, agency, electronic signatures, or non-repudiation;
- prescribe one regulator, foundation, vendor, or network as operator.

## 2. Design Principles

1. **Compose existing standards.** AAS should bind existing proofs rather than invent parallel identity or authorization systems.
2. **Separate claims from facts.** A signature authenticates a claim; it does not establish that the claimed real-world event occurred.
3. **Represent lifecycle, not only intent.** A pre-action record proves dispatch or intent. Execution and outcome require later events or counterparty receipts.
4. **Minimize disclosure.** The default public artifact should be a commitment, receipt, or selectively disclosed credential, not a full operational record.
5. **Permit plural infrastructure.** Bilateral stores, consortium transparency services, and public ledgers can all be conforming if they meet the selected profile.
6. **Make omission visible.** High-assurance deployments should gate consequential actions on a valid record or emit an explicit, reviewable exception.
7. **Support contestability.** Corrections, revocations, disputes, and compensating actions are new linked events; prior evidence is not silently rewritten.
8. **Avoid governance capture.** Changes should use open issue tracking, recorded decisions, independent implementations, and conflict-of-interest disclosure.

## 3. Ecosystem Position as of September 2026

| Layer | Examples | What they provide | AAS relationship |
|---|---|---|---|
| Tool and data connectivity | MCP | Tool discovery, invocation, and authorization mechanisms | Link MCP request/tool identifiers and authorization evidence |
| Agent interaction | A2A v1.0 | Agent discovery, messages, tasks, security schemes, and extensions | Carry an AAS extension or evidence reference alongside a task |
| Identity and authorization | OAuth/OIDC, SPIFFE, W3C VC/DID, AIP drafts, Microsoft AGT | Authentication, delegation, policy evaluation, workload or agent identity | Reference, not replace, the authorization decision and credential |
| Agentic commerce | AP2, Verifiable Intent, Visa TAP, x402, MPP | Intent, merchant recognition, payment coordination, and settlement | Bind payment-specific proof into a broader action lifecycle |
| Observability | OpenTelemetry GenAI conventions, W3C Trace Context | Operational traces and correlation identifiers | Reuse `traceparent` and stable span/event references |
| Signed statements and transparency | IETF SCITT, Sigstore/Rekor, CT, Tessera | Signed statements, receipts, append-only logs, consistency and inclusion proofs | Use as storage, receipt, and verification infrastructure |
| Hardware and execution-state attestation | IETF RATS, EAT | Evidence and appraisal of device or execution state | Reference device evidence for high-assurance and physical profiles |
| **Cross-organizational action evidence** | **AAS / CAAAS (proposed)** | **Portable event envelope and action lifecycle linking the layers above** | n/a |

This positioning is deliberately non-exclusive. Microsoft's Agent Governance Toolkit and emerging open governance specifications already intercept actions and produce audit evidence. Mastercard's Verifiable Intent already links identity, intent, and payment actions. SCITT already defines an architecture for signed statements and transparency services. AAS is useful only if it offers an interoperable profile across these systems, not if it claims to be the sole accountability mechanism.

### 3.1 Related Standards Efforts and Research

The problem AAS addresses is drawing active attention from several directions at once. None of the following overlaps AAS entirely; each addresses one part of the same underlying gap, and AAS is written to compose with them rather than compete with them.

- **IETF `draft-farley-acta-signed-receipts`, "Signed Decision Receipts for Machine-to-Machine Access Control"**[^25] proposes a signed-receipt format for individual machine-to-machine access decisions. It is closely aligned with the integrity requirement in Section 6.4, but it addresses a single access-control decision rather than a linked, multi-party action lifecycle; it does not by itself specify how a dispatch, an acceptance, a result, and a later dispute are correlated as one auditable sequence across two organizations.
- **IETF `draft-klrc-aiagent-auth`, "AI Agent Authentication and Authorization"**[^26] observes that agent identity and authorization efforts have developed in isolation, often reinventing mechanisms with prior art elsewhere: the same fragmentation problem this document's Abstract describes. Its scope is authentication and authorization; it does not extend to post-action evidence, so an `authorization` event under this draft's model could plausibly feed the `authorization_ref` field of a CAAAS event without conflict.
- **Sello**[^27], a reference implementation for receiver-attested confidential receipts for AI agent actions, is the closest existing analog to CAAAS's core mechanism: a receiver-signed, confidentiality-preserving receipt of an agent action. Based on its published description, it does not appear to model a full lifecycle of linked pre- and post-action events, define graded conformance profiles, or extend to physical-action evidence, but a full comparison should be done directly against its specification rather than assumed here before any claim of differentiation is finalized.
- **South, Hardjono, Mahari, et al., "Authenticated Delegation and Authorized AI Agents"**[^28] proposes an OAuth 2.0/OpenID Connect–based framework for delegation credentials establishing that a principal has authorized an agent to act. This is authorization-credential work, analogous to AIP and Microsoft AGT in Section 3's table: it establishes who may act, not what evidentiary record must exist once they do.

### 3.2 Where the Gap Still Sits

Read together, the efforts in 3.1 and the table above cover authentication, authorization, delegation credentials, single-decision receipts, payment proofs, and connectivity. None of them, individually or collectively as currently published, specify:

1. **A cross-organizational action *lifecycle*, not a single record.** Most adjacent efforts produce one signed artifact per decision or per payment. AAS models an action as an append-only sequence of linked events (Section 5) so that dispatch, acceptance, execution, and a later dispute or compensation remain connected under one `action_id` without requiring any single party's record to be complete on its own.
2. **A binding layer across protocols, not within one.** AP2, Verifiable Intent, and x402 are strong within agentic commerce; AIP, Microsoft AGT, and the IETF auth drafts are strong within authorization. None is designed to carry evidence identifiers across MCP, A2A, payment protocols, and physical control systems at once the way Section 7.3's protocol bindings do.
3. **A physical-action profile.** Nothing surveyed above (including the Singapore and NIST work referenced in Section 12) extends its schema to robots, vehicles, or industrial control systems as a first-class case. Section 10 does.
4. **Graded conformance tied to risk, not a single bar.** Section 11's Core / Transparency-backed / Bilateral / Physical profiles let an adopter choose an assurance level matched to the action's consequences. Most adjacent drafts specify one mechanism rather than a ladder.
5. **A single technical vocabulary usable across multiple regulatory regimes at once.** The EU AI Act, NIST's initiative, Singapore's framework, and the Berkeley profile (Section 12) each require organizations to produce auditable evidence of agent behavior, but none specifies the interoperable schema that evidence should take. An organization operating under more than one of these regimes currently has no single technical answer that satisfies all of them; that is the specific, narrow gap this document is written to fill.

This list should be treated as provisional and revised as the related efforts in 3.1 publish more complete specifications; the working group process in Section 13 exists partly to test whether it still holds.

## 4. Threat Model and Evidence Semantics

### 4.1 Threats considered

AAS considers:

- forged or replayed action records;
- compromised, expired, or mis-bound signing keys;
- an agent exceeding delegated scope;
- omitted, selectively disclosed, or post-hoc records;
- disagreement between initiator and receiver about what occurred;
- log equivocation, rollback, or unavailable infrastructure;
- accidental disclosure of sensitive prompts, payloads, or identifiers;
- high-volume event flooding and denial of service;
- clock skew and misleading timestamps;
- model, tool, or policy changes that prevent later reconstruction;
- physical actions whose consequences cannot be reversed.

### 4.2 What evidence can establish

An AAS verifier **may** establish that:

- a particular key signed a canonical event;
- the event was covered by a credential or key binding valid under the verifier's trust policy;
- the event references a stated principal, actor, policy, target, and action;
- an inclusion receipt commits to the event at or before a particular checkpoint;
- two or more independently signed events refer to the same action lifecycle.

An AAS verifier **must not** infer from those facts alone that:

- the issuer's claim is truthful;
- the underlying action succeeded;
- the human principal understood or validly consented;
- the agent behaved safely or lawfully;
- the signer had legal authority to bind another party;
- a cryptographic signature provides legal non-repudiation in every jurisdiction.

The term **evidentiary strength** is therefore preferred to categorical claims of "non-repudiation."

### 4.3 Why cryptographic signing is the required baseline

Section 4.2 limits what AAS evidence can establish. This section argues why even that limited set of properties (key-to-event binding, tamper evidence, and independent verifiability) requires a cryptographic signature specifically, and why weaker mechanisms sometimes proposed as substitutes do not provide it.

The requirement follows directly from the cross-organizational threat model in Section 4.1, not from a general preference for cryptography. Four properties are needed simultaneously for a record to be useful to a party that was not present when the action occurred, and no non-cryptographic mechanism supplies all four at once:

1. **Issuer binding.** A verifier who was not party to the original transaction must be able to determine which organization or agent made the claim, without relying on the claimant's own systems to vouch for that fact. A signature over a canonical representation of the event, checkable against a key the issuer controls, provides this. A database entry, a spreadsheet cell, or an unsigned log line does not: any of these can be authored, or later relabeled as authored, by whoever controls the storage.
2. **Tamper evidence without continuous trust.** A record may be examined long after the system that first held it has changed hands, been decommissioned, or come under dispute. Tamper evidence therefore must not depend on the verifier trusting whoever currently operates that storage. A signature is self-contained: any modification to the signed content invalidates the signature, checkable by any party holding the public key, independent of the storage layer. A bare hash (already excluded as a substitute for a signature in Section 6.4) proves content matches a prior value, but proves nothing about who committed to that value or when.
3. **Survivability across a change of custody.** Cross-organizational actions frequently outlive the relationship in which they occurred: a vendor is acquired, a counterparty's systems are decommissioned, a dispute surfaces years later. A signed claim remains independently verifiable regardless of what happens to the systems that generated it, because verification depends only on the signature, the canonical content, and the public key, not on continued access to, or continued good faith from, the original operator. A record that depends on trusting one operator's ongoing custody (a bilateral database, a vendor's internal audit trail) does not survive this.
4. **Detectable absence.** Design Principle 6 (Section 2) says omission should be made visible; that is only actionable if the presence of a valid record is a checkable, binary fact rather than a matter of whose word is trusted. A signature makes "no valid signed record exists for this action" independently verifiable. Without signing, absence of evidence and evidence of absence collapse into the same claim: "there's no record" and "they are not showing me the record" become indistinguishable from outside the claimant's own systems.

None of the plausible substitutes provide all four properties at once:

- **Bilateral trusted databases** provide issuer binding and, while the relationship holds, tamper evidence, but only for as long as both parties continue to trust the operator, and they do not survive a change of custody or a dispute in which one party controls the database.
- **Application logs and audit trails**, even timestamped ones, are authored and stored by one party and can be altered or selectively withheld by that party without any external party being able to tell: precisely the "omitted, selectively disclosed, or post-hoc records" and "log equivocation, rollback" threats in Section 4.1.
- **Unsigned hashes** establish that content has not changed since the hash was computed, but establish nothing about who computed it or vouched for it, and provide no basis for detecting a hash that was never published at all.
- **Screenshots, exports, and manual attestations** carry no cryptographic binding to an issuer or a moment in time, and are the easiest category of evidence to fabricate or selectively produce after the fact.

This is why Section 6.4 requires a digital signature or equivalent verifiable signature envelope as a floor requirement of the Core conformance profile, with no lower tier that substitutes an unsigned mechanism. A transparency receipt (Section 8.2) and a counterparty acceptance event (Section 11, Bilateral profile) add evidentiary strength beyond what a single signature provides, principally against a single issuer's unilateral omission, or a compromised key used before revocation. But they are additive protections layered on top of signing, not substitutes for it: a transparency service that logs unsigned claims can prove a claim was registered at a point in time, but not who made it or that it has not been altered since: exactly the pair of properties this section identifies as the baseline.

This does not extend to claims about the world beyond the record itself. Section 4.2 still holds without qualification: a signature authenticates that a specific key committed to a specific claim, not that the claim is true, that the underlying action succeeded, or that the signer had legal authority to bind another party. Cryptographic signing is the necessary floor for accountability evidence to be independently checkable at all; it is not, on its own, sufficient to establish that the events it describes occurred as described.

## 5. Action Lifecycle

AAS models one action as a set of immutable events linked by `action_id`.

| Event type | Typical issuer | Meaning |
|---|---|---|
| `authorization` | Principal organization or policy decision point | The stated action was allowed, denied, or made conditional under a policy |
| `dispatch` | Initiating agent gateway | A request was prepared and sent; this does not prove receipt or execution |
| `acceptance` | Receiving organization | The request was received and accepted, rejected, or challenged |
| `execution` | Tool, service, or actuator gateway | Execution began or a side effect was committed |
| `result` | Receiving service or verifier | A result, settlement, or completion status was observed |
| `failure` | Any participant | The action failed, timed out, or was blocked |
| `revocation` | Credential, policy, or principal authority | Prior authority or a key was withdrawn prospectively |
| `compensation` | Responsible operator | A reversal, remediation, or other compensating action was initiated |
| `dispute` | Any affected party | A party contests a claim, result, or interpretation |

Events are append-only. Corrections are new events referencing the event being corrected. For a high-risk action, a conforming implementation should capture at least `authorization`, `dispatch`, and one counterparty-issued terminal event (`result`, `failure`, or `dispute`).

## 6. CAAAS Core Envelope

The draft JSON Schema is at [`aas-action-event.schema.json`](aas-action-event.schema.json). The following fields describe the core model.

| Field | Requirement | Purpose |
|---|---|---|
| `spec_version` | Required | Version of this schema |
| `event_id` | Required | Globally unique identifier for this immutable event |
| `action_id` | Required | Correlates all events in one action lifecycle |
| `event_type` | Required | Lifecycle phase represented by the event |
| `occurred_at` | Required | RFC 3339 timestamp asserted by the issuer |
| `issuer` | Required | Signing entity, organization, key identifier, and signer role |
| `principal` | Required | Human, organization, service, or device on whose authority the action is claimed |
| `actor` | Required | Agent identity, operator, class, and software/model/runtime references |
| `target` | Required | Target organization and resource or service reference |
| `action` | Required | Machine-readable name, category, risk tier, side-effect class, and payload commitments |
| `authorization` | Required for `authorization` and `dispatch` | Decision, policy, credential, constraint, and approval references |
| `evidence` | Required for terminal events | Result status and commitments or receipts for request and result evidence |
| `trace` | Recommended | W3C Trace Context and protocol-native correlation identifiers |
| `privacy` | Required | Classification, disclosure mode, and retention metadata |
| `integrity` | Required | Canonicalization, digest, signature format, key, and optional transparency receipt |
| `previous_event_ids` | Conditional | Links lifecycle, delegation, correction, dispute, or compensation events |

### 6.1 Identity and key binding

Identifiers are URIs. AAS does not mandate DIDs. Implementations may use HTTPS identifiers, SPIFFE IDs, DIDs, account identifiers, or other resolvable forms. The verifier's trust policy determines acceptable methods.

`issuer.key_id` identifies the verification key or certificate. Implementations must support revocation or status checking and must retain sufficient historical material to verify an event after key rotation. The `actor.agent_id` identifies the logical agent; it must not be assumed to identify a particular running process unless accompanied by workload or device attestation.

### 6.2 Authorization references

`authorization.credential_refs` and `authorization.policy_ref` are references or digests, not bearer credentials. Implementations must never place access tokens, API keys, private prompts, or reusable secrets in an AAS event.

An authorization event should record:

- the decision (`allow`, `deny`, or `conditional`);
- policy identifier and version;
- credential or mandate commitments;
- constraints such as amount, time window, target, geography, and permitted tool;
- any required human approval evidence;
- an expiry and nonce where replay would matter.

### 6.3 Action commitments

Human-readable descriptions are informative. Verification must rely on structured fields and cryptographic commitments. Sensitive inputs and outputs should be represented by digests computed over a defined canonical form. If a payload may contain low-entropy values, a bare unsalted hash can leak information; implementations should use an encrypted envelope, keyed commitment, or salt managed under an evidence-access policy.

### 6.4 Integrity

AAS requires a digital signature or equivalent verifiable signature envelope. A bare hash is not a signature. Supported profiles should specify exact algorithms, key types, canonicalization, and verification rules. A baseline JSON profile uses RFC 8785 JSON Canonicalization Scheme with a JOSE or COSE signature. DSSE or a SCITT-compatible signed statement may be profiled where appropriate.

A signature authenticates the issuer's statement. A transparency receipt additionally establishes that a service committed to the statement. A witness or counterparty receipt reduces reliance on a single operator. These controls are complementary.

## 7. Exchange and Enforcement

### 7.1 Pre-action flow

For consequential cross-organizational actions, the initiating gateway should:

1. resolve actor and principal identity under local trust policy;
2. evaluate authorization and create or reference an `authorization` event;
3. create and sign a `dispatch` event with a nonce, expiry, and payload commitment;
4. obtain the required transparency or durability receipt for the selected conformance profile;
5. attach the `action_id`, `event_id`, and evidence locator to the outbound protocol message;
6. release, challenge, or block the action according to policy.

The receiver verifies the signature, freshness, authorization references, replay controls, and local policy. It then produces an `acceptance` event. The receiver or execution gateway later produces a terminal event.

### 7.2 Failure policy

AAS does not impose one universal fail-open or fail-closed rule. Each deployment must publish a failure policy by risk tier. Irreversible financial, safety, or privilege-changing actions should ordinarily fail closed if authorization evidence cannot be verified. Emergency and safety functions may require a locally authorized break-glass path; that path must emit a distinct exception event and must not masquerade as ordinary conformance.

### 7.3 Protocol bindings

- **A2A:** Use an extension URI to carry `action_id`, `event_id`, evidence locator, and receipt. Do not place reusable credentials in an Agent Card or task artifact.
- **MCP:** Enforce at the client, gateway, or tool boundary. Bind the event to the tool name, arguments commitment, server identity, and authorization decision.
- **HTTP:** Carry compact identifiers in headers and retrieve signed evidence out of band. Header values must be non-secret.
- **AP2 / Verifiable Intent:** Reference the applicable intent, cart, payment, or credential commitment and record settlement or dispute as terminal events.
- **x402 / MPP:** Bind the payment request and settlement identifier to the broader action lifecycle.
- **OpenTelemetry:** Reuse W3C Trace Context for operational correlation, while keeping AAS evidence durable independently of telemetry sampling.

## 8. Evidence Infrastructure

### 8.1 Local or bilateral stores

The core profile permits each party to retain signed events. This is deployable but provides weaker omission and availability guarantees. Bilateral cross-signing improves evidentiary strength but does not eliminate collusion or coordinated deletion.

### 8.2 Transparency services

The preferred high-assurance path uses IETF SCITT concepts and a transparency service capable of returning signed registration receipts. A tile-based log such as Tessera can provide append-only checkpoints, inclusion proofs, consistency verification, and witness support. Private entries may be represented by commitments while authorized parties retain encrypted evidence off-log.

Certificate Transparency is a useful design analogy, not a direct equivalence. CT concerns public certificates and benefits from browser-enforced policies, monitor ecosystems, and relatively public metadata. Agent actions can contain personal, commercial, and safety-sensitive information. AAS therefore requires privacy segmentation and does not assume a single public log.

### 8.3 Public ledgers

Public ledgers are optional transparency services, not a superior default. They may help when counterparties lack a shared operator, but introduce cost, privacy, governance, finality, availability, key-management, and jurisdictional tradeoffs. A conforming implementation should commit a digest or encrypted envelope rather than operational plaintext. No chain is privileged by this specification.

## 9. Privacy, Retention, and Access

Each deployment must define:

- data classification and fields permitted in the shared envelope;
- whether evidence is plaintext, redacted, encrypted, selectively disclosed, or commitment-only;
- who may resolve evidence locators and under what legal or contractual basis;
- retention, legal hold, deletion, and cryptographic-erasure policies;
- data residency and cross-border transfer requirements;
- procedures for data-subject requests and contested records;
- protection against correlation and graph-analysis risks.

Public transparency should never be interpreted as a requirement to publish personal data. Where deletion rights conflict with immutable commitments, the deployment should keep personal data off-log, use opaque or keyed commitments, and document residual linkability risk.

## 10. Physical-Action Profile

The physical profile adds evidence appropriate to robots, vehicles, industrial controls, and cyber-physical systems:

- controller, actuator, device, and safety-system identifiers;
- software, model, firmware, and policy versions;
- device or execution-state attestation references, potentially using RATS/EAT;
- command commitment and bounded operating envelope;
- geospatial or facility zone at a privacy-appropriate granularity;
- human supervisor or safety-controller status;
- start, stop, emergency-stop, and compensation events;
- sensor-evidence references and clock-synchronization quality.

An AAS record must not bypass existing functional-safety, industrial-control, aviation, automotive, medical-device, or machinery requirements. It supplies evidence; it is not a safety case.

## 11. Conformance Profiles

| Profile | Minimum requirements |
|---|---|
| **Core** | Valid schema, canonical form, issuer signature, stable identifiers, authorization reference, privacy metadata, replay protection |
| **Transparency-backed** | Core plus independently verifiable registration receipt, published checkpoint policy, monitor or witness policy, and evidence availability commitment |
| **Bilateral** | Transparency-backed plus receiving-party `acceptance` and terminal event linked to the same `action_id` |
| **Physical** | Bilateral plus device/controller identity, execution-state evidence, safety-envelope metadata, and emergency/failure semantics |

Conformance applies to an implementation and profile version, not to an organization in the abstract. A future test suite should include schema validation, canonicalization vectors, signature verification, key rotation, replay rejection, privacy checks, receipt verification, lifecycle completeness, and negative test cases.

## 12. Regulatory and Assurance Alignment

The EU AI Act requires high-risk AI systems to support automatic event logging over the system lifetime, but it does not prescribe AAS or a universal cross-organizational schema. AAS may help providers and deployers collect evidence relevant to Articles 12, 19, and 26; legal applicability remains system- and role-specific.[^1]

NIST's Center for AI Standards and Innovation (CAISI) launched an AI Agent Standards Initiative in February 2026 to catalyze industry-led, interoperable standards for AI agents; the NCCoE's concept paper on software and AI agent identity and authorization is one output under that broader initiative, framing agent identity, authorization, and non-repudiation as active standards-based problems.[^2][^24] AAS can complement that work by profiling durable action evidence; it should not be presented as a NIST-endorsed response.

The Berkeley Center for Long-Term Cybersecurity's Agentic AI Risk-Management Standards Profile emphasizes system-level governance, human responsibility, continuous monitoring, containment, and documentation. AAS can provide evidence for those controls but does not implement the whole profile.[^3]

Singapore's IMDA published a Model AI Governance Framework for Agentic AI in January 2026, requiring that agents carry a verifiable digital identity and that an audit trail exist showing which agent acted under whose authorization.[^23] AAS is one possible technical format for that audit trail; the framework does not specify one, and this document does not assume Singapore's framework or any other jurisdiction's guidance treats AAS as a reference implementation.

Assurance practitioners may use AAS events as audit evidence only after evaluating source reliability, key custody, completeness, population coverage, log governance, privacy controls, and the risk of management override. A schema alone does not establish audit sufficiency.

## 13. Governance and Adoption

The project should begin as a public working draft, not as a self-declared standard. A credible path is:

1. publish the schema, threat model, examples, test vectors, and issue taxonomy;
2. obtain technical reviews from identity, authorization, transparency-log, privacy, safety, payments, and assurance specialists;
3. build two independent implementations using different identity and storage stacks;
4. run a bounded pilot with one reversible software action and publish results;
5. establish open governance, intellectual-property terms, conflict disclosures, and a versioning policy;
6. pursue alignment or incubation with an appropriate standards or open-source venue only after implementation evidence exists.

No organization is named as operator in this draft. Possible homes (including AAIF, IETF-adjacent work, LF Decentralized Trust, or an independent consortium) would require their own intake, governance, and approval processes.

## 14. Open Questions

- What is the smallest interoperable core that implementers will actually emit?
- Which lifecycle events should be mandatory by risk tier?
- How should omission be detected when no universal gatekeeper exists?
- Which signature profile best balances JOSE adoption, COSE compactness, and SCITT compatibility?
- How should principal identity and agent identity be separated in short-lived, multi-tenant runtimes?
- How can disputes expose enough evidence for due process without revealing unrelated personal or commercial data?
- What retention and cryptographic-erasure patterns are acceptable across jurisdictions?
- When may high-frequency physical actions be batch-committed without losing useful causality?
- What independent governance model prevents an operator or major vendor from controlling the evidence layer?

Community input on these is welcome via GitHub issues.

## Repository Layout

```
.
├── README.md                              # Short overview, start here
├── SPEC.md                                # This document (the full normative discussion draft)
├── LICENSE                                # Apache License 2.0
└── aas-action-event.schema.json           # Draft JSON Schema for the core event envelope (Section 6)
```

That's everything in the repository today. `CONTRIBUTING.md`, a `CODE_OF_CONDUCT.md`, issue templates, and a populated `examples/` folder are all reasonable next additions once outside contributors are actually engaging, see [Section 13](#13-governance-and-adoption), step 1, but none of them exist yet, and nothing above links to them.

## References

[^1]: European Union, [Regulation (EU) 2024/1689, Article 12 and related obligations](https://eur-lex.europa.eu/eli/reg/2024/1689/oj/eng).
[^2]: NIST NCCoE, [Accelerating the Adoption of Software and AI Agent Identity and Authorization](https://www.nccoe.nist.gov/projects/software-and-ai-agent-identity-and-authorization), 5 February 2026.
[^3]: UC Berkeley CLTC, [Agentic AI Risk-Management Standards Profile](https://cltc.berkeley.edu/publication/agentic-ai-risk-profile/), February 2026.
[^4]: Linux Foundation, [Formation of the Agentic AI Foundation](https://www.linuxfoundation.org/press/linux-foundation-announces-the-formation-of-the-agentic-ai-foundation), 9 December 2025.
[^5]: A2A Project, [A2A Protocol Specification v1.0](https://a2a-protocol.org/latest/specification/).
[^6]: Model Context Protocol, [Specification 2026-07-28](https://modelcontextprotocol.io/specification/2026-07-28).
[^7]: Google Cloud, [Agent Payments Protocol (AP2)](https://cloud.google.com/blog/products/ai-machine-learning/announcing-agents-to-payments-ap2-protocol), 16 September 2025.
[^8]: Mastercard, [How Verifiable Intent builds trust in agentic AI commerce](https://www.mastercard.com/global/en/news-and-trends/stories/2026/verifiable-intent.html), 5 March 2026.
[^9]: Visa, [Trusted Agent Protocol](https://developer.visa.com/capabilities/trusted-agent-protocol/overview).
[^10]: Linux Foundation, [Operational launch of the x402 Foundation](https://www.linuxfoundation.org/press/linux-foundation-announces-operational-launch-of-x402-foundation-to-standardize-internet-native-payments-for-ai-agents-and-applications), 14 July 2026.
[^11]: Tempo, [Machine Payments Protocol and Tempo mainnet](https://tempo.xyz/blog/mainnet/), 18 March 2026.
[^12]: IETF, [RFC 9943: An Architecture for Trustworthy and Transparent Digital Supply Chains](https://www.rfc-editor.org/rfc/rfc9943.html), June 2026.
[^13]: IETF, [RFC 9334: Remote ATtestation procedureS Architecture](https://www.rfc-editor.org/rfc/rfc9334.html), January 2023.
[^14]: IETF, [RFC 9711: The Entity Attestation Token](https://www.rfc-editor.org/rfc/rfc9711.html), April 2025.
[^15]: IETF, [RFC 9162: Certificate Transparency Version 2.0](https://www.rfc-editor.org/rfc/rfc9162.html), December 2021.
[^16]: Sigstore, [Overview and Rekor transparency log](https://docs.sigstore.dev/).
[^17]: Transparency.dev, [Tessera](https://github.com/transparency-dev/tessera).
[^18]: W3C, [Verifiable Credentials Data Model v2.0](https://www.w3.org/TR/vc-data-model-2.0/), Recommendation, 15 May 2025.
[^19]: OpenTelemetry, [Generative AI semantic conventions](https://opentelemetry.io/docs/specs/semconv/gen-ai/).
[^20]: OWASP, [Top 10 for Agentic Applications 2026](https://genai.owasp.org/resource/owasp-top-10-for-agentic-applications-for-2026/), 9 December 2025.
[^21]: Microsoft, [Agent Governance Toolkit](https://opensource.microsoft.com/blog/2026/04/02/introducing-the-agent-governance-toolkit), 2 April 2026.
[^22]: Ethereum Improvement Proposals, [ERC-8004: Trustless Agents](https://eips.ethereum.org/EIPS/eip-8004).
[^23]: Infocomm Media Development Authority (IMDA), Singapore, [Model AI Governance Framework for Agentic AI](https://www.twobirds.com/en/insights/2026/singapore/singapore-introduces-new-model-ai-governance-framework-for-agentic-ai), January 2026.
[^24]: NIST, [Announcing the AI Agent Standards Initiative for Interoperable and Secure AI Agent Ecosystems](https://www.nist.gov/news-events/news/2026/02/announcing-ai-agent-standards-initiative-interoperable-and-secure), 17 February 2026.
[^25]: T. Farley, [Signed Decision Receipts for Machine-to-Machine Access Control](https://datatracker.ietf.org/doc/draft-farley-acta-signed-receipts/), IETF Internet-Draft, 2026.
[^26]: IETF, [AI Agent Authentication and Authorization](https://www.ietf.org/archive/id/draft-klrc-aiagent-auth-00.html), Internet-Draft, 2026.
[^27]: J. Figuera, [Sello: A protocol for receiver-attested confidential receipts for AI agent actions](https://github.com/juanfiguera/sello), reference implementation, 2026.
[^28]: T. South, T. Hardjono, R. Mahari, et al., [Authenticated Delegation and Authorized AI Agents](https://arxiv.org/abs/2501.09674), arXiv preprint, 2025.

## Changes from the May 2026 Concept Paper

This draft (2.1) replaces the May 2026 concept paper (v2.0) with a materially different design, not just an edit pass:

- **Data model:** a single attestation record per action → an append-only lifecycle of separately-signed events (Section 5), because one party's signature can't attest to what another party did.
- **Claims language:** "non-repudiation" as an achieved property → "evidentiary strength," with an explicit list of what a verifier must *not* infer (Section 4.2).
- **Infrastructure stance:** a specific blockchain named as the reference on-chain implementation → infrastructure-agnostic, with IETF SCITT + Tessera as the preferred neutral path and "no chain is privileged by this specification" (Section 8).
- **New sections with no v2.0 equivalent:** design principles (§2), threat model (§4), privacy/retention (§9), conformance profiles (§11).
- **Governance posture:** a declared roadmap and AAIF submission target → a bootstrap sequence that defers standards-body engagement until after independent implementations and a pilot exist (§13).
- **Removed from the spec itself** (candidates for a separate outreach/pitch document, not the technical draft): market-timing statistics (stablecoin volume, foundation membership growth), a fixed quarterly roadmap, and the author biography.
