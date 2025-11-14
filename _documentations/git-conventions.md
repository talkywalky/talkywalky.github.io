---
layout: documentation
title: "Git Conventions"
slug: git-conventions
short_description: "Branch naming, commit messages, PRs, tags, and everyday best practices."
last_update: 2025-11-14
---

## Why conventions?

Consistent names and messages make it easier for everyone to search history, review changes, automate tooling, and ship
faster. This page gives you clear, copy‑pasteable rules that beginners can follow and teams can enforce.

## Quick rules at a glance

- Branch names: `type/short-topic` in lower‑case kebab case.
    - Examples: `feat/login-otp`, `fix/build-timeout`, `feat/PROJ-123-login-otp`
- Commit messages: Conventional Commits (e.g., `feat: add primary button`). Keep subject ≤50 chars, imperative mood. Add
  details in body. Reference issues in footer.
- PR titles: mirror the main commit style. PR description: what/why/how + closes issues.
- Tags/releases: Semantic Versioning (`vMAJOR.MINOR.PATCH`, e.g., `v1.4.2`).
- Merge vs rebase: keep history clean. If you followed our Git setup, pulls use rebase by default (`pull.rebase=true`).
  Never rebase `main` or any shared/public branch.

## Branch naming

Recommended format:

```text
<type>/[ISSUE-]<short-topic>
```

Where:

- `type` is one of: `feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`, `build`, `ci`, `chore`, `revert`
- Optional `ISSUE` is your tracker ID, e.g., `PROJ-123` or `#456`
- `short-topic` describes the work in 2–5 words, lower‑case kebab case (e.g., `login-otp`)

Examples:

- `feat/login-otp`
- `fix/payment-retry`
- `feat/PROJ-123-login-otp`
- `chore/dep-updates-2025-11`

Rules and tips:

- Only letters, numbers, `-`, `_`, and `/` as separators.
- Avoid very long names; keep under ~50 characters.
- Do not end with `/` or `.`; do not use spaces; do not start with `-`.
- Avoid special sequences Git forbids (e.g., `..`, `@{`, a trailing `.lock`).

Validation pattern (starter; tune for your CI):

```text
^(feat|fix|docs|style|refactor|perf|test|build|ci|chore|revert)(/[a-z0-9]+[a-z0-9._-]*)+(-([A-Z]+-\d+|#\d+))?$
```

Common mistakes (don’t do these):

- `Feat/LoginOTP` (uppercase and camel case)
- `feat//double-slash` (double `/`)
- `feat/ends-with-slash/`
- `bugfix/missing-type` (type not in the list)
- `feat/contains space`

Useful commands:

```bash
# Create and switch to a branch
git checkout -b feat/login-otp

# Rename current branch
git branch -m feat/login-and-register

# Delete local branch (after merged)
git branch -d feat/login-otp
```

## Commit messages (Conventional Commits)

Structure:

```text
<type>: <short, imperative subject>
```

- `type`: same list as branches (`feat`, `fix`, `docs`, ...)
- Subject line: ≤50 chars, imperative mood, no period at end (e.g., `add button`, not `adds` or `added`).

Examples:

```text
feat: add primary button
```

```text
fix: handle 429 rate limit from payment provider
```

Helpful setup: commit message template

1. Create `~/.gitmessage` with this content:

```text
# type: short imperative subject
#
# Types: feat | fix | docs | style | refactor | perf | test | build | ci | chore | revert
# Keep subject <= 50 chars, no period at end. Use imperative mood.
```

2. Tell Git to use it by default:

```bash
git config --global commit.template ~/.gitmessage
```

Amending a message (safe):

```bash
git commit --amend
# edits the last message; use only if not yet pushed/shared
```

## Pull Requests (PRs)

- Title mirrors commit style: `type: short summary`.
- Description explains What, Why, How. Link issues and checklist.
- Keep PRs small and focused. Prefer a few well‑named commits over one giant commit.

PR template (you can copy this into `.github/pull_request_template.md`):

```text
Title: type: short summary

What
- ...

Why
- ...

How
- ...
```

Tip: for GitHub to auto‑close issues, the `Fixes #123` (or `Closes #123`) line must be in the PR description or the
commit message that lands on the default branch.

## Tags and releases (SemVer)

Use Semantic Versioning:

- `vMAJOR.MINOR.PATCH` (e.g., `v1.4.2`)

Commands:

```bash
# Create an annotated tag
git tag -a v1.4.2 -m "v1.4.2"

# Push tags
git push --tags

# Delete a tag (local)
git tag -d v1.4.2
# Delete a tag (remote)
git push origin :refs/tags/v1.4.2
```

## Best practices

- Make small, atomic commits (one logical change per commit).
- Write clear messages; prefer adding context in the body over cryptic subjects.
- Reference issues in the footer or PR description.
- Keep feature branches short‑lived; sync with `main` regularly.
- Avoid `git push --force` on shared branches; if needed locally, use `--force-with-lease`.
- Merge vs rebase:
    - Pulls: if you followed our setup, `git config --global pull.rebase true` keeps history linear.
    - Only rebase your local, unpublished branches. Never rebase `main` or any shared branch.
    - Prefer using the platform’s Merge/Squash‑merge buttons to integrate features into `main`.

## FAQ

- I pushed the wrong message. What now?
    - If not shared yet: `git commit --amend` and force‑push with lease.
    - If shared: create a follow‑up commit; or revert and recommit with the right message.
- How do I rename a branch that I already pushed?

```bash
git branch -m feat/new-name
git push origin :old-name
git push -u origin feat/new-name
```

- Can I squash commits?
    - Yes. Prefer squashing via the platform UI on merge. Avoid rewriting history on shared branches.

## Further reading

- Conventional Commits: [https://www.conventionalcommits.org/](https://www.conventionalcommits.org/)
- Semantic Versioning (SemVer): [https://semver.org/](https://semver.org/)
- GitHub closing
  keywords: [https://docs.github.com/issues/tracking-your-work-with-issues/linking-a-pull-request-to-an-issue](https://docs.github.com/issues/tracking-your-work-with-issues/linking-a-pull-request-to-an-issue)
