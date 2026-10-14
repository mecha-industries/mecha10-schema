# mecha10-schema

Public mirror of the generated config schema + field-reference docs ("the
standard") from [`mecha-industries/mecha10`](https://github.com/mecha-industries/mecha10)
(LAB-4067).

`mecha10`'s own `schemas/*.schema.json` + `schemas/README.md` are generated
output (see that repo's `schemas/README.md` for how), verified fresh against
source in CI by `scripts/ci/schema-docs-check.sh`. Since the `mecha10` repo
itself is private, this repo exists purely so that content has a public,
fetchable home — e.g. for `mecha10-dashboard`'s public developer docs page
(LAB-4054) to fetch server-side.

**Do not hand-edit anything under [`schema/`](schema/).** It's synced
verbatim from `mecha10`'s `schemas/` directory by
[`scripts/ci/schema-mirror-publish.sh`](https://github.com/mecha-industries/mecha10/blob/main/scripts/ci/schema-mirror-publish.sh)
whenever that content changes on `mecha10`'s `main` branch. [`schema/SOURCE_COMMIT`](schema/SOURCE_COMMIT)
records the exact `mecha10` commit this mirror was generated from.

- Raw JSON Schemas: `schema/*.schema.json`
- Field reference: [`schema/README.md`](schema/README.md)

Licensed MIT, matching the source repo (see [`LICENSE`](LICENSE)).
