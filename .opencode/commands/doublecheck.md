---
description: Compare week N-1 with week N, write review<N>.md, commit it as $REVIEW, push to GitHub
subagent: false
---

Review the week that just ended: **week $1**. If no argument is given, infer N as the
highest week that has both `week<N>.md` and `workout<N>.md` and whose last day is in
the past, then state the N you picked before continuing.

Read `AGENT.md` first. Then read `week<N-1>.md`, `workout<N-1>.md`, `week<N>.md`,
`workout<N>.md`, and `review<N-1>.md` if it exists. Do not write the review until
you have the actuals below — a plan compared against itself proves nothing.

## 1. Get the actuals

Ask me in one round, 3–6 numbered questions, each with concrete options and a
default:

- sessions actually done vs planned (lifts, runs, morning walks, Friday, Saturday)
- daily step totals, or which days cleared the floor
- bedtime: nights at or before target, and nights under 7 h
- weight: **direction only** — down / flat / up / not recorded. Never numbers.
- food: one dish eaten twice, cook timing held, snack kept, any improvised food
- what got in the way: time, energy, soreness, work, sleep debt

Never ask me for a fact you can read from the files.

## 2. Write `review<N>.md`

Use exactly this structure:

```md
# Week N review — <one-line verdict>

## Verdict
<2–3 sentences: did the week do what it was planned to do?>

## Planned vs actual
| Area | Planned | Actual | Delta |
|---|---|---|---|
| Lifts | | | |
| Runs | | | |
| Walks / steps | | | |
| Sleep | | | |
| Food format | | | |

## What worked
<What moved, and the number that shows it moved. Cite the file it came from.>

## What didn't
<Separate execution failure from plan failure. A plan that was not executed is
not evidence the plan was wrong — say which one this was.>

## Diff from week N-1
<What changed between the two plans, what that change bought, what it cost.>

## One change for week N+1
<Exactly one change, or "hold". Never two. Name the metric that will show whether
it worked.>

## Data gaps
<What we still do not know, and what would measure it next week.>

## Evidence notes
<Label every causal claim strong / mixed / weak / unknown, and link the source
when the claim is not obvious from the plan files.>
```

Rules for the file:

- **No body weight, height, BMI, waist or body-composition numbers anywhere.** The
  repo is pushed to a remote. Direction only ("down", "flat", "up").
- Grams only. No calories, no kcal totals.
- Never write the word `AGENT.md` forbids.
- Do not invent numbers or studies. `not recorded` is a valid entry.
- If the evidence contradicts a recommendation, say so and follow the stronger
  evidence.

## 3. Commit and push

Before staging anything:

1. Run `git status --short` and `git remote -v`. If this is not a git repository or
   has no remote, **stop** and report exactly what is missing. Do not run `git init`
   and do not invent a remote.
2. Confirm `.ntfy-topic`, `.ntfy-credentials`, `.ntfy-token` and `.ntfy-server` are
   ignored: `git check-ignore -v .ntfy-topic .ntfy-credentials .ntfy-token .ntfy-server`.
   If any is not ignored, add it to `.gitignore` and re-check. **Never print the
   contents of those files, and never stage them.**

Then:

```sh
git add -A
git commit -m '$REVIEW: week <N> review'
git push
```

The commit message must begin with the literal prefix `$REVIEW`. If `git push`
fails, report the remote's error verbatim and leave the commit in place — do not
force-push.

Report: the file path, the commit hash, the branch, the remote, and the exact
commit message.
