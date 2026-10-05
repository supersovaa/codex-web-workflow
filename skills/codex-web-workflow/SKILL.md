---
name: codex-web-workflow
description: Execute repository-changing tasks reliably in Codex web by treating the local work branch as temporary, resolving pull-request branch names and the writable Git remote independently, pushing with git, and creating a real GitHub pull request with gh.
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
- At least one configured Git remote for the target repository is writable.
- Network access required for GitHub operations is available.

Treat these assumptions as execution prerequisites.
Report an unmet prerequisite as soon as it blocks the workflow.

## Treat `work` as a temporary local branch

Codex web may force the local checkout branch name to `work`.
Treat `work` solely as a local implementation detail.

Resolve semantic branch roles from explicit task values, repository-specific governing instructions, and GitHub repository metadata.

When the task explicitly provides a branch value, use it.

When the pull-request base is omitted:

1. Use the repository-specific governing instruction when it defines a default pull-request base.
2. Otherwise use the repository's GitHub default branch.

When the remote push branch is omitted, choose a concise task-specific branch name.

Report missing branch information only when a required branch relationship remains unresolved after checking the task, repository instructions, and repository metadata.

## Resolve the push remote independently

Treat the Git remote name as repository-local configuration.

When the task or repository instructions explicitly provide the remote to use, use it.
Otherwise choose a configured remote whose URL identifies the target GitHub repository and whose push URL is writable.
If writable-remote resolution fails, report the missing writable remote as an unmet prerequisite.

## Pull requests are part of completion

For tasks that modify the repository, a real GitHub pull request is part of the definition of done.

Use only GitHub CLI (`gh`) to create or update the pull request.

After completing and validating the requested changes:

1. Commit the intended changes when needed.
2. Push the current `HEAD` to the resolved remote and remote branch.
3. Inspect the final diff and actual verification results.
4. Generate the PR title and body from that final state.
5. Create a new pull request or update the existing pull request with GitHub CLI.
6. Verify that the pull request exists on GitHub and reflects the final diff.
7. Obtain its URL.
8. Include the URL in the final response.

The PR title and body must describe the final implementation and actual verification results.

Use an explicit remote and branch target so local checkout state and repository-local remote naming stay separate from the remote workflow:

```sh
git push -u <push_remote> HEAD:<push_branch>
```

Create a new pull request explicitly with the resolved head and base branches, title, and generated body:

```sh
gh pr create --head <push_branch> --base <pr_base> --title "<title>" --body-file <body_file>
```

If a pull request already exists for the branch, update its body and update its title when needed:

```sh
gh pr edit <number> --title "<title>" --body-file <body_file>
```

Report completion only after the pull request exists on GitHub, reflects the final diff, and its URL has been obtained.

## Handle unmet prerequisites

Keep the expected GitHub environment contract and pull-request mechanism fixed during execution.
Keep repository changes scoped to the requested task and resolve environment blockers through environment configuration.
Treat authentication failure, insufficient permissions, writable-remote resolution failure, push failure, or pull-request creation failure as execution blockers.
Report the concrete unmet prerequisite or failed GitHub operation.
