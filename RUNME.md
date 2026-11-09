# RUNME: align OntoVibeShift with the D-LAB-5 cloud-and-edge architecture

**For Claude Code** on the machine where ovs-dlab5-net and ovs-dlab5-edge are
developed. Read all of it before starting. The person you work with decides
at every ⚑; stop there and ask.

## Where things stand

| Repository | What it is now | What it should become |
|---|---|---|
| ERP-LAB-5/ovs-dlab5-net (private) | a `-cloud`: imported from template-dlab5-cloud (then called template-dlab5-net) in commit `0e53f90758`, renamed in `8173c0ef3c`, with no record of which template version | a `-cloud` that records its template version and serves the template's **edge API** |
| ERP-LAB-5/ovs-dlab5-edge (private) | a `-lite` tool from python-tool-template v0.4.1, with **hand-made copies of an early edge layer** flat in the package (`cloud.py`, `sync.py`, `knowledge.py`, `jobs.py`, `chat.py`, `firststart.py`, `routes.py`, `mcp_tools.py`, `bridges/`), plus its own `cognito.py` and `targets.py` | a full **`-edge`**: the base plus the template-dlab5-edge layer, applied with Copier, so both update from their templates |
| ERP-LAB-5/ovs-dlab5-shared (this, public) | made from template-dlab5-shared; this file, one proposed decision | the product's public docs: decisions, diagrams, roadmap, at docs.dlab5.net/erp-lab-5/ovs/ |

The one real architecture question is **how an edge links to a deployment**
(⚑ phase 3). Everything else is alignment with templates that already exist.

## Rules for the whole job

- **Pull requests into `stage`, one concern each.** Commit and push only when
  the person asks; the person merges. `main` is promoted by pull request.
- **Nothing private in this repository.** It is public: no A-Box data, no real
  names, no account ids, ARNs, hostnames of cloud resources, keys or local
  paths. Name the private repositories, never link them. `npm run check`
  sweeps for most of it, not for a real name in prose.
- **Reports stay private.** A conformance or security report goes into
  ovs-dlab5-net's `docs/audits/YYYY-MM-DD-<kind>.md`, never here.
- **Production only with the person.** A deployment or a change to a live
  user pool is theirs to run.
- **Stop when the plan stops fitting.** If a step here is wrong for the code
  you find, say so and ask; don't force it.

## 0. Set up

1. Put the three repositories side by side, all on fresh `git pull`s:
   `ovs-dlab5-net/`, `ovs-dlab5-edge/`, `ovs-dlab5-shared/`.
2. Make sure git keeps executable bits on this machine:
   `git config --global core.fileMode true`. Without it, `run.sh` and
   `test.sh` were committed as plain files, and `./run.sh` failed on a fresh
   clone.
3. Install [Copier](https://copier.readthedocs.io) 9.4 or newer
   (`pipx install copier`), Node 22 and Python 3.11+.
4. Start Claude Code **in this repository**, with the other two added:
   ```bash
   cd ovs-dlab5-shared && claude --add-dir ../ovs-dlab5-net --add-dir ../ovs-dlab5-edge
   ```
   This repository's `.claude/settings.json` enables the d-lab-5 catalogue's
   plugins the job needs: `dlab5-house` (with `dlab5-repo-conformance` and
   `dlab5-security-audit`; it must be **0.3.0 or newer**), the cloud-edge
   blueprint, both templates' skills, `pytool-kit` and `erplab5-house`.
   Accept the catalogue when asked, then check with `/plugin` that they are
   installed. If `dlab5-repo-conformance` is missing, the catalogue's latest
   release is not merged yet: tell the person and stop.
5. Read before changing anything:
   - the skills `dlab5-cloud-edge-blueprint`, `dlab5-edge-template`,
     `dlab5-cloud-template` and `dlab5-repo-conformance`;
   - in d-lab-5/template-dlab5-cloud: `docs/specs/edge-cloud-api.md` (the wire
     between edge and cloud) and ADR-0006;
   - in d-lab-5/template-dlab5-edge: `template/docs/edge.md.jinja` (what the
     layer owns and what the tool owns);
   - ovs-dlab5-net's own `docs/adr/` (fifteen decisions; ADR-0002 Cognito
     only, ADR-0008 execute needs an approval, ADR-0012 connection secrets,
     matter most here) and `docs/agent-access.md`;
   - [ADR-0002](adr/0002-a-cloud-and-edge-product-on-the-templates.md) in this
     repository, proposed.

## 1. Assess both, read-only

```bash
node <dlab5-repo-conformance>/scripts/conformance.mjs ../ovs-dlab5-edge --online --run-checks
node <dlab5-repo-conformance>/scripts/conformance.mjs ../ovs-dlab5-net --online
```

Expect for the edge: the plugins missing from `.claude/settings.json`,
`run.sh` and `test.sh` not executable, `settings.local.json` not ignored, no
`stage`, no CLAUDE.md. For the net: no `.template.json`, no plugins. Write the
report to ovs-dlab5-net `docs/audits/`, and show the person the musts.

## 2. Conformance, no architecture yet

Small PRs; each repository's tests pass after each.

**ovs-dlab5-edge**
1. ⚑ Create `stage` from `main` and push it (the person agrees first).
2. On `conform/<date>` from `stage`:
   - `conformance.mjs ../ovs-dlab5-edge --fix settings` (plugins, `.gitignore`);
   - `git update-index --chmod=+x run.sh test.sh`;
   - a CLAUDE.md: what the tool is, its commands, what bites (start from
     `docs/` and the README; the template does not make one yet).
3. Re-assess with `--run-checks`: no must left. PR into `stage`.

**ovs-dlab5-net**
1. On `conform/<date>` from `stage`:
   - `conformance.mjs ../ovs-dlab5-net --fix settings`, then add
     `dlab5-cloud-template@d-lab-5` and `dlab5-cloud-edge-blueprint@d-lab-5`
     to the plugins if they are not there;
   - **`.template.json`**: find the template-dlab5-cloud commit the import
     was made from. Compare the tree of `0e53f90758` with the template's
     history before 2026-08-29 (`git rev-parse <commit>^{tree}` on both
     sides; the closest is the one with the smallest `git diff --stat`).
     Write `{"template": "d-lab-5/template-dlab5-cloud", "commit": "<sha>",
     "imported_in": "0e53f90758", "renamed_in": "8173c0ef3c"}`.
2. PR into `stage`.

## 3. ⚑ Decide how an edge links to a deployment

| | Today in ovs-dlab5-edge | The template's way |
|---|---|---|
| How | `cognito.py`: the edge signs in as an **agent account** (`agent-<slug>`, one workspace group) with USER_PASSWORD_AUTH, keeps the refresh token; `targets.py` resolves a deployment by name, behind a suffix allowlist | the **device flow** (RFC 8628): the edge shows a code, a workspace admin approves it on the cloud's `/link`, the edge gets a **device token** for that tenant, rotated 30 days before expiry |
| On the edge | a password typed once, a refresh token kept | no password ever; a 0600 token file |
| Revoking one edge | disable the account (and every edge sharing it) | revoke that device token, in Settings → Edges |
| Same wire as | OntoVibeShift's own CLI | the Mediathek edge and DigitalHome's edges |

Recommend the device flow, because it puts no password on the edge, can
revoke one edge without touching the others, and makes the edge a standard
one. Talk it through with the person against ADR-0002 (Cognito only) and
ADR-0010 (delegated invites) in ovs-dlab5-net. Then:

- update [ADR-0002](adr/0002-a-cloud-and-edge-product-on-the-templates.md)
  here to **Accepted** with the choice, or write a new ADR if the person
  chooses otherwise;
- if the agent-account sign-in stays for a while (the CLI uses it too), say
  until when and what removes it.

## 4. The cloud serves the edge API

In ovs-dlab5-net, on its own branch, after phase 2 is merged:

1. List what template-dlab5-cloud has for edges that ovs-dlab5-net lacks:
   the device-authorization and token functions, `/link`, Settings → Edges
   (link, revoke, installers), the version gate (426 below
   `MIN_EDGE_VERSION`), A-Box sync, model downloads. Diff the template from
   the commit in `.template.json` to its `main`, file by file under
   `backend/` and `packages/site/`.
2. Port them, mapping the template's **tenant** to OntoVibeShift's
   **workspace** (the Cognito group per workspace) and its **space** to
   whatever an A-Box belongs to here. Keep ovs-dlab5-net's own rules: the
   approval before execute (ADR-0008), credentials in Secrets Manager
   (ADR-0012), objects behind the proxy Lambda (ADR-0004).
3. Deploy to a personal sandbox only (`npm --prefix backend run sandbox:once`
   or this repository's equivalent); the person promotes.
4. Verify against `edge-cloud-api.md`: a device links, a revoked token gets
   410, an old edge gets 426. Add the checks to the repository's own
   `verify:*` scripts.
5. Run `dlab5-security-audit` (core and the amplify module) on the change.
   New public endpoints are exactly what it is for.

One PR per piece (the device flow; Settings → Edges; sync; models), each
with its own tests.

## 5. The edge takes the layer

In ovs-dlab5-edge, after phases 2 to 4 are merged and the tree is clean:

1. ```bash
   copier copy --trust --data-file .copier-answers.yml \
     -a .copier-answers.edge.yml gh:d-lab-5/template-dlab5-edge .
   ```
   `edge_kind` is `human`. Name the cloud and the nouns as OntoVibeShift
   says them (the cloud: OntoVibeShift; a tenant: a workspace). The layer
   writes `ovs_dlab5_edge/edge/`, and `scripts/edge-wire.py` wires it into
   `app.py`, `mcp_server.py`, the page, `pyproject.toml` and
   `.claude/settings.json`. Read what it says it could not wire.
2. **The flat copies go.** For each of `cloud.py`, `sync.py`, `knowledge.py`,
   `jobs.py`, `chat.py`, `firststart.py`, `routes.py`, `mcp_tools.py` and
   `bridges/`: diff it against its `edge/` counterpart. What is
   OntoVibeShift's own (targets, settings, anything the layer cannot know)
   moves to `edge/settings.py` or to the tool's own modules; a fix the layer
   lacks goes back to template-dlab5-edge as its own PR. Then delete the
   flat file and repoint the imports.
3. `cognito.py` and `targets.py`: as decided in phase 3.
4. `./test.sh`, `./run.sh`, then link to the sandbox cloud from phase 4 and
   sync one A-Box.
5. Re-assess: type **edge**, `--run-checks`, no must left. PR into `stage`.

From then on the edge takes fixes with
`copier update --trust` (the base) and
`copier update --trust -a .copier-answers.edge.yml` (the layer).

## 6. The docs, here

- Diagrams with Archify, sources in `diagrams/src/`, listed in
  `diagrams/README.md`: how the cloud and the edge work together; linking an
  edge (a sequence); A-Box sync (a dataflow); the lifecycle phases. Facts
  from the code, with file paths in the cards. `npm run diagrams`.
- The decisions that shape the product, as ADRs here; the internal ones stay
  in ovs-dlab5-net.
- `ROADMAP.md` and `site/landing.md` kept current.
- `npm run check` before every PR.

## 7. Publish

The person creates nothing new: this repository already exists. PR `stage`
into `main`. docs.dlab5.net lists it once a line is added to `sites.json` in
d-lab-5/d-lab-5.github.io; ask the person, who may hand that to another
session.
