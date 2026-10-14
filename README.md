# kaggle-plugin

Claude Code plugin for Kaggle competitions. It uses the community [`kaggle-mcp-server`](https://github.com/Galaxy-Dawn/kaggle-mcp) and adds three skills:

| Skill | What it does |
|---|---|
| `competition-recon` | Briefing in `notes/recon.md`: task, metric, timeline, top notebooks (saved to `refs/`) and discussion insights |
| `data-setup` | Downloads data to `data/raw/`, unzips and previews it |
| `submit` | Validates against `sample_submission.csv`, asks for confirmation, submits, logs the score to `notes/submissions.md` |

## Setup

1. Install [uv](https://docs.astral.sh/uv/) (provides `uvx`).
2. Create a Kaggle API token: Kaggle → Settings → API → Create New Token.
3. Provide it to the server in one of two ways:
   - `export KAGGLE_API_TOKEN=KGAT_...` in your shell profile, or
   - save `kaggle.json` to `~/.kaggle/kaggle.json` and run `chmod 600 ~/.kaggle/kaggle.json`.

Never commit the token.

## Install

```
/plugin marketplace add /home/alex/Courses/Kaggle/kaggle-plugin
/plugin install kaggle-plugin@kaggle-local
```

Restart Claude Code afterwards. For quick testing without installing: `claude --plugin-dir /home/alex/Courses/Kaggle/kaggle-plugin`.

## Usage

Run Claude in your competition folder, then:

```
/kaggle-plugin:competition-recon playground-series-s5e10
/kaggle-plugin:data-setup playground-series-s5e10
/kaggle-plugin:submit playground-series-s5e10 submissions/v1.csv
```

Or just ask in plain language ("do recon on ...", "submit v1.csv") and the skill loads automatically.

Files are created in the current folder: `notes/`, `data/raw/`, `refs/`.
