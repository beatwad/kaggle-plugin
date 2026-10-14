---
name: competition-recon
description: Research a Kaggle competition and write a briefing to notes/recon.md - task, metric, submission format, timeline, top public notebooks and insights from discussions. Use when the user gives a competition slug and wants to understand the competition, start working on it, or asks what the top notebooks or discussions say.
---

# Competition recon

Input: a competition slug (the part of the URL after `kaggle.com/competitions/`). Ask for it if not given.

All files go in the current working directory, not the plugin directory.

## Steps

1. **Overview** - `competition_get`, `competition_files`, `competition_data_summary`. Extract:
   - the task and data type (tabular, images, text, time series...);
   - the evaluation metric, exactly as stated;
   - the submission format (columns, id field, what the prediction is);
   - deadlines (entry/merger deadline, final deadline) and the daily submission limit.
2. **Leaderboard** - `competition_leaderboard`: the top score and the spread across the top 20. For an ended competition this is the private leaderboard. Skip rows like `perfect_submission.csv`, which are host benchmarks.
3. **Top notebooks** - `competition_top_kernels`, then download the top 5 with the Kaggle CLI, which writes straight to disk:
   `uvx kaggle kernels pull <user>/<slug> -p refs/<user>__<slug>`
   - Do **not** use the `kernel_pull` tool: it returns the whole notebook (often 100K-1M characters) into context.
   - Never push anything back to Kaggle.
   - Skim each saved notebook with a short script (markdown headings, class names, `/kaggle/input/` datasets, blend weights, CV splitter, sequence length) and read only the cells that matter. Don't read whole notebooks.
   - For each, note: approach/model, feature engineering, CV scheme, CV score and LB score if stated (code comments often list per-model CV).
   - Scores aren't exposed by the API; only quote a score if it appears in the title or the notebook itself.
4. **Discussions** - `discussions_list` with `competition` set (returns the most voted threads), then `discussion_detail` and `discussion_comments` for the most relevant 10-15 threads. `discussions_search` is not scoped to a competition, so always put the competition title in the query, e.g. `"<title> leak"`, `"<title> CV LB"`, `"<title> shake"`, `"<title> external data"`, and drop results from other competitions. Most insight is in the comments, so read `discussion_comments` for every thread you use, not just the opening post. Look for:
   - data leaks and their status (confirmed, fixed by hosts, allowed?);
   - CV/LB correlation problems and which CV schemes people trust;
   - shake-up risk (small or unrepresentative public test set);
   - useful external data and whether rules allow it;
   - host clarifications that change the rules or data.
5. **Solutions (ended competitions only)** - if the deadline has passed, find the top solution write-ups: `discussions_search` with `"<title> place solution"`, plus any `Nth place` threads from step 4. (`discussions_solutions`, `discussions_writeups` and `source_type` filters often return nothing: since 2025 most solutions are in Kaggle's Writeups section, which the server doesn't index.) Read the top 5 by placement and note the model, key tricks, CV scheme and what did not work. If the top-3 write-ups are missing, say so and fill the gap from the top teams' public inference notebooks found in step 3.
6. **Write `notes/recon.md`** with the structure below. If it already exists, ask before overwriting.

## notes/recon.md structure

```markdown
# <Competition title> (<slug>)

_Recon date: <YYYY-MM-DD>_

## Task
## Data
## Metric
## Submission format
## Timeline and limits
## Leaderboard snapshot
## Top notebooks
| Notebook | Approach | CV scheme | CV | LB | Saved to |
## Discussion insights
### Leaks
### CV vs LB
### Shake-up risk
### External data
### Host clarifications
## Top solutions
<ended competitions only: placement, approach, key tricks>
## Takeaways
<3-5 bullets: what to try first, what to avoid>
```

Link every insight to its source thread or notebook URL. Mark anything unconfirmed as such.
