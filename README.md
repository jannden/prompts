# prompts

A Claude Code plugin marketplace. It holds one plugin today.

## plower

plower runs a Claude Code Project's development upkeep and carries your standing rules into every thread: it settles decisions with you, reviews PRs, folds follow-up PRs into one, audits code quality and keeps a strategy file true. Nine skills, three pieces a Project needs, and no scripts: every skill is a Markdown prompt.

Install it from the marketplace in this repository:

```text
claude plugin marketplace add jannden/prompts
claude plugin install plower@jannden-prompts
```

Then run `/plower:onboard` in a thread of your Project. The full description, the install steps for Claude Projects and a checklist to prove an install are in [plower/README.md](plower/README.md).

plower works on GitHub repositories through the REST API, and its setup assumes the CodeRabbit and Greptile review bots. Read the skills before you install them: they instruct an agent to open and change pull requests in your repositories.
