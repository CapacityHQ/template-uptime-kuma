# Uptime Kuma, as a Capacity template

This project is Uptime Kuma 2.5.5 (upstream `louislam/uptime-kuma`, MIT): the self-hosted
uptime monitor and status page, prepared as a Capacity Desktop template. It is the owner's
project, to change as they see fit.

## How it runs here

- `npm run dev` starts the Vite dev server for the Vue 3 frontend on port 3000 and the Node
  server (`server/server.js`: Express, Socket.IO, the monitors) on port 3001 once port 3000
  answers. In development the frontend reaches the server at `<page hostname>:3001`. The
  preview shows port 3000. `npm run lint` runs upstream's ESLint and Stylelint.
- The server reads `.env`, copied from `.env.example` when the project was created:
  `UPTIME_KUMA_DB_TYPE=sqlite` skips the database setup page and `DATA_DIR=./data/` keeps the
  SQLite database, the uploads and the screenshots under `data/`, which is gitignored. Delete
  that folder to start from an empty instance.
- There is no seeded account: the first visitor creates the admin account on `/setup`. The
  session is a token in the browser's local storage.
- Schema changes are knex migrations under `db/knex_migrations/`, applied at every server
  start. Keep them additive: a deployed instance migrates while the previous version is still
  serving.
- Deploying from Capacity builds `docker/dockerfile` (target `release`): it installs the
  dependencies, builds the frontend into `dist/`, and runs `node server/server.js` under
  `dumb-init` with the data on a volume at `/app/data`. Its base images are pinned by digest;
  bump the digests when upstream rebuilds `louislam/uptime-kuma:base2` or `:builder-go`.

## Layout

`server/` is the Node server (routers, socket handlers, monitor types, notification providers,
models); `src/` the Vue app (pages, components, `lang/` translations); `db/` the SQLite
template and the migrations; `config/` the Vite and Playwright configuration; `extra/`
upstream's maintenance scripts; `test/` upstream's backend and end-to-end tests
(`npm run test-backend`, `npm run test-e2e`).

## Upstream

Upstream's contribution policy (`CONTRIBUTING.md`, and the `AGENTS.md` of
`louislam/uptime-kuma`) applies to pull requests sent to upstream, not to the changes made in
this project.
