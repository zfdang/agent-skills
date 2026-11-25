---
name: github-workflow
description: >
  Manage the complete GitHub development workflow using Conventional Commits
  conventions for branches, commits, and pull requests. Automatically update
  the default branch before starting new work, create appropriately named
  branches, commit and push completed work, and create draft pull requests or
  update existing pull requests. Leave marking ready and merging to a human.
---

# GitHub Development Workflow

Follow this workflow whenever implementing a GitHub task that involves
branches, commits, pushes, or pull requests.

The goal is to produce clean, conventional Git history and GitHub artifacts
without exposing implementation tooling or agent identity.

## Core principles

1. Follow repository-specific instructions first.
2. Follow existing repository conventions when they are compatible with this
   workflow.
3. Use Conventional Commit conventions consistently for:
   - branch names
   - commit messages
   - PR titles
4. Keep GitHub artifacts focused on the software change.
5. Never mention coding agents, AI tools, models, or implementation assistants
   in branches, commits, PR titles, PR descriptions, or commit metadata.
6. Preserve the user's Git identity.
7. Create every new PR as a draft (`gh pr create --draft`). Only a human may
   mark it ready for review or merge it. Stop after pushing and creating or
   updating the PR.

---

# Draft Pull Requests and Human-Only Review Transitions

Every new PR must be created as a draft, including when implementation and
validation are complete. With APIs or connectors, set `draft: true` (or the
equivalent draft flag).

Only a human may move a draft PR to ready for review. Never run `gh pr ready`,
invoke a ready-for-review API mutation, use the browser to mark a PR ready, or
configure automation to do so. Do not convert an existing ready PR back to draft
unless the user requests it; preserve its review state when updating it.

# Human-Only Merging

Merging is exclusively a human action. Never perform a merge yourself, even
when asked to complete a release, publish an update, or handle the entire
workflow independently. If asked to merge, leave the PR open and tell the user
that they must perform the merge themselves.

- Never merge a PR through `gh pr merge`, GitHub APIs, connectors, or browser UI.
- Never enable auto-merge, add a PR to a merge queue, or configure automation,
  workflows, or another actor to merge on your behalf.
- Never use local `git merge` or push feature changes directly to the default
  branch to bypass the human PR merge step.
- Fast-forward-only pulls of changes already merged by a human remain allowed.
- After validation, pushing, and creating or updating the PR, report its URL
  and leave it open for human review and merging.

---

# Conventional Types

Use these types consistently:

    feat
    fix
    docs
    style
    refactor
    perf
    test
    build
    ci
    chore
    revert

Use the type that best describes the primary purpose of the change.

---

# Branch Naming

Branch names use:

    <type>/<kebab-case-description>

Examples:

    feat/passkey-authentication
    fix/expired-access-token
    refactor/runtime-lifecycle
    docs/deployment-guide
    test/api-error-handling
    chore/update-dependencies

For breaking changes, use:

    feat!/new-authentication-api

or, when repository conventions prefer it:

    feat/new-authentication-api

## Branch rules

- Use lowercase.
- Use kebab-case.
- Use one Conventional Commit type.
- Keep the description concise.
- Describe the work, not the tool performing the work.
- Do not include ticket numbers unless the repository convention requires them.

Never use agent/tool-specific prefixes or names.

Forbidden examples:

    codex/...
    codex-...
    claude/...
    claude-code/...
    agent/...
    ai/...
    openai/...
    cursor/...
    gemini/...

Do not mention any coding agent, AI tool, model, or assistant in a branch name.

---

# Commit Messages

Use Conventional Commits 1.0.0:

    <type>[optional scope][optional !]: <description>

Examples:

    feat(auth): support passkey authentication
    fix(api): handle expired access tokens
    refactor(runtime): simplify request lifecycle
    test(api): cover malformed requests
    docs: document deployment process
    chore(deps): update dependencies

## Commit subject

- Use imperative mood.
- Use lowercase after the colon.
- Do not end with a period.
- Keep it concise.
- Prefer no more than 72 characters.
- Describe the actual change.
- Do not use the user's request or issue title verbatim unless appropriate.
- Do not mention implementation tools or agents.

## Commit body

Add a body only when useful.

The body should explain:

- why the change was necessary
- important implementation decisions
- compatibility considerations
- migration considerations

Do not simply repeat the diff.

## Commit trailers

Do not add agent attribution.

Never add:

    Co-authored-by: Codex <codex@openai.com>

Never add:

    Co-authored-by: Claude <...>

Never add:

    Co-authored-by: <AI agent>

Never add:

    Created with AI.

Never add any equivalent attribution.

Do not modify:

    git config user.name
    git config user.email

Do not modify commit author or committer identity.

The user's configured Git identity must remain the identity of the commit.

---

# Pull Request Titles

PR titles use the same Conventional Commit format:

    <type>[optional scope][optional !]: <description>

Examples:

    feat(auth): support passkey authentication
    fix(api): handle expired access tokens
    refactor(runtime): simplify request lifecycle

The PR title should normally correspond to the primary change represented by
the PR.

Do not mention:

- coding agents
- AI
- models
- assistants
- development tools

in the PR title.

---

# Pull Request Description

Use this structure:

    ## Summary

    <Why this change is needed and what it accomplishes.>

    ## Changes

    - <Important change>
    - <Important change>
    - <Important change>

    ## Verification

    - <Test or validation performed>
    - <Test or validation performed>

Keep the description concise and focused on the software change.

Do not include:

- agent identity
- AI/tool attribution
- model names
- implementation assistant names
- internal prompting details
- conversation details
- statements about how the code was generated

Do not write phrases such as:

    Generated by Codex
    Created with Claude Code
    Implemented by AI
    Assisted by an AI agent
    Generated by an LLM

The PR should read as a normal engineering PR.

---

# Starting New Work

Before creating a branch, inspect the repository:

    git status --short
    git branch --show-current
    git remote -v
    git log --oneline -20
    gh pr status

Determine the default branch.

Prefer:

    main

If `main` does not exist, use:

    master

Before starting new work, update the default branch.

First:

    git fetch origin

Then:

    git switch main
    git pull --ff-only origin main

or:

    git switch master
    git pull --ff-only origin master

Never use a merge-based pull when updating the default branch.

The new feature branch must be created from the freshly updated default
branch.

Example:

    git switch main
    git pull --ff-only origin main
    git switch -c feat/passkey-authentication

---

# Existing Pull Request

Before creating a new branch, check whether the current branch already has an
open PR:

    gh pr view --json number,state,headRefName,baseRefName,url

If an open PR exists:

- Stay on the existing branch.
- Do not create another branch.
- Do not create another PR.
- Continue implementing the requested changes.
- Commit using Conventional Commits.
- Push to the existing remote branch.

The existing PR will automatically receive the new commits.

Workflow:

    modify
      ↓
    test
      ↓
    commit
      ↓
    push
      ↓
    done

---

# Completing New Work

When the implementation is complete:

1. Run appropriate tests.
2. Run linters and formatters when applicable.
3. Inspect the final diff.
4. Stage only files belonging to the task.
5. Create a Conventional Commit.
6. Push the branch.
7. Create a draft PR if no PR exists.

For a new branch:

    git push -u origin <branch>

Then:

    gh pr create --draft

After creating the PR, report its URL and draft status. Leave marking it ready
for review and merging it to a human.

---

# Completing Existing PR Work

If an open PR already exists:

1. Finish the requested changes.
2. Run appropriate validation.
3. Review the diff.
4. Create a Conventional Commit.
5. Push the branch.

Do not run `gh pr create`.

Do not create another branch.

Do not recreate the PR.

Leave the updated PR open for a human to merge. Never enable auto-merge.

---

# Atomic Commits

Each commit should represent one logical change.

For bug fixes, create one separate commit per independently identifiable bug,
with its own Conventional Commit message describing that specific fix. Include
tests and documentation for that bug in the same commit. Do not bundle fixes
for multiple independent bugs into a single commit, even when they share a
file or are addressed in the same task or PR. Shared prerequisite changes may
use a separate focused commit when needed.

For the same requested task or batch of review fixes, default to one branch
and one PR containing multiple focused commits. One bug per commit does not
mean one commit per PR: do not create a separate branch or PR for each bug or
commit. Split the batch into multiple PRs only when the user explicitly asks.
Reuse an existing open PR for that task and update its title and description
to cover the final scope. Before committing, check that the staged diff
addresses only the bug named in that commit message.

Before staging:

    git status --short
    git diff

Stage only relevant files:

    git add <specific-files>

or use:

    git add -p

Avoid:

    git add .
    git add -A

when unrelated user changes may exist.

Never include unrelated user changes in a commit.

---

# Git Safety

Never destroy user changes.

Do not use these commands unless explicitly requested:

    git reset --hard
    git clean -fd
    git checkout -- .
    git restore .

Never force-push unless explicitly requested:

    git push --force
    git push --force-with-lease

Do not modify the user's Git identity.

Do not automatically rewrite existing commits.

---

# Final Verification

Before committing, verify:

    git diff --cached

Confirm:

- only intended changes are staged
- commit type is correct
- scope is meaningful when present
- commit description is concise
- no agent/tool attribution exists
- no unauthorized trailers exist

After committing:

    git show -s --format=fuller HEAD
    git show -s --format=%B HEAD

Confirm that:

- the commit message follows Conventional Commits
- the expected Git identity is used
- no agent attribution exists

Before creating the PR, verify:

- branch follows `<type>/<description>`
- PR title follows Conventional Commit format
- PR description uses the required structure
- new PR creation uses `--draft` or the equivalent API flag
- no automated draft-to-ready transition is configured
- no agent/tool/model identity is mentioned

---

# Expected Workflow

## New task without an existing PR

    inspect repository
        ↓
    check working tree
        ↓
    fetch origin
        ↓
    update main/master
        ↓
    create <type>/<description> branch
        ↓
    implement changes
        ↓
    test
        ↓
    review diff
        ↓
    Conventional Commit
        ↓
    push to origin
        ↓
    create draft PR
        ↓
    report PR URL

## Existing PR

    inspect existing PR
        ↓
    continue on existing branch
        ↓
    implement changes
        ↓
    test
        ↓
    review diff
        ↓
    Conventional Commit
        ↓
    push to origin
        ↓
    done

Do not create another branch or PR.

---

# Identity and Attribution Policy

GitHub artifacts must describe the software change, not the software used to
produce the change.

Never expose or mention the identity of the coding agent or development
assistant in:

- branch names
- commit messages
- commit bodies
- commit trailers
- PR titles
- PR descriptions
- Git author information
- Git committer information

The Git history should appear exactly like normal human-maintained project
history.
