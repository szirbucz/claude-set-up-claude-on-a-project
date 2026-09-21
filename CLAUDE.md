# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

Starter Express API for the Claude Code course projects — a small in-memory
REST API with a `/users` resource and a `/health` check.

## Commands

```bash
npm install       # install dependencies
npm run dev        # start the API on http://localhost:3000 (auto-restarts on change)
npm test           # run the test suite (Node's built-in test runner)
npm run lint       # check code style with ESLint
```

Run a single test file:

```bash
node --test tests/users.test.js
```

## Architecture

- `server.js` — entry point; builds the Express app, mounts routers, and starts
  listening only when run directly (`require.main === module`), so `tests/`
  can `require("../server")` and drive it with supertest against an unbound app.
- `routes/` — one router file per resource (`users.js`, `health.js`), mounted
  in `server.js` under `/users` and `/health`.
- `db/store.js` — the only data access layer; an in-memory array standing in
  for a real database. State resets on every restart.
- `tests/` — supertest-driven integration tests that hit routes through the
  exported `app`, not the individual handler functions.

## Conventions

- Data access goes through `db/store.js`; routes never touch the `users`
  array directly.
- New resources get their own file in `routes/` and are mounted in `server.js`.
