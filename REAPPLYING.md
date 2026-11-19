# Keeping this fork on top of upstream

This repository is a fork of `op7418/dsh-guizang-design`. It is installed as a local link, so
the profile reads the code straight out of this checkout: edit a file here, restart DSH, and the
change is live. Nothing needs to be republished or reinstalled.

```bash
git clone https://github.com/erwindassen/dsh-guizang-design.git
cd dsh-guizang-design
npm install
dsh plugin --profile desktop add link:$PWD     # or --profile web
```

`ls -ld ~/.dsh/profiles/desktop/node_modules/dsh-guizang-design` must show a symlink to this
checkout, and the profile's `package.json` must contain `"dsh-guizang-design": "link:…"`.

## What the fork changes (13 files)

| File | Change |
| --- | --- |
| `skills/humanizer/SKILL.md`, `LICENSE` | vendored `blader/humanizer` v4.0.0 (skill name `humanizer`) |
| `skills/manifest.json` | source pin for the new skill (`b48c832f…`) |
| `lib/recipes.js` | `langs` on the humanizer tile, `HUMANIZER_TILES = 'per-language'`, the `humanizer` SKILLS entry, English `steps` for both workflows |
| `lib/skills.js` | YAML block-scalar parsing, English addendum for `humanizer`, `HUMANIZERS` set that skips `guizang_library_add` |
| `gallery/app.js` | `META.humanizer`, otherwise the tile falls back to the grey placeholder |
| `gallery/i18n.js`, `locale/*.json`, `package.json`, `README*.md`, `test/plugin.test.mjs` | copy, version, expected skill list |
| `tools/vendor-skills.mjs` | knows the new source, but do not run it — it re-pins every source from upstream `main` |

## When upstream releases a new version

```bash
git fetch upstream && git rebase upstream/main
npm install            # the checkout's own dependencies
npm test               # the suite must stay green
```

Then restart DSH. Conflicts, when they happen, land in `lib/recipes.js`, `lib/skills.js`,
`gallery/app.js` and `test/plugin.test.mjs`; the vendored `skills/` is usually untouched.

## When the plugin was updated from the DSH UI

The plugin market installs the npm tarball, which replaces the symlink with the upstream package.
The English tile then disappears silently — the gallery only lists skill names found in the
plugin's own `skills/` and `own-skills/` directories, so the upstream package simply has no
`humanizer` tile. Put the link back:

```bash
dsh plugin --profile desktop add link:$PWD     # from inside this checkout
```

## Checking without restarting the app

```bash
cd ~/.dsh/profiles/desktop/node_modules/dsh-guizang-design
pwd -P        # must print this checkout, not the profile's node_modules
npm test      # runs this fork's tests through the symlink
```

A browser reload is not enough: `lib/recipes.js` is host code, read once when DSH boots.
