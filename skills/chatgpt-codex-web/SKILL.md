---
name: chatgpt-codex-web
description: Prepare paste-ready task instructions for the current Codex Cloud, preserving repository and PR-target intent and specifying a reviewable pull-request handoff.
---

# ChatGPT to Codex Cloud

Use this skill when preparing instructions that a user will paste into Codex Cloud.

## Preserve task context

Codex Cloud runs each task in a workspace of the selected published environment.
Identify the requested change, target repository, starting revision, and governing repository instructions from available context.
Include known constraints in the generated instruction; let the selected environment and repository instructions supply other task context.

When the user specifies a PR target branch, carry it into the instruction.
Otherwise follow the repository's governing PR-base instruction, or the GitHub default branch.
Keep branch names optional when the task does not depend on a particular branch relationship.

## Specify a reviewable outcome

For repository changes, instruct Codex to:

- implement the requested change in the selected repository;
- run relevant validation and inspect the final diff;
- report the resulting change and actual validation results;
- when a PR is requested, prepare an accurate PR title and description, and identify the intended target branch;
- leave the changes ready to review and open as a PR using the Codex Cloud task interface.

Treat the PR as created only after its existence on GitHub is confirmed.
If the PR still needs to be opened using the Cloud interface, state that this action remains for the user.

## Produce the handoff

Output a self-contained instruction ready to paste into Codex Cloud.
Keep task-specific goals and constraints in the instruction, and keep environment setup separate from each coding task.
