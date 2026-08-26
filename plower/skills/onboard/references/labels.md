# Labels

plower tells its own PRs apart by label and branch prefix, never by author. Labels are repository settings that no merge creates, so `onboard` creates them over REST before the setup PR opens.

| Label | Color | Description | Carried by |
|---|---|---|---|
| `plower:consolidate` | `fbca04` | Fold this PR into the next consolidated PR. | A PR the user marks for folding |
| `plower:consolidated` | `5319e7` | Folds follow-up PRs into one. The user merges it. | The consolidated PR, on `plower/consolidate-<yyyymmdd-hhmm>` |
| `plower:calibrate` | `0e8a16` | Brings STRATEGY.md up to date with merged decisions. | The strategy update, on `plower/calibrate-<yyyymmdd>` |
| `plower:audit` | `c5def5` | Cleans up one area a nightly audit found. | Each audit cleanup PR, on `plower/audit-<slug>-<yyyymmdd>` |
| `plower:setup` | `1d76db` | Sets this repository up for plower. The user merges it. | The setup PR, on `plower/setup-<yyyymmdd>` |

## Create or update

For each label:

1. `gh api repos/{owner}/{repo}/labels -X POST -f name=<name> -f color=<color> -f description=<description>`.
2. When it answers 422 because the label exists, update it in place: `gh api repos/{owner}/{repo}/labels/<name> -X PATCH -f color=<color> -f description=<description>`, with the colon in the name written as `%3A` in the path.

Then read every label back (`gh api repos/{owner}/{repo}/labels?per_page=100`, every page) and confirm all five exist with the color and description above. A label counts as created only when this read shows it.

When a call is refused (403, or the proxy blocks it), do not retry it. Give the user the one step: open `github.com/<owner>/<repo>/labels`, choose New label, and add each missing label with its name, color and description from the table. Ask with a card offering "done", then read the labels back again.

Never rename or delete a label: open PRs carry them.
