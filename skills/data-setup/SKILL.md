---
name: data-setup
description: Download a Kaggle competition's data into data/raw/, unzip it and preview the files. Use when the user wants to get, download or set up the data for a Kaggle competition.
---

# Data setup

Input: a competition slug. Ask for it if not given. All paths are relative to the current working directory.

## Steps

1. **List files** - `competition_files`. Show names and sizes, and the total size. If the total is over 1 GB, ask before downloading.
2. **Check for existing data** - if `data/raw/` already contains any of these files, list them and ask before overwriting. Never overwrite without confirmation.
3. **Download** - for each file, call `competition_download` with `file_name` set. It returns a signed download URL, not the file. Fetch it with `curl -fL -o "data/raw/<file path>" "<url>"`, creating subfolders as needed. Always quote the URL.
   - A 403 error means the competition rules have not been accepted. Stop and tell the user to open `https://www.kaggle.com/competitions/<slug>/rules`, accept the rules, then run this again. This cannot be done through the API.
4. **Unzip** - extract any `.zip` archives in `data/raw/` in place, then delete the archive only if extraction succeeded.
5. **Preview** - for every file in `data/raw/`:
   - name and size;
   - for tabular files (`.csv`, `.parquet`, `.tsv`): row count, column names and dtypes, first 5 rows;
   - for other files (images, audio...): count per folder and one example path.
6. **Summarize** - which file is train, which is test, which is `sample_submission.csv`, and the target column if it can be identified.
