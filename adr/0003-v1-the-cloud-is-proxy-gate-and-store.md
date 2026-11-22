# ADR-0003: v1, the cloud is proxy, gate and store

Status: **Accepted**

## Context

The edge, ovs-dlab5-edge, now does the work on a person's PC: it reads Odoo
and Shopify without changing them, models what each does, maps them, and
plans the migration. The cloud, ovs-dlab5-net, still carried what it had
before the edge existed, and Shopify could be written two ways: from the PC
with a key kept there, or by the cloud with the key it holds.

## Decision

For v1, the cloud keeps four jobs:

- **Proxy.** It holds every connection's key and makes the reads and writes
  the contracts allow. Every Shopify write goes through it; the PC keeps no
  key that could write.
- **Gate.** A person's approval of a plan is recorded there before anything
  is sent, and only a person, signed in on the site, can give it.
- **Second brain.** What an organization knows is kept there and shared by
  its people and its edges ([ADR-0004](0004-organizations-over-workspaces.md)).
- **Middleware for claude.ai.** A chat reaches the organization as the
  person using it, and hands work over as user stories
  ([ADR-0005](0005-claude-ai-and-the-story-handoff.md)).

What nothing used any more was removed: the cloud's own discovery, a delete
of stored files, and the edge's own Shopify writer.

The decisions behind each part are in ovs-dlab5-net's ADR-0023 to ADR-0025.

## Consequences

- One write path to audit, with the key where the contract is enforced.
- Shopify is written only while the cloud is reachable.
- Reading a system may still use a key on the PC; reads cannot change it.
- The edge links as an agent account for v1; the template's device flow
  ([ADR-0002](0002-a-cloud-and-edge-product-on-the-templates.md)) comes after.
