# codex-web-workflow

Two lightweight skills for preparing and executing Codex Cloud changes **through creation of a real GitHub pull request by Codex**.

## Skills and activation

### `chatgpt-codex-web`

Use in ChatGPT when preparing a complete task instruction for Codex Cloud.
It preserves known repository and PR-target requirements and requires Codex to create the GitHub PR itself.

### `codex-web-workflow`

Use inside a Codex Cloud task when changing a repository.
It checks publishing prerequisites, implements and validates the change, and creates or updates the real GitHub PR.

The first skill prepares the task; the second executes it.

## Current Codex Cloud workflow

1. Select a published environment containing the target GitHub repository and the authenticated GitHub publishing capability described below.
2. Start a task and have Codex implement and validate the requested changes.
3. Have Codex commit and push a task-specific branch and create or update the GitHub PR.
4. Verify the PR exists on GitHub and obtain its URL before reporting completion.

The native Cloud interface also supports opening a PR manually, but that does not meet this repository's **Codex-created PR** completion contract.

## Configure GitHub publication in the Cloud environment

Repository selection and checkout access alone do not guarantee that the task agent can push branches or create PRs.
Provision agent-side GitHub publishing once in the published Cloud environment and verify it in a new task.

A concrete supported setup uses GitHub CLI (`gh`) and Git authentication:

- Ensure `git` and `gh` are installed and available to the task.
- Provide authenticated GitHub access for the target repository. For a fine-grained personal access token, grant repository permissions **Contents: Read and write** and **Pull requests: Read and write**.
- Supply the credential through the Cloud environment's personal vault or environment configuration (for `gh`, a suitable `GH_TOKEN` environment variable is one option). Do not commit the credential into the repository.
- Allow the necessary GitHub HTTPS destinations (including `github.com` and `api.github.com`) in the Cloud environment network settings.
- For HTTPS Git remotes, configure Git to use the authenticated CLI (for example, `gh auth setup-git`) and verify that both Git push and PR creation work.
- Save and publish or republish the configured environment after testing access.

This is a GitHub publishing capability for **current Codex Cloud tasks**, not a separate older-mode workflow.
An alternative authenticated GitHub publishing tool may be used when it offers the same real PR creation, update, and verification capabilities.

## Install the skills

The source definitions in this repository are:

- `skills/chatgpt-codex-web/SKILL.md` for the ChatGPT-side instruction preparation workflow;
- `skills/codex-web-workflow/SKILL.md` for Codex task execution.

Load `chatgpt-codex-web` as a standalone skill in the ChatGPT desktop app, or bundle it in a plugin for use in ChatGPT Chat or Work. A GitHub-hosted `SKILL.md` is a distribution source, not automatic ChatGPT installation.
Installing `codex-web-workflow` in Codex Cloud does not activate the ChatGPT-side skill.

Install the Codex skill in the target repository at `.agents/skills/codex-web-workflow/SKILL.md`, or install it environment-wide at `$HOME/.agents/skills/codex-web-workflow/SKILL.md`.

For the environment-wide option, run the following as part of Cloud environment preparation:

```sh
skill_dir="$HOME/.agents/skills/codex-web-workflow"
mkdir -p "$skill_dir"
curl -fsSL https://raw.githubusercontent.com/supersovaa/codex-web-workflow/main/skills/codex-web-workflow/SKILL.md -o "$skill_dir/SKILL.md"
```

Publish or republish the environment after changing its setup, then start a new task.
Verify skill discovery by invoking `$codex-web-workflow` and requesting its PR completion rules without modifying the repository.

## PR completion

Codex must create or update a real pull request and verify that GitHub shows the final changes and intended base.
The final answer must contain the GitHub PR URL.
If authenticated publishing fails, report the concrete blocker and mark the requested result incomplete rather than hand PR creation to the user.

## References

- [Codex Cloud overview](https://learn.chatgpt.com/docs/cloud)
- [Cloud environments](https://learn.chatgpt.com/docs/environments/cloud-environments)
- [Build skills](https://learn.chatgpt.com/docs/build-skills)
- [GitHub CLI: create a pull request](https://cli.github.com/manual/gh_pr_create)
