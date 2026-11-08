# Toybox Tanks playtest

Isolated publication of a selected feature branch from
[ratatwiskr/toybox-tanks](https://github.com/ratatwiskr/toybox-tanks).
The production repository and its Pages workflow are not modified.

Expected playtest URL, pending Pages setup and a successful deployment:
https://ratatwiskr.github.io/toybox-tanks-playtest/

Production remains at https://ratatwiskr.github.io/toybox-tanks/.
One selected build is published here at a time, not a separate site per PR.
All published content is public. Do not publish secrets or private assets.

## One-time owner setup

1. Open this repository's Settings > Pages. Under Build and deployment, select
   GitHub Actions as the source. Do not select publishing from a branch.
2. In Settings > Environments, inspect/create `github-pages`. Restrict deployment
   branches to `main`. Add a required reviewer if desired and available.
3. Confirm Actions is allowed to use the official checkout, setup-node,
   upload-artifact, upload-pages-artifact, configure-pages and deploy-pages actions.

No PAT, GitHub App, cross-repository secret, custom domain, or gh-pages branch is
needed. The workflow reads public game source and publishes using this repository's
short-lived GITHUB_TOKEN/OIDC permissions. Do not paste credentials into chat.
Settings and a successful live deployment are not established by committing files.

## Publish a branch for device testing

1. Open Actions > Publish playtest > Run workflow.
2. Keep the workflow branch set to `main` in this publishing repository.
3. Enter the source branch from `ratatwiskr/toybox-tanks`, for example
   `feature/solo-touch-controls`. This is not a PR number, fork or arbitrary URL.
4. Run it. The selected branch is resolved once to an exact game commit. Later
   branch pushes do not change that run; publish again to test a newer revision.
5. Wait for `build`, `deploy` and `smoke` to succeed. Open the Pages URL from the
   run summary on your phone/tablet. Bookmark that stable URL for later tests.
6. If this is an older branch, ensure it contains the existing release scripts
   and Pages smoke test. Missing scripts or failed checks block publication;
   do not weaken checks or silently substitute a different source revision.

The workflow runs `npm run verify` and `npm run test:browsers`, then uploads only
the production `dist` output, plus `deployed-commit.txt` and `playtest-source.json`.
The latter records source repository, branch, exact SHA and publishing run URL.
The game SHA differs from this publishing repository's workflow SHA.

The build job has read-only repository permissions and no publishing secrets.
Only the separate deployment job has Pages write/OIDC permissions; it does not
checkout or execute game source. The live smoke job is read-only.

Only trusted branches in the owner's public source repository should be selected.
The branch's package scripts/tests execute on a GitHub runner; this is not a
sandbox for hostile code, independent audit of branch checks, or fork-PR service.
Do not add pull_request_target execution or publishing credentials to the build.

## Verification, concurrency and recovery

- Runs are serialized across the entire workflow, including live smoke. Active
  runs are not cancelled. GitHub may replace an older pending run with a newer
  pending run; do not expect every queued request to publish.
- A failed build does not deploy. A failed live smoke does not roll back an
  already published build. Inspect artifacts and republish a known-good branch
  if needed; there is no automatic rollback or retained per-commit site.
- A moving branch is published at its resolved SHA, even if the branch advances
  while the run is verifying. Check the marker/JSON when reporting a playtest.
- If the device appears stale, reload and check `deployed-commit.txt` or
  `playtest-source.json`, adding a query parameter to bypass a cached response
  when investigating. No service worker or cache-clearing guarantee is added.
- Production test hooks must remain absent, including with `?test=1`. Existing
  live smoke checks the source SHA, loading, solo/co-op start and pause/resume.
- Phone multitouch, scroll suppression, layout/readability and controller feel
  still need physical testing. Automated WebKit is not Safari/iOS certification.
- Publishing incomplete feature code does not implement missing controls.

## Implementation evidence and local-agent handoff

Prepared 2026-11-08 using GitHub connector reads. The source package scripts and
`tests/release/pages.smoke.ts` were inspected on source `main`. No local npm,
actionlint, browser or release commands were run. The workflow is authored, not
verified, and no Pages setting, deployment or successful smoke is claimed.

For OpenCode or another execution-capable reviewer: inspect current files and
applicable instructions, validate YAML/shell expressions with installed tooling,
and report exact commands, exit statuses and blockers. Inspect the first Actions
run and fix narrowly without reducing game gates. Do not install missing tools,
change production, grant broader permissions, commit, push or deploy without
separate authorization. Preserve any owner changes.

Acceptance: selected source SHA passes existing gates, the new site serves that
SHA, live smoke passes, the production site remains unchanged, and the owner
performs a short real-device test. None is inferred from repository creation.
