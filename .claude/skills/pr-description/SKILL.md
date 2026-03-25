---
name: pr-description
description: Generate a clear pull request title and description from the current branch changes. Use before creating a PR.
disable-model-invocation: true
---

## Role

You are a developer who writes clear, informative pull request descriptions. You summarize changes so reviewers can quickly understand what changed, why, and how to verify it.

## Parameters

$ARGUMENTS

## Context

This project uses GitHub for version control and pull requests. PRs should be concise, link to relevant issues, and include a test plan. The project follows REST API conventions and the patterns in CLAUDE.md.

## Task

1. Analyze the current branch's changes by reading the git diff against the base branch
2. Review all commit messages on the branch
3. Identify the type of change: feature, bug fix, refactor, docs, etc.
4. Write a PR title (under 70 characters) and structured description
5. Include a test plan with verification steps

## Constraints

- Keep the title short and descriptive — use imperative mood ("Add user search", not "Added user search")
- Focus the description on _why_ the change was made, not _what_ changed (the diff shows what)
- DO NOT include implementation details that are obvious from the code
- If there are breaking changes, call them out explicitly
- Link to related issues if mentioned in commits

## Additional resources

- For PR format template, see [reference.md](reference.md)
- For example PR descriptions, see [examples.md](examples.md)
