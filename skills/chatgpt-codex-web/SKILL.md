---
name: chatgpt-codex-web
description: Prepare complete Codex web task instructions, including environment prerequisites, explicit branch metadata, and pull-request requirements, so the generated instruction can be pasted into Codex web without rewriting.
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

When branch identity matters, include the relevant branch values explicitly in the generated instruction.
Use these concepts:

- `source_branch`: the branch or revision the task is based on;
- `pr_base`: the target branch of the pull request;
- `push_branch`: the remote branch to which Codex should push its changes.

Do not tell Codex to infer these values from:

- `git branch --show-current`;
- `git status`;
- the local checkout branch name.

If a required branch value is unknown and cannot be derived from information already provided by the user, make the missing value explicit rather than pretending that Codex can recover it later.

## Generate a directly executable instruction

The final Codex instruction should be ready to paste into Codex web as-is.
Do not require the user to translate explanatory prose into operational steps.

When repository changes are requested, include all of the following requirements in the Codex instruction:

- Treat the local branch name `work` as an implementation detail.
- Use the explicitly supplied branch values.
- Never use `make_pr`.
- Push the current `HEAD` to the explicitly supplied `push_branch`.
- Create the pull request with `gh pr create`.
- Pass `--head <push_branch>` and `--base <pr_base>` explicitly.
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
