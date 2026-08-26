plower 1.3.0 · memory

Facts for every thread in this Project, filled in by `onboard` and saved by the coordinator. The standing rules, including how to handle review comments and review bots, live in the plower block of each repository's root `CLAUDE.md`, which scheduled runs read too; the facts can each be read again from GitHub.

- Repository: <owner/repo>, default branch `<branch>`.
- Proof commands: <the commands a change must pass before it is pushed>.
- GitHub: reach it through REST (`gh api repos/{owner}/{repo}/...`). The cloud proxy refuses GraphQL, so `gh` subcommands that use it fail.
- Review bots: <CodeRabbit with `.coderabbit.yaml`, Greptile with `greptile.json`>. Both skip drafts.
- plower labels: `plower:consolidate` (a PR the user marks for folding), `plower:consolidated`, `plower:calibrate`, `plower:audit`, `plower:setup`.
- plower branches: `plower/consolidate-<yyyymmdd-hhmm>`, `plower/calibrate-<yyyymmdd>`, `plower/audit-<slug>-<yyyymmdd>`, `plower/setup-<yyyymmdd>`. Other threads leave these branches alone.
- Every scheduled task has Auto-fix pull requests on, so a run that opens a PR gets its later review and CI events and handles them. The run itself never waits for a review. Scheduled runs cannot start Project threads or reach the coordinator, so their prompts carry every input.
- Approved strategy sections: a `STRATEGY.md` section whose heading is followed by `<!-- approved -->`, or that carries an HTML comment containing `author-approved`, is never edited by a thread.
- Scheduled tasks, in <time zone>: `consolidate` <when>; `audit` <when>, cap <n>, tree `<tree>`; `calibrate` <when><; compounding weekly <when>>.

plower · end of memory
