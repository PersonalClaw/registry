# PersonalClaw app registry

A list of community-built [PersonalClaw](https://github.com/personalclaw/personalclaw)
apps. PersonalClaw ships this repository's URL as a removable default git source, so
listed apps appear in the Store alongside any source you add yourself.

**This is a list, not a store and not an endorsement.** Every listing is contributed
by whoever wrote the app. The only automated judgement made here is a static scanner
dry-run whose verdict is published *with* the listing rather than used to quietly
curate it, and PersonalClaw's install-time gate still runs on your own machine.

## The files

| File | What it is |
|---|---|
| `app-registry.json` | the data. One object per listed app. |
| `app-registry.schema.json` | its JSON Schema. Generated — do not hand-edit. |
| `validate_registry.py` | the validator CI runs on every listing PR. |
| `CONTRIBUTING.md` | **the listing policy** — what gets listed, what this list does not do. |
| `DELISTING.md` | what gets a listing removed, and how long that takes. |
| `fixtures/` | three apps and four candidate documents that pin the policy's outcomes. |

## Listing an app

Open a PR adding one row to `app-registry.json`. Read
[`CONTRIBUTING.md`](CONTRIBUTING.md) first — it is short, and every rule in it is
enforced by CI rather than by review.

## What validation checks

Four things, on the rows a PR added or changed:

1. **Repo liveness** — `git ls-remote` reaches it, and it has a branch.
2. **Manifest fetch and parse** — a shallow clone carries an `app.json` that
   PersonalClaw's own parser validates, and the row's `types` and
   `permissions_declared` match what that manifest actually declares.
3. **License present** — a license file in the repo, a `license` in the manifest, and
   the row agreeing with it.
4. **Scanner dry-run** — PersonalClaw's `SkillScanner` over the clone at the
   `community` trust tier. `dangerous` blocks the listing and the rule that fired is
   recorded on the PR. `warning` and `low` are **displayed and never block**.

Each row's optional `signer` is **recorded and echoed** alongside those four, and is not
a fifth check: the registry cannot verify a signature and does not try, so a named signer
reads as a claim by the listing's author. A row that omits `signer` is published as "no
claim" — deliberately not the same fact as a declared `unsigned`. See
[`CONTRIBUTING.md`](CONTRIBUTING.md) rule 8.

"Dry-run" is literal. The scanner is static text inspection; neither it nor the
validator executes a single line of a listed repository. Nothing is skipped on error
either — an unreachable repo or an unparseable manifest is a *failed* validation with
a stated reason, never a pass.

The validator imports core's manifest parser and core's scanner instead of
reimplementing either, so what the registry checks and what your install gate checks
cannot drift apart.

## Licence

MIT — see [LICENSE](LICENSE). That covers this repository: the listing data, its
schema, and the validator. It says nothing about any **listed** app, each of which
carries its own licence — the validator blocks a listing whose repo has no licence
file, whose `app.json` declares none, or whose row disagrees with either.

## Regenerating the schema

```bash
python validate_registry.py --emit-schema > app-registry.schema.json
```

`validate_registry.py` is the authority; the schema file mirrors it for editors and
third-party tooling. Regenerate after any change to the field list, and after a core
bump that adds a capability type.
