---
name: codex-web-workflow
description: Carry out repository-changing tasks in Codex Cloud, preserve the selected repository and branch context, and publish a verifiable GitHub PR when supported or hand off a reviewable diff to the Cloud PR UI.
---

# Codex Cloud workflow

Use this skill when performing repository-changing work in Codex Cloud on the web.

## Resolve the active context

Work in the repository and workspace selected for the task.
Use explicitly supplied branch values and repository-specific governing instructions.
When the PR base is otherwise unspecified, use the GitHub repository's default branch.

A local checkout may use a temporary name such as `work`.
Treat that name as a local implementation detail rather than evidence of the original source branch or PR target.
Use actual task selection and repository metadata for semantic branch roles.

Check which GitHub publication facilities are available in the active environment before depending on them.
The Cloud connection does not by itself guarantee an authenticated `gh` session, `GH_TOKEN`, a writable remote, or direct PR-creation tools.

## Implement and validate

Make the requested changes within the task's scope.
Run relevant checks and inspect the final diff.
Report validation that was actually performed, including any limitations.
Prepare an accurate PR title and description from the final change and verification results when a PR is requested.

## Publish or hand off the PR

When an authenticated, authorized tool or command in the active environment can create or update a real GitHub pull request:

1. Use the specified PR base, or resolve it from repository instructions and then GitHub metadata.
2. Publish the intended final changes through the available mechanism.
3. Verify the real PR exists on GitHub and corresponds to the final changes.
4. Return its URL and state whether it is a draft.

When publication requires a user action in the Codex Cloud interface:

1. Keep the validated changes accessible in the current Cloud task for review.
2. Supply the proposed PR title and body, the resolved target repository and base branch, and the next UI action (`Create PR` or `Create draft PR`, whichever is available).
3. Explicitly report that the PR has not yet been created.

A `make_pr` output, commit, push, or PR description counts as a PR only if a real GitHub PR has been created and its existence verified.
Do not claim that a user-only Cloud UI operation was performed by the agent.
If an agent-created PR was explicitly required and no authorized publishing path is available, report the missing capability as an unmet requirement instead of marking that outcome complete.

## Optional command-line publication

Use the command-line publishing path only when the active environment provides authenticated GitHub CLI access, network access, and a writable remote for the target repository.

Resolve an explicitly supplied `push_remote` first; otherwise identify a configured writable remote for the correct GitHub repository.
Resolve an explicitly supplied `push_branch` first; otherwise choose a concise task-specific remote branch name.
Do not assume the remote is named `origin` or derive a semantic branch identity from a temporary local checkout name.

Commit the intended changes if needed, push the current HEAD to the resolved head branch, then create or update the PR with `gh` using explicit head and base branches.
Verify the PR on GitHub and return its URL.
If credentials, permissions, network access, the push, or PR creation fail, report the actual blocker.
