# Local patches

Base: `kaggle-mcp-server` 0.2.0 from PyPI (https://github.com/Galaxy-Dawn/kaggle-mcp), MIT license, see `LICENSE`.

All changes are in `src/kaggle_mcp/discussions.py` and `pyproject.toml`:

1. **`discussion_detail`**: the original searched for the competition slug, took the top 50 results and looked for the ID. Threads outside the top 50 came back as "not found". It now calls `get_topic` by ID. The `competition` argument is kept but unused.
2. **`discussion_comments`**: the original did a full-text search for comments containing the ID number, so it returned comments that link to the thread rather than the thread's own comments. It now pages through `list_comments` for the topic, sorts top-level comments by votes and includes nested replies.
3. Both tools convert the API's HTML to plain text (`_html_to_text`).
4. `mcp` is pinned below 2.0. The original didn't pin it, and mcp 2.x removed `mcp.server.fastmcp`.

To update from upstream: copy the new `kaggle_mcp` package over `src/kaggle_mcp`, then re-apply 1–3.
