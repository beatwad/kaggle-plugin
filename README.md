# kaggle-plugin

Claude Code plugin for Kaggle competitions. It uses a locally patched copy of the community [`kaggle-mcp-server`](https://github.com/Galaxy-Dawn/kaggle-mcp) in [`server/`](server/), with the discussion tools fixed to fetch threads and comments by ID (see [`server/PATCHES.md`](server/PATCHES.md)). It adds three skills:

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

`competition-recon` also uses the Kaggle CLI (`uvx kaggle`, no install needed) to save notebooks to disk. It reads the same credentials.

## Install

From a shell (works for both the terminal and the VS Code extension):

```
claude plugin marketplace add /home/alex/Courses/Kaggle/kaggle-plugin
claude plugin install kaggle-plugin@kaggle-local
```

In the terminal version of Claude Code you can run the same thing as `/plugin marketplace add ...` and `/plugin install ...`. `/plugin` isn't available in the VS Code chat panel.

The plugin is installed for your user and loads in every new session. Check with `claude plugin list`. For quick testing without installing: `claude --plugin-dir /home/alex/Courses/Kaggle/kaggle-plugin`.

## Usage

Open your competition folder (in VS Code, as the workspace) and start a new Claude session, then:

```
/kaggle-plugin:competition-recon playground-series-s5e10
/kaggle-plugin:data-setup playground-series-s5e10
/kaggle-plugin:submit playground-series-s5e10 submissions/v1.csv
```

Or just ask in plain language ("do recon on ...", "submit v1.csv") and the skill loads automatically.

Files are created in the current folder: `notes/`, `data/raw/`, `refs/`.

Don't run Claude from the plugin folder itself. There, `.mcp.json` is read as a project config, `${CLAUDE_PLUGIN_ROOT}` isn't set, and the Kaggle server fails to start.
