# mcp-project

Study project developed while following the **MCP: Build Rich-Context AI Apps with Anthropic** course (DeepLearning.AI), implementing a Python **Model Context Protocol (MCP)** server for searching and retrieving scientific papers from arXiv.

> This repository follows the course tutorials and will serve as a base for experiments and new implementations after completing the lessons.

## Overview

The MCP server is defined in `src/mcp_project/research_server.py` using `FastMCP("research")` over `stdio` transport, exposing tools to search papers on arXiv and store their metadata locally as JSON files inside the `papers/` directory (ignored by Git).

### Available Tools (`research_server.py`)

- **`search_papers(topic: str, max_results: int = 5) -> List[str]`**
  - Searches arXiv for papers matching `topic`, sorted by relevance (`arxiv.SortCriterion.Relevance`).
  - Saves paper metadata (`title`, `authors`, `summary`, `pdf_url`, `published`) to `papers/<topic>/papers_info.json` (with the topic lowercased and spaces replaced by `_`).
  - Returns the list of short paper IDs found.

- **`extract_info(paper_id: str) -> str`**
  - Scans subdirectories under `papers/` for `paper_id` inside `papers_info.json` files.
  - Returns the formatted JSON metadata for the paper, or a message stating that no saved information was found for the given ID.

## Requirements

- Python `>= 3.10` (set to `3.12` in `.python-version`)
- [uv](https://docs.astral.sh/uv/) for virtual environment and dependency management

### Core Dependencies

Defined in `pyproject.toml`:

- `arxiv>=4.0.3`
- `mcp>=1.2,<2`

## Project Structure

```text
mcp-project/
├── .gitignore
├── .python-version
├── pyproject.toml
├── README.md
├── uv.lock
└── src/
    └── mcp_project/
        ├── __init__.py          # Default entrypoint (mcp-project script)
        └── research_server.py   # MCP server (FastMCP "research")
```

## Setup and Running

1. **Sync the environment and dependencies:**

   ```bash
   uv sync
   ```

2. **Run the MCP server (`stdio`) from the project root:**

   ```bash
   uv run src/mcp_project/research_server.py
   ```

   Because the server uses `transport='stdio'`, it produces no interactive terminal output and waits for JSON-RPC messages over `stdin`/`stdout` (the standard behavior for MCP client integration).

3. **Run from another directory (e.g., MCP client configuration):**

   ```bash
   uv --directory /Users/viniciusmartins/Development/tutoriais/mcp-project run src/mcp_project/research_server.py
   ```

> **Note on the `mcp-project` script:** the entrypoint defined under `[project.scripts]` in `pyproject.toml` (`mcp_project:main`) currently points to `src/mcp_project/__init__.py` and does not start `research_server.py`.

## Next Steps and Experiments

After finishing the course tutorials, this section tracks planned changes and additional experiments:

- [ ] Add new MCP `resources` and `prompts` to the server
- [ ] Build or connect an MCP client to test tool calls end-to-end
- [ ] Handle edge cases (e.g., calling `extract_info` before the `papers/` directory exists)
