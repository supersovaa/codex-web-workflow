---
name: codex-web-workflow
description: Execute repository-changing tasks reliably in Codex web by treating the local work branch as temporary, using explicit branch metadata, pushing with git, and creating a real GitHub pull request with gh instead of make_pr.
---

# Codex web workflow

Use this skill when executing repository-changing work in Codex web under the expected GitHub environment contract.

## Environment assumptions

Assume:

- The repository is hosted on GitHub.com.
- `git` is available.
- GitHub CLI (`gh`) is installed.
- `GH_TOKEN` is configured for GitHub CLI authentication.
- The token can access the target repository.
- The token has `Contents: Read and write`.
- The token has `Pull requests: Read and write`.
- The configured Git remote is writable.
- Network access required for GitHub operations is available.

These are prerequisites, not problems to work around.
If a prerequisite is missing, report the missing prerequisite instead of switching to an unrelated pull-request workflow.

## Treat `work` as a temporary local branch

Codex web may force the local checkout branch name to `work`.
Treat `work` only as a local implementation detail.

Never interpret `work` as:

- the user's original branch;
- the source branch requested by the user;
- the pull-request base branch;
- the desired remote branch.

Never infer the original branch identity from:

- `git branch --show-current`;
- `git status`;
- the current local branch name.

Use branch names explicitly provided in the task instruction.
If branch metadata required to create the requested pull request is absent, report the missing metadata instead of guessing.

## Pull requests are part of completion

For tasks that modify the repository, a real GitHub pull request is part of the definition of done.

Never call `make_pr`.
Do not treat PR title/body generation, a commit, or a push as equivalent to creating a pull request.

After completing and validating the requested changes:

1. Commit the intended changes when needed.
2. Push the current `HEAD` to the explicitly supplied remote branch.
3. Inspect the final diff and actual verification results.
4. Generate the PR title and body from that final state.
5. Create a new pull request or update the existing pull request with GitHub CLI.
6. Verify that the pull request exists on GitHub and reflects the final diff.
7. Obtain its URL.
8. Include the URL in the final response.

The PR title and body must describe the final implementation and actual verification results.
Do not leave stale descriptions from an earlier plan or intermediate implementation.

Use an explicit push target so the local `work` branch name does not leak into the remote workflow:

```sh
git push -u origin HEAD:<push_branch>
```

Create a new pull request explicitly with the supplied head and base branches, title, and generated body:

```sh
gh pr create --head <push_branch> --base <pr_base> --title "<title>" --body-file <body_file>
```

If a pull request already exists for the branch, update its body and update its title when needed:

```sh
gh pr edit <number> --title "<title>" --body-file <body_file>
```

Generating or printing a PR title/body is not completion.
Do not report the repository-changing task as complete unless the pull request actually exists, reflects the final diff, and its URL has been obtained.

## Do not redesign the environment during execution

Do not replace the expected workflow with another mechanism merely because the environment contract is not satisfied.
In particular:

- do not use `make_pr`;
- do not invent another branch from the local `work` name;
- do not attempt to compensate for missing GitHub permissions by changing the repository contents;
- do not claim completion when GitHub authentication, push, or pull-request creation failed.

Report the concrete unmet prerequisite or failed GitHub operation instead.
