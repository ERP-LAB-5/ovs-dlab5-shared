# Roadmap

Where OntoVibeShift is heading: what is done, what is next, and what is only
an idea. Each step links to the decision behind it.

## Now: v1 on ovs.dlab5.net

- A simpler cloud: proxy, gate and store, with one write path to Shopify
  ([ADR-0003](adr/0003-v1-the-cloud-is-proxy-gate-and-store.md)). Done on stage.
- Organizations over workspaces, with a shared second brain and one edge
  per organization ([ADR-0004](adr/0004-organizations-over-workspaces.md)).
  Done on stage.
- User stories, shaped in claude.ai and handed over through the cloud's MCP
  server, drawn by the edge on the Scenarios map
  ([ADR-0005](adr/0005-claude-ai-and-the-story-handoff.md)). Stories done on
  stage; the MCP server next.
- A security audit of the cloud's public surface, then the go-live: the
  pilot organization copied from stage to production with the same ids.

## Next

- Align the cloud and the edge with the D-LAB-5 templates
  ([ADR-0002](adr/0002-a-cloud-and-edge-product-on-the-templates.md)):
  1. both repositories load the d-lab-5 plugins and record their template;
  2. an edge links with the device flow;
  3. the cloud serves the edge API;
  4. the edge takes the edge layer.
- Diagrams of organizations, the single write path and the story handoff.

## Later

- The lifecycle phase runners.
