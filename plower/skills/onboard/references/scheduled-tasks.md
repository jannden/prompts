# Scheduled tasks

Each task starts a new session. A run cannot be counted on to see the Project's instructions block or memory or the coordinator, and it cannot start Project threads, so its prompt carries every input it needs and the skills work from the prompt, the repository's `CLAUDE.md` and GitHub alone. The prompt is copied word for word, with only the `<...>` parts filled in: an edited prompt runs the wrong thing every time it fires. The scheduled task form cannot be scoped to a repository, so every prompt names its repository in its own text, and the user never sets a repository on the form. Times are in the user's time zone and sit a few minutes past the hour, since a run set on the hour can start late.

## plower tasks

| Task | Name | Default schedule | Auto-fix pull requests | Prompt |
|---|---|---|---|---|
| consolidate | `plower consolidate <owner/repo>` | daily at 06:07 | on | `/plower:consolidate repo: <owner/repo> proof: <proof commands>` |
| audit | `plower audit <owner/repo>` | nightly at 03:07 | on | `/plower:audit repo: <owner/repo> cap: <n> tree: <tree> proof: <proof commands>` |
| calibrate | `plower calibrate <owner/repo>` | weekly, Monday at 05:07 | on | `/plower:calibrate repo: <owner/repo>` |

Each prompt passes its inputs as `key: value` pairs after the skill name, which the skills read on the invocation line or on lines of their own. In the `audit` prompt, `<n>` is the cap on cleanup PRs per run and `<tree>` is `/` for the whole repository or a folder path. `<proof commands>` is the repository's install, lint, typecheck, test and build commands on one line, from the answer to question 7.

The Auto-fix pull requests setting on the task decides what happens after a run opens a PR. With it on, the PR's later review comments and CI results reach the run's session again, and the run handles them. The review bots review only when a run asks, so a run that opens a PR posts `@coderabbitai review` on it first. `audit` opens its PRs through cleanup subagents of the run, and the run handles those PRs too.

## Compounding task

Offered only when Compound Engineering is installed in the Project. It counts as installed when the session's skill list holds `ce-compound-refresh`. There is no scheduled task that captures learnings: `ce-compound` needs the conversation of the agent that did the work, which a scheduled run opened on a merged PR does not have. So each thread runs it itself at the end of its work, as the `CLAUDE.md` block and the memory seed say. The weekly refresh only compares saved learnings with the current code, which needs no conversation.

| Task | Name | Default schedule | Auto-fix pull requests | Prompt |
|---|---|---|---|---|
| Weekly refresh | `compound weekly <owner/repo>` | weekly, Sunday at 05:37 | on | `Run /ce-compound-refresh mode:non-interactive on the repository <owner/repo>.` |

## What to give the user

The user adds every task by hand on the Scheduled tasks tab, since neither the coordinator nor a thread can create one. Give one filled-in block per task, in the user's own thread or chat, with the web steps below:

```text
- Name: <name>
- Schedule: <daily | weekly on <day>> at <hh:mm>, <IANA time zone>
- Model: Sonnet 5.5, effort High
- Auto-fix pull requests: <on | off, from the table>
- Repository: none on the form; the prompt below names <owner/repo>
- Every run starts a new session; never continue an earlier one.
- Prompt, word for word, between the lines:
---
<prompt>
---
```

Then ask the user for the task's name and next run time as the Scheduled tasks tab shows them.

## Web steps

How the user adds a task. A task made at claude.ai/code/routines outside the Project runs without plower, the instructions block and the Project memory: it does not count, and the user deletes it.

1. Open the Project, then the Overview pane's Scheduled tasks tab. When a task with the name exists, open it, choose Edit, replace the prompt and check every setting below, then Save changes. Never add a second task with the same name.
2. Otherwise choose New task there, and Set up manually when a menu opens.
3. Enter the name and paste the prompt.
4. Pick Sonnet 5.5 in the prompt's model selector and set its effort to High.
5. Set Auto-fix pull requests as the table says for the task.
6. Set the trigger to Schedule, at the chosen day and time. The form uses the device's time zone, so when the device is not in the chosen zone, enter the time converted to the device's zone.
7. Leave the repository setting alone: the form cannot be scoped to a repository, and the prompt names it.
8. Choose Create.

## Counts as created

A task counts as created only when the user confirms it is listed on the Project's Scheduled tasks tab with its name, schedule, Sonnet 5.5 at High effort and the Auto-fix setting from the table, and that Run now on it made a new run there. Until then it is reported as needing the user.
