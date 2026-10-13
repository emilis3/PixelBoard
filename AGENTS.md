# AGENTS.md

The process document for coding agents working in this repo (`emilis3/PixelBoard`, PixelBoard). It is the only process doc; the README stays slim. `CLAUDE.md` points here.

> Dev process only. What the app does, its stack and design live in the GitHub issues (MVP: #1), never here.

## 1. TL;DR
- GitHub Issues are the source of truth. The issue body is the spec.
- Re-read the live issue, post a `## Plan (Claude Code)` comment, and wait for Emilis Gin (emilis3)'s go before coding.
- Work lands on the tip of `staging`. Never push to `main`. Never force-push. Only Emilis Gin (emilis3) merges `staging` into `main`.
- Commit and push every completed change, with `Refs #N` / `Closes #N`.
- When done, post `## On staging: <sha>` on the issue, including what you could not check.
- After landing, swap `ready for dev` -> `in staging` on the issue and open or refresh the single `staging` -> `main` PR. Change no other labels; Chief Pixelboard owns the rest.
- Don't invent requirements; ask in the issue. Never commit secrets, `.env*`, keystores/signing keys, or `local.properties`.

Roles: **Claude Code** develops and lands work on `staging`, swaps `ready for dev` -> `in staging`, and opens or refreshes the single production PR. **Chief Pixelboard** writes and preps issues and owns all other labels; it writes no app code. **Emilis Gin (emilis3)** decides, gives the go, checks staging by hand, and is the only one who merges to `main`.

## 2. Source of truth
- The issue **body is the spec**. Comments are background; where a comment conflicts with or goes beyond the body, **the body wins**.
- Flag body-vs-label mismatches in your plan. If the issue and code disagree, implement the issue or ask.
- Reference images live in `docs/reference/` and are linked from the issue; the body wins over them.

## 3. Before you write code
1. Re-read the live issue (body, labels, comments).
2. Post `## Plan (Claude Code)`: files and approach, tests, anything unclear. End with "Waiting for a go before coding."
   - Add a `### Ideas to make it better` section: short, concrete improvements with trade-offs. Suggestions only: build exactly the body unless Emilis Gin (emilis3) approves an idea, in which case update the issue body first.
3. Wait for the go (it may come in Emilis Gin (emilis3)'s session; note that in your on-staging comment).
4. Implement the body as written.

## 4. Branches and delivery
- `main` = production, `staging` = landing branch. Push directly to `staging`; no feature branches unless the issue says so.
- Never push to `main`, merge the production PR, publish or edit releases, or force-push.
- One open `staging` -> `main` PR at a time. After landing, open it if none exists, or update its title/body to list every issue now on `staging` ahead of `main`. Emilis Gin (emilis3) merges it with a merge commit.
- After a production merge, the `post-prod-sync` Action moves `staging` to `main` and removes `in staging` from shipped issues.

## 5. Labels
Claude Code only swaps `ready for dev` -> `in staging` after landing. Chief Pixelboard changes every other label.
| Label | Meaning |
| --- | --- |
| `dev` | Implementable work |
| `bug` | Something is broken |
| `deferred` | Parked; not permission to implement |
| `blocked` | Blocked on another issue or step |
| `waiting` | Waiting on access or a decision |
| `ready for dev` | Prepped, final body approved by Emilis Gin (emilis3) |
| `in staging` | On `staging`, awaiting production merge |
| `security` | Security/credentials |
| `platform` | Infra/hosting/database |

Never have both `ready for dev` and `in staging` on one issue.

## 6. Environments
- Hosting: GitHub Releases (APK artifacts; no web hosting). `main` -> production (https://github.com/emilis3/PixelBoard/releases), `staging` -> staging (build artifacts from the Build & Test workflow on `staging`). Staging has no live URL; don't claim to have tested on a device unless you did.
- Env vars: document names only, never values; scope staging and production separately.

## 7. Before reporting it on staging
Run the Gradle build and tests (also under a non-Europe/Vilnius `TZ`) as the Build & Test workflow does; confirm no secrets were committed. The `## On staging: <sha>` comment lists changes, SHAs, checks with results, what you couldn't check, and what Emilis Gin (emilis3) must check by hand.

## 8. Testing
- Pin Java 21 / Gradle everywhere (Node not applicable). CI runs all checks on push/PR to `staging` and `main`.
- All pure logic gets unit tests; worked examples in the issue become tests. Tests never hit live services.
- Time zone: Emilis Gin (emilis3) is in `Europe/Vilnius`. Date/time behaviour must be stated in the issue and tested, including DST days.
- Don't add anything the issue doesn't ask for.

## 9. Secrets and server data
- No secrets in the repo. If one is committed, stop and tell Emilis Gin (emilis3) in the issue.
- Deploys never reset or overwrite server data. Shape changes bump a schema version and migrate in place. Staging and production data stay apart.
