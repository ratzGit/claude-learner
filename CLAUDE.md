# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is a learning/experiments directory containing:
- **Jupyter notebooks** (`Lesson *.ipynb`, `Exercise *.ipynb`) — Claude API lessons and exercises
- **Portal Web Scraper** — A Python tool for extracting data from SSO-protected portals, living in a git worktree at `.claude/worktrees/nifty-chatterjee-bee17e/`

## Environment Setup

```bash
# Activate the virtual environment (Windows)
.venv\Scripts\activate

# Install dependencies (from the scraper worktree)
pip install -r .claude/worktrees/nifty-chatterjee-bee17e/requirements.txt
```

## Portal Scraper — Commands

All commands run from the worktree root (`.claude/worktrees/nifty-chatterjee-bee17e/`):

```bash
# Run the scraper with a config
python scraper.py --config configs/<config_file>.json

# Test connection only (no report generated)
python scraper.py --config configs/<config_file>.json --test-connection

# Launch the configuration UI (opens at http://localhost:5000)
python configurator.py

# Run all tests
pytest

# Run a single test file
pytest tests/test_parser.py

# Run tests with coverage
pytest --cov=modules

# Run tests in parallel
pytest -n auto

# Run only unit tests
pytest -m unit
```

## Portal Scraper — Architecture

The scraper pipeline flows: **config** → **cookie extraction** → **HTTP fetch** → **HTML parse** → **report output**

- `scraper.py` — Entry point; orchestrates the full pipeline via `run_scraper(config_path)`
- `configurator.py` — Flask web UI for non-technical users to create JSON configs saved to `configs/`
- `modules/cookie_extractor.py` — Reads live browser session cookies (Chrome/Firefox) via `browser-cookie3`
- `modules/http_client.py` — `AuthenticatedHTTPClient` sends requests with extracted cookies
- `modules/parser.py` — BeautifulSoup/lxml HTML parsing using CSS selectors from config
- `modules/reporter.py` — `ReportGenerator` outputs to `reports/` as CSV or Excel (via pandas/openpyxl)
- `modules/logger.py` — Centralized logging; logs written to `reports/scraper.log`

Configs are JSON files in `configs/`. Required fields: `report_name`, `portal_url`, `data_selector`, `output_format`. Reports are saved to `reports/` with `YYYY-MM-DD_HH-MM-SS_<report_name>` timestamps.

Tests live in `tests/` with one test file per module. `conftest.py` holds shared fixtures.
