# codex-web-workflow

Two lightweight skills for preparing and executing repository changes in the current Codex Cloud workflow.

## Skills and activation

### `chatgpt-codex-web`

Use in ChatGPT when preparing a task instruction that will be pasted into Codex Cloud.
It preserves known repository, branch, and outcome requirements and produces a self-contained handoff.

### `codex-web-workflow`

Use inside a Codex Cloud task when changing a repository.
It validates the result and prepares a precise PR handoff through the Cloud task interface.

The two skills separate instruction preparation from task execution.

## Workflow

1. Select a published Codex Cloud environment containing the target repository.
2. Start a task in that environment and execute the requested changes.
3. Review the final diff and validation results.
4. Open a GitHub pull request from the Cloud task interface when ready.

PR creation is complete only when the PR exists on GitHub.
Until then, the task should give the proposed title, description, target branch, and remaining Cloud UI action rather than claim that a PR exists.

## Install the Codex skill

This repository publishes `skills/codex-web-workflow/SKILL.md` as the source skill definition.
Install it into the target repository at `.agents/skills/codex-web-workflow/SKILL.md` for repository-scoped use, or into the Codex Cloud environment's `$HOME/.agents/skills/codex-web-workflow/SKILL.md` for environment-wide use.

For an environment-level installation, add this to the Cloud environment setup:

```sh
skill_dir="$HOME/.agents/skills/codex-web-workflow"
mkdir -p "$skill_dir"
curl -fsSL https://raw.githubusercontent.com/supersovaa/codex-web-workflow/main/skills/codex-web-workflow/SKILL.md -o "$skill_dir/SKILL.md"
```

Publish or republish the environment after changing its setup, then start a new task.
Confirm skill discovery by invoking `$codex-web-workflow` and asking it to summarize its PR handoff rules without modifying a repository.

The ChatGPT-side `chatgpt-codex-web` skill is intended for preparing the task before handing it to Codex Cloud.

## References

- [Codex Cloud overview](https://learn.chatgpt.com/docs/cloud)
- [Cloud environments](https://learn.chatgpt.com/docs/environments/cloud-environments)
- [Build skills](https://learn.chatgpt.com/docs/build-skills)
