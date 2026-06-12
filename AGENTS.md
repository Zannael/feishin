# AGENTS.md

## Setup
- Use `pnpm`; the repo has `pnpm-lock.yaml` and no npm/yarn lockfile.
- README says development was built/tested with Node `v23.11.0`.
- `pnpm install` runs `electron-builder install-app-deps` via `postinstall`, so dependency install may touch native Electron deps.

## Commands
- `pnpm run dev`: Electron dev server.
- `pnpm run dev:watch`: Electron dev server with main/preload HMR watch.
- `pnpm run dev:remote`: remote-control web app dev server from `src/remote`.
- `pnpm run start`: production preview with `electron-vite preview`.
- `pnpm run typecheck`: runs `typecheck:node` then `typecheck:web`.
- `pnpm run lint`: runs typecheck, ESLint, then Stylelint; use this as the broad verification command.
- `pnpm run lint-code` and `pnpm run lint-styles`: focused lint checks.
- `pnpm run build`: builds Electron bundles and the remote app, but not the standalone web app.
- `pnpm run build:web`: builds only the standalone web/PWA app.
- There is no `test` script or test runner config in `package.json`; do not invent test commands.

## App Layout
- Electron main process entry: `src/main/index.ts`; preload APIs live in `src/preload`.
- Main renderer app entry: `src/renderer/main.tsx`, app shell: `src/renderer/app.tsx`.
- Remote-control app entry: `src/remote/index.tsx`, app shell: `src/remote/app.tsx`, built to `out/remote`.
- Standalone web/PWA build uses `web.vite.config.ts`, root `src/renderer`, and outputs to `out/web`.
- Shared API normalization/types for Navidrome, Jellyfin, and Subsonic live under `src/shared/api`.

## Build And Aliases
- Electron build config is split across `electron.vite.config.ts` for main/preload/renderer, `remote.vite.config.ts`, and `web.vite.config.ts`.
- Path aliases are Vite/tsconfig-backed: `/@/main`, `/@/preload`, `/@/renderer`, `/@/remote`, `/@/shared`, `/@/i18n`.
- The React Vite plugin enables `babel-plugin-react-compiler` in `vite.react-plugin.ts`; avoid unnecessary manual memoization unless the surrounding code already uses it for a reason.
- Build artifacts are under `out/` and ignored by ESLint; packaged apps use `electron-builder.yml` and include `out/**/*` plus `assets/**`.

## Style And I18n
- Formatting is Prettier with 4-space indentation, single quotes, semicolons, trailing commas, and print width 100.
- ESLint enforces tab indentation for TS/TSX even though Prettier is configured for spaces; run the repo scripts rather than guessing formatting behavior.
- CSS Modules use `fs-[name]-[local]` class names and camelCase locals in all Vite configs.
- `pnpm run i18next` extracts renderer translation keys via `src/i18n/i18next-parser.config.js` and writes English output to `src/renderer/i18n/locales/en.json`.

## Environment Knobs
- Docker/web runtime config uses `PUBLIC_PATH`, `SERVER_NAME`, `SERVER_TYPE`, `SERVER_URL`, `SERVER_LOCK`, `REMOTE_URL`, `LEGACY_AUTHENTICATION`, and `ANALYTICS_DISABLED` as documented in `README.md`.
- First-run settings overrides use `FS_`-prefixed environment variables; the maintained list is in `docs/ENV_SETTINGS.md` and `src/renderer/store/env-settings-overrides.ts`.
