# ADR-0004: Organizations over workspaces

Status: **Accepted**

## Context

A workspace was the only level: one group of people, one store, one
knowledge graph. A business runs several projects that share what it knows
about its own systems: the connections and their keys, how its Odoo was
customized, what was already decided. With one level that knowledge is
copied into every workspace, or locked in one.

The D-LAB-5 cloud template has the pattern for this: a tenant, with its own
admins, holding spaces, one of them shared.

## Decision

- **An organization is the template's tenant.** It has its own admins.
  Operators create organizations; an organization's admins add its
  workspaces and its people.
- **A workspace is OntoVibeShift's name for a space.** It is `shared` (one
  per organization) or a `project`.
- **The shared workspace is the organization's second brain**: its
  knowledge graph, connections, documents, decisions and story backlog,
  read by every member. Plans and approvals are kept with the connections
  they use, there. A project workspace holds its own graph, documents and
  the stories given to it.
- **One edge per organization.** Its agent account reaches every workspace
  of the organization; the edge works in the shared one and keeps each
  workspace's graph in that workspace.
- **Reading needs no new rule.** An organization's admins are members of
  every workspace of it, so access is checked as before.

The decision in detail is ovs-dlab5-net's ADR-0024.

## Consequences

- A workspace made before organizations joins one with everything it holds.
- The same organization can exist on stage and on production with the same
  id, so content keyed by it is found on both.
- An organization always keeps at least one admin.
- Operators can still read content, unlike in the template: they run the
  migrations for the organizations they serve.
