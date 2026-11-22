# ADR-0002: A cloud-and-edge product on the D-LAB-5 templates

Status: **Proposed**

## Context

OntoVibeShift has a cloud, ovs-dlab5-net, imported from the D-LAB-5 cloud
template, and an edge, ovs-dlab5-edge, made from the Python tool template
with an early copy of the edge layer added by hand. Neither records which
template version it follows, so neither can take the templates' fixes, and
the edge links to a deployment differently from every other D-LAB-5 edge:
it signs in as an agent account instead of using the device flow.

## Decision

OntoVibeShift follows the cloud-and-edge blueprint:

- the cloud records its template version and serves the template's edge API
  (device flow, version gate, A-Box sync, model downloads, installers);
- the edge is the Python tool template plus the edge layer, applied with
  Copier, so both update from their templates; the hand-made copies go;
- each repository declares the d-lab-5 plugins its sessions load.

**For v1** ([ADR-0003](0003-v1-the-cloud-is-proxy-gate-and-store.md)): an edge
keeps signing in as an agent account, now one per organization
([ADR-0004](0004-organizations-over-workspaces.md)). The device flow below
comes after v1.

**Open:** how an edge links. The proposal is the device flow, which puts no
password on the edge and revokes one edge at a time. It replaces the
agent-account sign-in once the cloud serves it; the command-line client keeps
its own sign-in.

## Consequences

- An edge made for OntoVibeShift speaks the same wire as the Mediathek's and
  DigitalHome's edges, and gets the layer's fixes with `copier update`.
- The cloud gains public endpoints (device authorization, token, link); they
  get a security audit before they reach production.
- The OntoVibeShift-specific parts of the edge (which deployments it may
  talk to, its lifecycle engine) stay in the tool's own modules and in
  `edge/settings.py`, where an update does not touch them.
- The work is done in phases, each a pull request; the steps are in RUNME.md.
