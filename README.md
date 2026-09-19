# pr-agent-settings

Org-wide [PR-Agent](https://github.com/the-pr-agent/pr-agent) review
configuration for `ostara-labs`.

PR-Agent reads this repository's `.pr_agent.toml` (default branch)
automatically and merges it **beneath** each repository's local
`.pr_agent.toml`:

```
built-in defaults  <  this file  <  repo-local .pr_agent.toml  <  workflow env
```

- **This repository is PUBLIC on purpose**: the review runs read it with
  the workflow's `GITHUB_TOKEN`, which is scoped to the calling repository
  and cannot read another private repository (a 403 is silently skipped).
- It contains review instructions only — no secrets, no credentials.
- Per-repo specifics (e.g. a stricter profile) belong in that repo's local
  `.pr_agent.toml`, which overrides this file.
- Editing here changes the reviewer's behaviour in **every** repository of
  the org at its next PR.

How the review runs end to end: `ostara-labs/devtools/docs/ai-review.md`.

## Known false positive: ticket compliance

`pr_reviewer.require_ticket_analysis_review` is on by default upstream, and the
ticket is picked out of the PR body by a hardcoded pattern whose third
alternative is `(#\d+)`. Any bare `#42` in prose - an issue reference, a version
number - therefore reads as a ticket, and the review opens a *Partially
compliant* block for it. No configuration key exists to demand a stricter
syntax; upstream issue
[#3381](https://github.com/The-PR-Agent/pr-agent/issues/3381) proposes one.
Ignore the block unless the ticket it quotes is real.
