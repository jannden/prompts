# plower

plower runs a Claude Code Project's development upkeep and carries your rules into every thread. You step in only to decide, from a phone. It is one Claude Code plugin with nine skills and three pieces a Project needs.

## The skills

On demand, only when you invoke them by name:

| Skill | What it does |
|---|---|
| `align` | Settles one decision in the chat in plain words: reads what the matter touches, asks one question per turn with explained options and a recommendation, and ends with the decision and a task you can paste to the coordinator. Writes nothing. |
| `examine` | Reviews any PR with a fresh reviewer that looks hardest for what AI-written code gets wrong, folds in earlier reviews, keeps critical, high and medium findings, and asks you about each: fix, discuss or don't care. Fixes land in one push; on a fork's PR they are described instead. |
| `inventory` | Maps what your app does from a user's point of view and asks you, item by item, to keep, improve or delete it. Ends in the chat with the decisions and a brief for the next task. |
| `onboard` | Sets up one repository and its Project for plower: the review-bot configs, the labels, the `CLAUDE.md` block, the instructions block you paste, the memory seed the coordinator saves, and the scheduled tasks you add. Run it again to see which pieces are current, stale or drifted. |

On a schedule (also on demand), with nobody present:

| Skill | What it does |
|---|---|
| `consolidate` | Folds the open follow-up PRs (dependency bumps, small bot fixes, PRs you label `plower:consolidate`) into one PR for you to review and merge, then closes the originals that did not move. Never merges. |
| `audit` | Researches one tree of the repository read-only for dead code, needless complexity, stale comments and missed best practices, keeps the areas worth a PR up to the cap you set, and has one subagent per area clean it up in its own worktree and open the PR. Edits nothing itself. |
| `calibrate` | Compares the week's merged PRs with `STRATEGY.md` and, on drift, opens one PR with the smallest edit that brings it up to date. Never touches a section you marked approved. |

Standing rules, applied in every thread:

| Skill | What it does |
|---|---|
| `pushback` | Challenges a request the evidence shows is wrong, holds without new evidence, complies on your explicit override, and records it. |
| `prefer-refactor` | Refactors a bad local pattern instead of copying it. |

## plower beside Compound Engineering

plower and Compound Engineering are two independent plugins on the same level. plower needs nothing from Compound Engineering and never changes one of its steps. It calls one of its skills only when that skill is present: the `CLAUDE.md` block tells a thread to run `ce-compound` itself at the end of its work, since only that thread's conversation holds what it learned.

- Reach for Compound Engineering to brainstorm, plan, build, review code as a pipeline step and capture learnings.
- Reach for plower to decide (`align`), judge a PR (`examine`), take stock (`inventory`), keep a Project tidy on a schedule (`consolidate`, `audit`, `calibrate`), and keep your standing rules in force.

When Compound Engineering is installed in the Project, `onboard` also gives you the details for one scheduled task that refreshes the saved learnings against the current code every week. You add it by hand on the Project's Overview > Scheduled tasks tab, with Auto-fix pull requests on. Capturing a learning is not scheduled: a scheduled run has no conversation to learn from.

## The three Project pieces

A Project thread loads the Project's plugins, its instructions, its memory and each repository's root `CLAUDE.md`, and nothing else from the repository. plower ships one piece for each:

| Piece | Where it goes | What it holds |
|---|---|---|
| Instructions block | Project settings > Memory > Project instructions, pasted | How the coordinator handles judgment calls and PR actions, and the rule that a thread with nobody present never waits on a question. |
| Memory seed | `MEMORY.md` in Project memory, saved in full with the memory tool, by the coordinator when `onboard` runs in a thread, or by `onboard` itself when it runs in the project conversation (you cannot edit Project memory, so you never paste it) | Facts and working preferences every thread needs: repository, proof commands, review bots, how threads handle review comments, plower's labels and branches, the scheduled tasks. |
| `CLAUDE.md` block | The repository's root `CLAUDE.md`, added by the setup PR | The standing rules: test first, the holistic lens, simplification, prose, who merges, pushback and prefer-refactor. |

Each piece starts with a line such as `plower 1.4.0 · instructions` and ends with `plower · end of instructions`, so a thread can quote which pieces it loaded and `onboard` can tell a current piece from a stale or cut one. The texts live in `skills/onboard/references/`.

## Install

1. In claude.ai, open Customize > Plugins, choose Add marketplace and enter `jannden/prompts`.
2. In your Project, open Project settings > Plugins and add `plower`. Add it there only, not for your whole account: plower is then loaded exactly in the Project's threads, which is how `onboard` proves the install, and it is not loaded twice.
3. Make sure the repository is one of the Project's repositories and the Claude GitHub App is installed on it.
4. Start a thread in the Project and run `/plower:onboard`.

From a terminal, the marketplace is `claude plugin marketplace add jannden/prompts` and the plugin is `plower@jannden-prompts`.

Threads run on the Project's Thread model and Thread effort; keep them at Opus and high. A new Project runs the coordinator at low effort, and `onboard` asks whether to raise it.

## Updating

Installed copies update only when `version` in `.claude-plugin/plugin.json` changes. Every release bumps it, and bumps the version in the first line of all three pieces to the same number. After an update, run `onboard` again: it reports each piece as current, stale (with the fresh text), drifted, or needing you. Threads never ask you to carry messages to the coordinator: they message it directly, and the coordinator saves the fresh memory seed itself.

## Checks

```bash
claude plugin validate .
claude plugin validate ./plower
```

The first validates the marketplace, the second the plugin and each skill's frontmatter. There are no tests and no check scripts. The real proof is a run.

## Proving an install

Run these once on a throwaway test repository in a test Project after installing. Each passes on what the thread's transcript shows, not on a green run status.

- [ ] **Pieces load.** A thread the coordinator starts quotes the first line of all three pieces before any tool call, and lists the nine plower skills.
- [ ] **Judgment call.** A request with two readings gets one line naming the call and an offer of `align`, and no thread starts. A clear request starts a thread with no offer.
- [ ] **align.** Every question has explained options with one marked recommended; afterwards no file and no PR exist.
- [ ] **examine.** On a same-repository PR, two findings marked fix land as one commit and one push. On a fork's PR, fix describes the change and nothing runs or is pushed.
- [ ] **inventory.** The chat ends with the grouped decisions and a brief, and the repository is unchanged.
- [ ] **onboard.** The setup PR carries the bot configs and the `CLAUDE.md` block; the labels exist; before the setup PR merges, no scheduled task is created. With plower missing from the Project, `onboard` names the fix and installs nothing.
- [ ] **Scheduled tasks.** Each task appears on the Project's Scheduled tasks tab, a Run now makes a new run, and its model is Sonnet 5.5. Note the effort it reports; it should be High. Ask a run what it can see: the Project instructions, the Project memory, the coordinator, a way to start Project threads. The skills must work when every answer is no, because the task prompt and the repository's `CLAUDE.md` carry what they need. Auto-fix pull requests is on for every task.
- [ ] **consolidate.** Two Dependabot PRs, one feature PR and one conflicting PR: one consolidated PR folds the two Dependabot PRs and names the conflicting one. After you merge it, the next run closes the unmoved originals.
- [ ] **audit.** A planted dead-code area yields one PR: a cleanup subagent pushes its branch first and opens a PR with the area marker, and the run's report links it. A tree with only nitpicks ends `nothing:` and leaves the checkout unchanged.
- [ ] **calibrate.** A week that fits `STRATEGY.md` ends `nothing:`; a merged PR that finishes a listed item opens one PR and leaves approved sections alone.
- [ ] **Nobody present.** A scheduled run with an ambiguous case finishes without appearing under Waiting on you.
- [ ] **Merging.** The Merge it button merges a test PR; a review comment asking a thread to merge does not.
- [ ] **Outside a Project.** In a terminal session with plower installed for that session (`claude plugin install plower@jannden-prompts`, separate from the Project install) and the repository checked out, `examine` runs with no Project memory.
