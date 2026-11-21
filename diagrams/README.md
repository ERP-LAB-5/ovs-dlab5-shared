# Diagrams

Interactive drawings of how OntoVibeShift works, made with Archify. The site
shows them in its gallery at `/diagrams/`.

- `src/<name>.<type>.json`: the source, the only file to edit.
- `<name>.html`: the page rendered from it. Never edit it by hand.
- `src/rendered.json`: which source each page was rendered from.

`npm run diagrams` renders what changed (it needs the Archify skill in
`~/.claude/skills/archify`, or `$ARCHIFY`); `npm run check` says whether every
page is in step with its source and listed once.

A diagram is listed once, with what it explains: under one of the groups
below, or in the ADR it belongs to, under that ADR's `## Diagrams`. Each line
reads `- [Title](../diagrams/<name>.html): what it explains.` and becomes the
gallery card's text.

## End to end

- [From discovery to Map](../diagrams/discovery-to-map.html): What happens from connecting a system to the Map gate: the edge reads Odoo and Shopify without changing them, derives what each does and can do per B2C step, and a person allocates each step, an overlap as a decision.

- [Execute: from a plan to the shop, with two approvals](../diagrams/execution-approval.html): How a migration reaches the shop: the edge reads the picked products from Odoo and plans the writes, the cloud checks the plan and names it by its SHA-256, a person approves it, and only an approved plan is sent: first the products in DRAFT, then, after a second approval, the release that puts them on sale.

## Cloud

- [The cloud: one Amplify app per environment](../diagrams/cloud.html): Who gets in (invited users, a group per workspace, the edge's own app client), and how a request reaches its rows, its objects behind the proxy's group check, and a connection's secret.

## Edge

- [The edge: what runs on this PC](../diagrams/edge.html): The page, the chat that only proposes, discovery with its credential store, the work directory, and what goes to the cloud workspace and how.

## Models

- [Ontology layers and a workspace's outcomes](../diagrams/ontology-layers.html): The product T-Box (process, capabilities, system cores) and what one workspace holds against it: as-is, availability, to-be, decisions, customizations and mappings.

