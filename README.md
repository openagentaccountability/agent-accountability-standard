# Agent Accountability Standard

**The open standard for cross-organizational AI agent action attestation.**

---

## What this is

AI agents now act across organizational boundaries — executing transactions, accessing data, delegating to other agents — at a speed and scale that contracts were not designed to govern.

When an agent operated by Company A delegates to Company B, which delegates to Company C, there is no standard specifying what attestation record must exist, where it must be stored, or who can verify it when something goes wrong.

The **Agent Accountability Standard** defines that record.

It is not a communication protocol. It is not a runtime governance tool. It is the persistent, cross-organizational accountability layer that specifies what proof must exist when an AI agent takes a consequential action across an organizational boundary — payment or otherwise.

---

## The gap this fills

Existing protocols address adjacent problems well:

- **MCP / A2A / AGNTCY** — agent connectivity, messaging, and infrastructure
- **x402 / MPP** — how agents pay for things
- **AP2 (Google)** — authorization for agent-initiated purchase transactions
- **AIP / Microsoft AGT** — intra-organizational agent governance

None of them define the persistent, independently-verifiable attestation record that a regulator, auditor, or counterparty can examine after the fact, across organizational boundaries, for actions that are not payments.

That is the gap this standard fills.

---

## The technical specification

The Agent Accountability Standard is the formal name for the **CAAAS** schema — the Cross-Organizational Agent Action Attestation Schema.

A CAAAS-compliant attestation record captures who acted, on whose authorization, on what, and when — in a tamper-evident, non-repudiable form that travels with the agent action rather than staying inside any single organization's systems.

The full concept paper is available upon request: kay@naruadvisory.com

---

## Status

**Concept paper stage.** This standard is being submitted to the Linux Foundation Agentic AI Foundation (AAIF) for working group consideration.

We are actively seeking:
- Working group participants from agent protocol teams, regulated financial institutions, and Big Four assurance practices
- Technical review of the attestation schema
- Reference implementation collaborators for Path 1 (W3C DID + consortium log) and Path 2 (on-chain)

---

## How to engage

- **Open an issue** to comment on the schema, raise questions, or propose changes
- **Start a discussion** for broader working group conversation
- **Email:** kay@naruadvisory.com

---

## About

Initiated by **Kay Yoo**, founder of Naru Advisory. Former Senior Consultant, KPMG Financial Institutions Group. Former COO, ZTX. Former Director of Strategy & Ops, Origin Protocol.

This standard draws on a background in financial audit and assurance and direct experience operationalizing governance in cross-organizational, cross-jurisdictional, and decentralized environments.

---

## License

Apache License 2.0
