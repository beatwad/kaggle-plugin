---
name: submit
description: Validate a submission file against sample_submission.csv and submit it to a Kaggle competition after explicit user confirmation, then log the score to notes/submissions.md. Use when the user wants to submit predictions to Kaggle or check a submission file.
---

# Submit

Input: a competition slug and a submission file path. Ask for either if not given. Submissions are limited per day and cannot be undone.

## Steps

0. **Check the competition type** - `competition_get`. If it says `Kernels only: True`, this is a code competition: a CSV file cannot be submitted through the API. Stop and tell the user to submit from a Kaggle notebook (Submit button in the notebook editor, or the notebook's Output tab). The validation in step 1 can still be run on a local file if the user asks.
1. **Validate** against `data/raw/sample_submission.csv` (if missing, get the expected format from `competition_get` / `competition_files`). Check:
   - same columns, same order;
   - same number of rows;
   - same set of ids, and same order if the sample is ordered;
   - prediction dtypes match the sample (e.g. int labels vs float probabilities);
   - no NaN or empty values;
   - values in a plausible range for the metric (e.g. probabilities in [0, 1]).

   If any check fails, report it and stop. Do not submit.
2. **Show today's usage** - `competition_submissions`: count submissions made today (UTC, Kaggle resets daily limits at 00:00 UTC) versus the daily limit from `competition_get`. Show the last 3 submissions with scores.
3. **Confirm** - show the file, the submission message you will use, and the remaining submissions for today. Ask the user to confirm. Submit only on an explicit yes. Never skip this step, even if the user asked to submit earlier in the conversation.
4. **Submit** - `competition_submit` with the file and message.
5. **Get the score** - poll `competition_get_submission` (or `competition_submissions`) until the status is complete or errored, waiting about 30 seconds between checks, for up to 10 minutes. Show the public score, or the error.
6. **Log** - append a row to `notes/submissions.md` (create it with the header if missing):

```markdown
| Date (UTC) | File | Message | Public score |
|---|---|---|---|
```
