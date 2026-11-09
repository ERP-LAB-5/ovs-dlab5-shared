# OntoVibeShift: shared documentation

The decisions, roadmap, diagrams of OntoVibeShift.
This repository is public and holds only what can be: structures and decisions,
never anyone's data (see [SECURITY.md](SECURITY.md)).

Read it at **https://docs.dlab5.net/erp-lab-5/ovs/**.

## Working on it

```bash
npm ci
npm run diagrams          # render the diagrams whose source changed (needs Archify)
npm run check             # shared.json, diagrams in step, site builds, nothing private
npm run serve             # build _site/ and serve it on http://127.0.0.1:8792/
```

**Adding a diagram:** write `diagrams/src/<name>.<type>.json`, list it once,
with what it explains, under a group in `diagrams/README.md` or under its ADR's
`## Diagrams`, then `npm run diagrams` and `npm run check`.

**Adding a decision:** `adr/NNNN-short-title.md`, with Context, Decision and
Consequences filled in. Numbers are never reused.

Made from [template-dlab5-shared](https://github.com/d-lab-5/template-dlab5-shared);
`copier update --trust` brings in its fixes.

## Licence

Documents and diagrams CC BY 4.0; scripts MIT. See
[LICENSE](LICENSE) and [NOTICE](NOTICE).
