---
name: chatgpt-codex-web
description: Prepare self-contained Codex Cloud task instructions for repository changes that Codex must implement, validate, and publish as a real GitHub pull request.
---

# ChatGPT to Codex Cloud

Use this skill when writing task instructions to paste into Codex Cloud.

## Preserve task context

Use the repository and published Cloud environment selected for the task.
Carry known goals, constraints, starting revisions, and repository-specific instructions into the handoff.

When a pull-request base is explicitly selected, preserve it.
Otherwise use the governing repository instruction or the GitHub default branch.
Leave optional branch names to Codex.

## Require agent-side pull-request creation

For repository-changing tasks, the instruction must make Codex responsible for implementing and validating the changes **and creating or updating the real GitHub pull request**.
A pull-request creation step in the Codex Cloud interface that requires the user to act does not satisfy this requirement.

Require Codex to:

1. Check that the active Cloud environment provides an authenticated GitHub publishing mechanism, access to the correct repository, and permission to push and create pull requests.
2. Implement the requested change, run relevant validation, and inspect the final diff.
3. Commit and publish the intended changes on a task-specific branch.
4. Create or update the GitHub pull request with its resolved base branch and an accurate title and body.
5. Verify the pull request exists and represents the final changes, and return its GitHub URL.

If agent-side publication is unavailable, require Codex to report the concrete blocker and mark the requested outcome incomplete.
A prepared PR description, a pushed branch, or a request for the user to open the PR is not task completion.

Codex Cloud repository selection alone does not guarantee that the agent has GitHub write credentials or a PR creation tool.
Treat those as environment prerequisites established before task execution, not as a choice to delegate PR creation to the user.

## Produce the handoff

Output a complete instruction ready to paste into Codex Cloud without rewriting.
Include the requested change, any known branch constraints, validation expectations, and the agent-created PR completion rule.
