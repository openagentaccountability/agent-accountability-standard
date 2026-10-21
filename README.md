<div align="center">

# Agent Accountability Standard (AAS)

**When an AI agent acts across a company boundary, what proof should exist afterward?**

[![License: Apache 2.0](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](LICENSE)
[![Status](https://img.shields.io/badge/status-discussion%20draft-orange.svg)](SPEC.md#document-status)

*A discussion draft, not an adopted standard. [Read the full spec →](SPEC.md)*

</div>

---

## The problem

AI agents now execute transactions, move data, and control physical systems across company lines: one company's agent acting inside another company's environment. When something goes wrong, each side usually has a *different* record of what happened: one system logged an approval, another logged a task, a third logged a payment. None of these records were designed to line up with each other, and neither party can prove to an outside auditor that their version is the complete one.

**AAS proposes a shared answer:** a common format for recording who acted, on whose authority, what happened, and where the proof lives, one that works no matter which company built the agent or which protocol it ran on.

## The core idea

Instead of one record that claims to capture an entire action, AAS treats an action as a **chain of cryptographically signed events**, each added by whoever was actually in a position to know it happened. This is what makes it a form of **cryptographic attestation**, not just logging: each event is signed by its issuer, so anyone can independently verify who made a given claim and that it hasn't been altered since, without needing to trust whoever is storing the record.

```
authorization → dispatch → acceptance → execution → result
                                              ↓
                                   (or: failure, dispute, compensation)
```

Each event is signed by whoever issued it, and all events for one action share an ID so they can be linked together later, whether by an auditor, a counterparty, or either side of a dispute. No single party's signature can vouch for what someone else did; the chain is only as complete as what both sides actually contributed.

## Why not just use what already exists?

Good question. The honest answer is: use as much of it as you can. AAS isn't trying to replace MCP, A2A, OAuth, or payment protocols like AP2 and x402. It's trying to fill the specific hole none of them cover: **a portable record of what happened, that survives after the transaction, and that a party outside the original relationship can independently verify.**

Some things that make AAS worth a look next to adjacent work:

- **It's a lifecycle, not a snapshot.** Most comparable proposals sign one record per decision. AAS links the whole sequence, including what happens if it's later disputed or reversed.
- **It works across protocols, not inside just one.** It's designed to carry evidence through MCP, A2A, payment protocols, and physical control systems, not just one of them.
- **It covers physical agents too.** Robots and industrial systems get the same treatment as software agents, not a bolt-on.
- **It doesn't require picking a blockchain, a vendor, or a single log operator.** Bilateral logs, consortium logs, or public ledgers can all conform.

The [full spec](SPEC.md) goes into where this overlaps with related efforts (IETF drafts, an existing receipt protocol called Sello, delegation-credential research), including a direct argument for [why cryptographic attestation, not just logs →](SPEC.md#43-why-cryptographic-signing-is-the-required-baseline), and is upfront about what a signature can and can't actually prove. Worth reading before assuming this is a bigger claim than it is.

## What this is *not*

- Not a finished standard: no organization has adopted it, and it doesn't claim any endorsement.
- Not a replacement for existing identity, authorization, or payment protocols: it references their proofs rather than reinventing them.
- Not a legal opinion: it doesn't decide who's liable, or whether a signature counts as a legal signature in your jurisdiction.

## Get involved

- **Read the [full specification](SPEC.md)** for the threat model, the schema, and the open questions.
- **Look at the [draft JSON Schema](aas-action-event.schema.json).** It's an early stub and needs review.
- **[Open an issue](../../issues/new/choose)** if something's unclear, wrong, or missing.

## About

Authorship and Governance

The Agent Accountability Standard (AAS) was independently conceived and initially authored by Kay Yoo in 2026, through Naru Advisory.

The original specification is documented in the repository's commit history and initial tagged release, v0.2.1-draft.

AAS is currently maintained as an open-source discussion draft. External technical reviews, research perspectives, and contributions are welcome.

Contributors will receive appropriate attribution. Participation does not imply institutional endorsement or governance authority.

For contribution guidelines, see CONTRIBUTING.md.

For research inquiries or collaboration: kay@naruadvisory.com

## License

Apache License 2.0. See [LICENSE](LICENSE).
