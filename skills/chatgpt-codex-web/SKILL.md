---
name: chatgpt-codex-web
description: Prepare paste-ready instructions for Codex Cloud web coding tasks, preserving repository and branch intent and distinguishing a created GitHub pull request from a Cloud UI handoff.
---

# ChatGPT to Codex Cloud

Use this skill when preparing a self-contained task instruction that the user will hand to Codex Cloud on the web.

## Establish the task environment

Use the current Codex Cloud workflow by default: the user selects a published environment containing the intended GitHub repository, and each task runs in its own workspace.
Treat Codex Cloud (Legacy) as a distinct workflow when the user explicitly uses it.

Before the handoff, identify any required repository, starting revision, PR target, and task restrictions from information already available.
Include explicit values in the instruction when known; leave choices to the selected environment or repository instructions when they are optional.
Ask for environment setup only when an actual prerequisite is missing.
Environment creation, GitHub connection, and publication belong to Codex Cloud setup, not to every coding task.

The Cloud environment and GitHub connection do not imply that the agent shell has `GH_TOKEN`, `gh`, a writable Git remote, or permission to create a PR.
Require those facilities only when a task explicitly needs agent-side GitHub publishing and the environment provides them.
When setup is required, explain the missing capability before producing instructions that depend on it.

## Preserve repository and branch intent

Carry known, task-relevant branch values explicitly:

- `source_branch`: the task's intended starting branch or revision;
- `pr_base`: the branch targeted by the pull request;
- `push_branch`: the remote head branch, if agent-side publishing is configured;
- `push_remote`: the writable remote, if agent-side publishing is configured.

Use a user-selected `pr_base` or repository-specific governing instruction; otherwise use the GitHub default branch.
Do not infer the original branch from a temporary local checkout name such as `work`.
When `push_branch` or `push_remote` is unnecessary for the selected Cloud workflow, omit it.
Do not require the user to select optional branch or remote names.

## Specify the pull-request outcome

For repository-changing tasks, require implementation, relevant validation, and a reviewable final diff.

If a pull request is requested, specify the expected publication outcome:

- When the task has an authorized mechanism that creates or updates a real GitHub PR, use it, verify the resulting PR, and provide its URL.
- Otherwise leave the validated changes available for review in the Codex Cloud task and provide a proposed PR title, body, and the explicit next step to create the PR through the Cloud UI.
- Distinguish `PR created` from `PR not yet created`. A commit, push, `make_pr` description, or prepared PR text is not evidence of an existing PR.

Do not instruct the agent to operate controls in the Cloud UI as if they were shell tools.
If the user requires Codex itself to create the PR, state that authenticated GitHub publication capability must be available; do not silently replace that requirement with a user-action handoff.

## Produce a directly usable instruction

Output a complete instruction the user can paste into Codex Cloud as-is.
Include the repository and branch constraints already known, the required change, relevant verification, and the PR completion or handoff rule.
Do not substitute generic environment setup steps for task-specific instructions.
