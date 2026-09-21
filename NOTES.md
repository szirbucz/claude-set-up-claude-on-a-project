# NOTES.md

## CLAUDE.md

I kept it to four parts: a one-line description, the commands I actually run
(`npm run dev`, `npm test`, `npm run lint`, plus how to run a single test
file), an architecture note on how a request flows from `server.js` through
`routes/` to `db/store.js`, and two conventions (data access only through
`db/store.js`; new resources get their own router file). I included the
`require.main === module` guard in `server.js` because it's non-obvious from
skimming the file alone and explains why `tests/` can `require("../server")`
without opening a real port.

I left out the course README's task checklist (that's process, not code
guidance), the contents of `.env.example` (no real secrets to document, and
not something Claude needs repeated back to it), and anything a reviewer
could get from a `git log` or `ls routes/` in ten seconds. Shorter felt more
useful than complete — a long file is one Claude (and I) will skim past.

## .claude/settings.json

- **Allow**: `npm test`, `npm run lint`, `npm run dev`, `node --test`, and
  read-only git commands (`status`, `diff`, `log`). These are safe, run
  constantly, and get in the way if they prompt every time.
- **Ask**: `git commit`, `git push`, `npm install`. Not dangerous enough to
  block outright, but worth a pause — `npm install` runs arbitrary package
  install scripts, and commits/pushes change shared history.
- **Deny**: reading `./.env` (real secrets would live there, even though it's
  git-ignored — no reason Claude should ever need it), `git push --force`,
  `git reset --hard`, and `rm -rf`.

Without the deny rule on `.env`, an agent debugging a config issue could read
real credentials into context and, depending on what it does next (logging,
pasting into a commit message, calling an external tool), leak them. The rule
costs nothing since `.env` is never needed to run or test this app — `PORT`
is the only variable in use, and it defaults sensibly without one.

I deliberately scoped the deny to `./.env` exactly, not `./.env.*`, so
`.env.example` — which has no secrets and exists to be read — isn't blocked.
