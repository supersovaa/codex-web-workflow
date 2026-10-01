---
name: chatgpt-codex-web
description: Prepare complete Codex web task instructions, including environment prerequisites, branch-resolution rules, and pull-request requirements, so the generated instruction can be pasted into Codex web without rewriting.
---

# ChatGPT to Codex web

Use this skill when preparing instructions that a user intends to paste directly into Codex web.

The generated instruction must be self-contained enough for Codex to execute without reconstructing information that Codex web discards or obscures.

## Environment contract

Assume the following Codex web environment unless the user specifies otherwise:

- The repository is hosted on GitHub.com.
- `git` is available.
- GitHub CLI (`gh`) is installed.
- `GH_TOKEN` is configured.
- `GH_TOKEN` is a fine-grained personal access token with access to the target repository.
- The token has `Contents: Read and write`.
- The token has `Pull requests: Read and write`.
- The configured Git remote is writable.
- Network access required for GitHub operations is available.

If GitHub Actions workflow files must be modified, explain that the environment may additionally require `Workflows: Read and write`.

When the user asks how to configure the environment, answer from this contract instead of delegating environment design to Codex.

## Preserve branch information before handoff

Codex web may check out the working tree on a local branch named `work`.
The local branch name therefore cannot be used to recover the user's original branch identity.

When branch identity matters and the relevant value is known or user-selected, include it explicitly in the generated instruction.
Use these concepts:

- `source_branch`: the branch or revision the task is based on;
- `pr_base`: the target branch of the pull request;
- `push_branch`: the remote branch to which Codex should push its changes.

Do not tell Codex to infer these values from:

- `git branch --show-current`;
- `git status`;
- the local checkout branch name.

Branch names are optional unless the task requires a specific branch relationship.
When `pr_base` is omitted, let Codex resolve it from repository-specific governing instructions and then the GitHub default branch.
When `push_branch` is omitted, let Codex choose a concise task-specific remote branch name.
Do not ask the user to choose an otherwise optional branch name.

## Generate a directly executable instruction

The final Codex instruction should be ready to paste into Codex web as-is.
Do not require the user to translate explanatory prose into operational steps.

When repository changes are requested, include all of the following requirements in the Codex instruction:

- Treat the local branch name `work` as an implementation detail.
- Use any explicitly supplied branch values.
- Resolve an omitted `pr_base` from repository instructions, then the GitHub default branch.
- Choose a concise task-specific `push_branch` when it is omitted.
- Never use `make_pr`.
- Push the current `HEAD` to the resolved `push_branch`.
- Create the pull request with `gh pr create`.
- Pass the resolved `--head <push_branch>` and `--base <pr_base>` explicitly.
- Do not treat a commit, push, pull-request draft, or generated PR description as completion.
- Verify that the pull request actually exists on GitHub.
- Obtain the resulting pull-request URL.
- Include the pull-request URL in the final response.

Prefer this push pattern when the local branch is named `work`:

```sh
git push -u origin HEAD:<push_branch>
```

Prefer this pull-request pattern:

```sh
gh pr create --head <push_branch> --base <pr_base>
```

## Do not shift environment design to Codex

The ChatGPT-side instruction is responsible for preserving information that will be unavailable or ambiguous after handoff.
Do not generate instructions that ask Codex to decide what the GitHub token permissions should be, recover the original branch from `work`, or choose a substitute pull-request mechanism.

If the expected environment contract is not suitable for the user's repository or task, explain the required environment change before generating the final Codex instruction.
