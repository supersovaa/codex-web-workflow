# codex-web-workflow

Skills for generating Codex web instructions in ChatGPT and executing them reliably in Codex, with explicit branch and pull request handling.

## Why this exists

Codex web has environment-specific behavior that can make otherwise reasonable instructions unreliable.
In particular:

- the checked-out local branch may be named `work`, so the original branch identity cannot be recovered from the local checkout name;
- preparing pull-request text is not the same as creating a real pull request;
- `make_pr` can be mistaken for completion even when no pull request exists on GitHub;
- GitHub authentication and permissions need to be defined before the Codex task starts.

This repository splits those concerns into two small skills.

## Skills

### `chatgpt-codex-web`

Use this on the ChatGPT side when preparing a task for Codex web.

Its job is to:

- define the expected Codex web environment;
- explain the required `GH_TOKEN` permissions when asked;
- preserve branch information that Codex web cannot recover later;
- generate a complete instruction that can be pasted into Codex web without rewriting.

### `codex-web-workflow`

Use this on the Codex side while executing the task.

Its job is to:

- treat the local `work` branch as an implementation detail;
- use branch names explicitly supplied in the task;
- push the current `HEAD` to the requested remote branch;
- create a real GitHub pull request with `gh pr create`;
- never use `make_pr`;
- require a real pull-request URL before reporting completion.

## Expected environment

The initial version assumes:

- the repository is hosted on GitHub.com;
- `git` is available;
- GitHub CLI (`gh`) is installed;
- `GH_TOKEN` is set;
- `GH_TOKEN` is a fine-grained personal access token with access to the target repository;
- the token has `Contents: Read and write`;
- the token has `Pull requests: Read and write`;
- network access required for GitHub operations is available;
- the configured Git remote is writable.

If the task is expected to modify GitHub Actions workflow files, the environment may additionally require `Workflows: Read and write`.

## Branch model

Instructions generated for Codex should distinguish these values when they matter:

- `source_branch`: the branch or revision the work is based on;
- `pr_base`: the target branch of the pull request;
- `push_branch`: the remote branch that receives the Codex changes.

Codex must not infer any of them from the local branch name.

## Pull-request completion rule

A task that modifies the repository is not complete merely because the changes were committed or pushed.
The pull request itself must exist on GitHub.

The expected flow is conceptually:

```sh
git push -u origin HEAD:<push_branch>
gh pr create --head <push_branch> --base <pr_base>
```

The final Codex response should include the resulting pull-request URL.

## Status

This is intentionally a small first version.
The rules should be refined from real Codex web usage rather than expanded speculatively.
