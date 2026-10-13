---
name: codex-web-workflow
description: Execute repository changes in Codex Cloud, validate them, and create or update a real GitHub pull request from the task using authenticated GitHub publishing capabilities.
---

# Codex Cloud workflow

Use this skill when executing a repository-changing task in Codex Cloud.

## Confirm publication capability

Use the repository and workspace selected for this task.
Before implementation, confirm that the environment can authenticate to the correct GitHub repository, push changes, and create or update a pull request.
Use the authenticated GitHub publishing mechanism configured for this Cloud environment.

A repository connected for checkout does not itself establish agent-side write access.
When credentials, permission, network access, or a required publication tool is missing, report the unmet prerequisite as a blocker.
Do not substitute a user-operated Cloud UI action for the required agent-created PR.

## Resolve the PR target

Use explicit task values and repository-specific governing instructions for branch choices.
For an unspecified PR base, use the repository's governing instruction or its GitHub default branch.
Publish the changes to a task-specific head branch rather than directly to the PR base branch.

## Implement and publish

1. Implement the requested change within scope.
2. Run relevant checks, inspect the final diff, and record actual verification results.
3. Commit the intended changes and push the task-specific head branch.
4. Generate the PR title and body from the final diff and actual verification.
5. Create a new GitHub PR, or update the existing PR for the head branch, through the configured authenticated publishing mechanism.
6. Verify the PR exists on GitHub, targets the resolved base branch, and includes the final changes.
7. Report the PR URL with the changes and verification results.

When GitHub CLI is the configured publishing mechanism, use it for PR creation or updates with explicit repository, base, and head values.
Resolve the remote from the configured repository rather than presuming a fixed remote name.

Report a failed push or PR operation as an incomplete task with its concrete blocker.
A commit, branch, or draft description alone does not satisfy PR creation.
