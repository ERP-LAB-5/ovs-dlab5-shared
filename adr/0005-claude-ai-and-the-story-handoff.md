# ADR-0005: claude.ai and the story handoff

Status: **Accepted**

## Context

Work on a migration starts in conversation: what a buyer should see, at
which step of the process, and how anyone would know it works. That
conversation often happens in a chat in claude.ai, away from what the
organization knows, and its result reaches OntoVibeShift only if someone
types it in again.

## Decision

- **A remote MCP server in the cloud** that claude.ai connects to as a
  custom connector. The person signs in with their own OntoVibeShift
  account; the chat then acts as them, with exactly their access, and the
  server has no rights of its own.
- **It reads, and writes two things.** It reads the organization's
  workspaces, process steps, knowledge, stories, decisions, issues and
  documents. It writes a user story, always as a **draft**, and an issue.
- **A person's acts stay on the site.** Approving a plan, accepting a story,
  running a migration, managing people and using a connection are refused
  for the chat's sign-in, wherever they are asked for.
- **A skill for claude.ai**, `ovs-story-handoff`, shapes a story in the
  organization's form (for whom, what, why; the process steps; what it is
  accepted by; how it is tried), shows it, and hands it over only after the
  person confirms.
- **The story is the unit of work.** It goes from the organization's backlog
  to a workspace, and the edge draws it on the workspace's Scenarios map,
  solid once a person has accepted it.

The decision in detail is ovs-dlab5-net's ADR-0025; the story itself is its
ADR-0020.

## Consequences

- A story written in a chat is in the backlog, with its author, in seconds.
- One more public endpoint, whose boundary is the person's sign-in; it is
  part of the security audit before production.
- Connecting claude.ai needs an operator once per deployment, and each person
  signs in once.
