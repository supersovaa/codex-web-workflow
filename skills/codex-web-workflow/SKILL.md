---
name: codex-web-workflow
description: Execute repository changes in the current Codex Cloud workspace, validate the resulting diff, and prepare a precise GitHub pull-request handoff through the Cloud task interface.
---

# Codex Cloud workflow

Use this skill when executing a repository-changing task in Codex Cloud.

## Follow the selected workspace

Work in the repository and task workspace selected by Codex Cloud.
Follow explicit task constraints and repository-specific governing instructions.

For a requested PR, use the explicitly specified target branch.
Otherwise follow the repository's governing PR-base instruction, or use its GitHub default branch.

## Implement and verify

Make the requested changes within scope.
Run relevant checks and inspect the final diff.
Summarize the actual changes, verification performed, and outstanding limitations.

## Prepare the pull request

When a PR is requested:

1. Prepare the proposed title and body from the final implementation and actual verification results.
2. Identify the target repository and PR base branch.
3. Leave the final change available for review in the Codex Cloud task.
4. State that the next step is to review the change and open the PR using the Cloud task interface.

A proposed title, body, commit, or diff is a reviewable result, not proof of a GitHub PR.
Report a PR as created only when its existence on GitHub has been verified, and include its URL.
If it still needs to be opened through the Cloud interface, report that status explicitly.
