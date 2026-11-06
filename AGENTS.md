# Notes for coding agents

This is the Sumidoku project website, built with Nimbus (Astro). The
reference conventions from the Nimbus starter apply; the short version:

## Layout

- `astro.config.mjs` — site metadata (title, description, GitHub, edit
  pattern) and the sidebar definition. Keep `site` in sync with the real
  domain when one is chosen. (`.mjs`, not `.ts`: Astro loads JS configs
  natively and only falls back to the Vite module runner for TypeScript.)
- `src/pages/index.astro` — the landing page. Styles come from
  `src/styles/landing.css` (all selectors prefixed `.sm-landing`).
- `src/content/docs/docs/*.mdx` — the documentation pages. Frontmatter
  needs `title` and `sidebar.order`; the page H1 comes from `title`, so
  bodies start at `##`.
- `src/components/` — Header/Footer and the Nimbus UI registry. Components
  used inside MDX must be registered in `src/components.ts`.
- Brand tokens are the `--sm-*` custom properties in
  `src/styles/globals.css` (vermilion `#ff2f52` from the abacus mark).

## Rules

- Docs content is ported from the main Sumidoku repository's `docs/`
  directory. Port faithfully — never invent config keys, environment
  variables, flags or endpoints.
- Public writing records mechanisms and numbers, never local machine
  details: no hostnames, absolute paths, internal domains or IPs, and no
  personal attribution.
- Internal links are absolute from the site root (`/docs/configuration`);
  the `nimbus/internal-link` lint rule fails the build on broken ones.

## Commands

- `npm run dev` — dev server
- `npm run build` — production build in `dist/`
- `npm run typecheck` — `astro check`
- `npm run lint:docs` — prose + MDX lint
- `npx nimbus-docs check` — build-free preflight

## Environment notes

- Use npm (not pnpm): pnpm's isolated linker creates junctions that the
  DSH file sandbox cannot resolve, and npm's flat layout works everywhere.
  Ignore build scripts on install (`--ignore-scripts`) inside sandboxed
  sessions; esbuild/sharp/workerd ship prebuilds.
- On Windows, Vite's resolver probes network drives with `exec("net use")`
  at startup. Sandboxed agent sessions that block child-process spawning
  make that probe throw, which cascades into CJS dependencies failing to
  externalize ("require is not defined" from the module runner). Run
  `astro build`/`astro dev` with spawn permission (or outside the sandbox);
  it is not a bug in this project.
- Disable telemetry inside sandboxes: `ASTRO_TELEMETRY_DISABLED=1` (the
  default config dir is outside the writable workspace).
