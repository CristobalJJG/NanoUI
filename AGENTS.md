# NanoUI monorepo (container of 3 git submodules)

This repo contains three independent submodules, each a separate npm project with its own `node_modules`, `package.json`, scripts, and `AGENTS.md`. There is **no root `package.json`**, no root build/test/lint, and no CI at this level.

## Submodules

| Path | What it is | Library version it consumes |
| --- | --- | --- |
| `NanoUI-components` | Angular 22 component library, published as `@nanojjg/nano-ui-components` | — |
| `NanoUI-storybook` | Storybook 10 for the library (no Angular app) | `@nanojjg/nano-ui-components` from npm |
| `NanoUI-web` | Angular demo app + Storybook | `@nanojjg/nano-ui-components` from npm |

## Commands — run inside each submodule, never at root

There is no root `npm install`. Install and build in each submodule separately:

```bash
cd NanoUI-components   && npm ci && npm run build
cd NanoUI-storybook    && npm ci && npm run storybook   # port 6006
cd NanoUI-web          && npm ci && npm start            # port 4200
```

Each submodule has its own `AGENTS.md` with exact commands, conventions, and gotchas. Read those before making changes; this file only covers cross-repo facts.

## Cross-repo gotchas (easy to miss)

- **The library is consumed from npm, not from local source.** `NanoUI-storybook` and `NanoUI-web` import `@nanojjg/nano-ui-components` from the published package. After changing the library, publish a new version (see Releasing) and bump the dependency in the other submodules before testing there.
- **Import from the package root only:** `import { NanoButtonComponent } from '@nanojjg/nano-ui-components';`. Deep paths and the `@nano/...` scope do not exist.
- **Each submodule pins TypeScript `~6.0.2` and Angular `^22.2.0`.** Do not mix versions across submodules.

## Releasing (library → consumers)

1. In `NanoUI-components`: `npm version patch --no-git-tag-version`, commit `package.json` + `package-lock.json`.
2. Tag `v0.2.6` (must match `package.json` version) and push `main` + the tag.
3. The `.github/workflows/publish.yml` workflow builds with ng-packagr and publishes via OIDC trusted publishing (no `NPM_TOKEN`; needs `id-token: write` and the `npm-publish` environment).
4. In `NanoUI-storybook` and `NanoUI-web`: bump the `@nanojjg/nano-ui-components` dependency and `npm install`.

The workflow fails if the tag version does not match `package.json`. Versions/tags cannot be reused — npm rejects already-published versions.

## Submodule AGENTS.md (authoritative per-package guidance)

- `NanoUI-components/AGENTS.md` — public API wiring (`src/public-api.ts` → `src/lib/components/index.ts`), component conventions, `ng-package.json`/`tsconfig` quirks, publishing.
- `NanoUI-storybook/AGENTS.md` — Storybook commands, the three settings that keep component metadata alive (`optimizeDeps.include`, `mode: 'development'`, `enableProdMode: false`), typecheck with `npx tsc -p .storybook/tsconfig.json --noEmit`.
