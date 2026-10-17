# webapp-dm-vue

EASY Digitization Management web client: Vue 3, Vuetify 3, Vuex 4, vue-router
5 (file-based routes), vue-i18n, built with Vite and tested with Vitest.
`README.md` has the full project notes; this file is the short version agents
work from.

## Setup

A fresh checkout (including a git worktree) needs only:

```bash
yarn install
```

Node `>=20 <=24` (see `engines` in `package.json`). Use yarn, never npm.

## Dev server

```bash
yarn serve --port <port> --strictPort
```

Runs `vite --mode development` against the backend in `.env.development`. Every
page except the login page needs a signed-in user against that backend, so an
agent without credentials can only look at the login page. Don't sign in and
don't submit forms. Check component behaviour with unit tests instead.

## Validation

Run these for the areas you changed, and lint only the files you changed:

```bash
yarn test:unit                       # all unit tests (vitest run)
yarn test:unit tests/unit/<path>     # a single spec or folder
npx eslint <file> [<file> ...]       # ESLint on the given files
npx prettier --check <file> ...      # formatting check
yarn build                           # production build
```

Lint with `npx eslint <files>`, not `yarn lint`: the `lint` script may already
name whole folders, which lints the entire repo, where existing errors are
unrelated to your change. Never run `yarn format`, `--fix`, or anything else
that rewrites files across the repo.

## Layout

- `src/pages/` – routed pages (file-based routing, see `typed-router.d.ts`).
- `src/components/{app,base,entity}/` – prefixed `A*`, `B*`, `E*`; root-level
  components are named after the business object (`TraitSelect`).
- `src/features/<feature>/` – self-contained features (`api`, `components`,
  `composables`, `i18n`, `pages`, `router`, `utils`).
- `src/api/` – backend clients; `src/api/auth/` is login and tokens.
- `src/state/` – Vuex modules. `src/composables/` – shared composables.
- `src/i18n/{de,en}.json` and `src/features/*/i18n/{de,en}.json` – every new
  string goes into both German and English.
- `tests/unit/` mirrors `src/`; store mocks live in `tests/unit/mocks/state/`.

## Conventions

Read before changing code in an area:

- `docs/best-practices.md` – index of the JS and Vue/Vuetify rules, with
  details in `docs/best-practices/{general,vue}/`.
- `.cursor/rules/*.mdc` – the same rules in compact form, including defensive
  code (normalize at boundaries).
- `README.md` – import order (external → internal aliases → components, with
  blank lines between groups), naming and what to unit test (stateless UI
  components, utils, composables; not app or page components).

Use the `b-*` base components rather than raw `v-*` Vuetify components where a
base component exists.

## Git

The default branch is `master`. Work reaches it only through Bitbucket pull
requests; branches are named `feature/NDMS-<ticket>-<slug>`. Never push to
`master`.

## Software factory

`/factory <feature>` runs the spec → build → review → approve loop in
`.claude/skills/factory/SKILL.md` with the agents in `.claude/agents/`. See
`factory/README.md`.
