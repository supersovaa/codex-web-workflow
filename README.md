# codex-web-workflow

Two lightweight skills for handing tasks from ChatGPT to Codex Cloud and completing repository changes with an accurate GitHub pull-request outcome.

## Skills

### `chatgpt-codex-web`

Use in ChatGPT when writing a self-contained instruction to paste into Codex Cloud.
It carries known repository and branch constraints into the task and states whether PR publication must happen inside the agent or can be completed through the Cloud interface.

### `codex-web-workflow`

Use in Codex Cloud when implementing repository changes.
It validates the final diff, uses an authenticated PR-publishing capability if one is available, and otherwise hands a reviewable result to the native Cloud PR controls without reporting an uncreated PR as complete.

The two skills separate instruction preparation from task execution.

## Current Codex Cloud workflow

1. Connect the intended GitHub repository and create or select a Codex Cloud environment.
2. Prepare and test the environment, then **Publish** it.
3. Start a task from that environment; each task receives an isolated workspace.
4. Review changes and verification in the task.
5. Create or update a real GitHub PR through an authorized capability, or use the Cloud UI's PR action after the task.

Codex Cloud (Legacy) remains a separate experience for supported integration workflows.
Do not assume legacy shell, authentication, branch, or PR behavior applies to the current Cloud experience.

A configured GitHub connection is not proof that `gh`, `GH_TOKEN`, or a writable Git remote exists in the agent shell.
Those are optional prerequisites for a specifically configured command-line publishing path, not the default Cloud task contract.

## Install `codex-web-workflow` in Codex Cloud

The published skill source is `skills/codex-web-workflow/SKILL.md` in this repository.
Codex does not discover it automatically when working in an unrelated repository.

Install it into the Cloud environment's `$HOME/.agents/skills/codex-web-workflow/SKILL.md` during environment preparation, or install it as a repository-scoped skill under `.agents/skills/codex-web-workflow/SKILL.md` in the target repository.
Repository-scoped skills are available in Cloud tasks; skills installed on a different local computer are not synced automatically.

For an environment-level installation, run this during Cloud environment setup:

```sh
skill_dir="$HOME/.agents/skills/codex-web-workflow"
mkdir -p "$skill_dir"
curl -fsSL https://raw.githubusercontent.com/supersovaa/codex-web-workflow/main/skills/codex-web-workflow/SKILL.md -o "$skill_dir/SKILL.md"
```

After preparing or updating an environment installation, publish or republish the environment and start a new task to use the prepared version.
Keep the file in this repository as the distribution source rather than maintaining another independent copy.

Before testing publication behavior, confirm the skill is discoverable in the Cloud task.
For example, invoke `$codex-web-workflow` and request a summary of its PR completion rules without modifying the repository.

## Branch and PR rules

Branch values have distinct roles:

- `source_branch`: intended starting branch or revision;
- `pr_base`: target branch for the PR;
- `push_branch`: remote head branch when command-line publishing is used;
- `push_remote`: writable Git remote when command-line publishing is used.

An explicitly selected value takes precedence.
Use repository-specific governing instructions for a missing PR base, otherwise the GitHub default branch.
A temporary local branch name such as `work` is not a reliable source for recovering the original branch identity.
Neither remote names nor push-branch names need to be supplied for the native Cloud PR workflow.

A completed PR must exist on GitHub and have a verifiable URL.
If a PR still requires a Cloud UI action, the task should report **PR not yet created** and provide the proposed title, body, target base, and next action.
A prepared `make_pr` description alone is not proof of PR creation.

## References

- [Codex Cloud overview](https://learn.chatgpt.com/docs/cloud)
- [Cloud environments](https://learn.chatgpt.com/docs/environments/cloud-environments)
- [Build skills](https://learn.chatgpt.com/docs/build-skills)
