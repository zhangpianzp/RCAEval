# Project Guidelines

## Code Style
- Keep changes minimal and localized; avoid broad refactors unless requested.
- Follow existing patterns in RCAEval/e2e and RCAEval/io for function signatures and data flow.
- Preserve existing dependency pinning style in requirements files.

## Architecture
- Core package is RCAEval.
- RCAEval/e2e contains end-to-end RCA method implementations.
- RCAEval/benchmark contains evaluation and metrics.
- RCAEval/graph_construction builds causal graphs.
- RCAEval/graph_heads ranks candidate root causes.
- RCAEval/io handles time-series preprocessing and loading.
- RCAEval/logparser handles log template and event parsing.

## Build and Test
- Recommended environment: Python 3.12 for main branch workflows.
- Editable install (default methods): pip install -e .[default]
- Optional RCD environment: pip install -e .[rcd]
- Run core tests: pytest tests/test.py
- Run quick smoke test: pytest tests/test.py::test_basic
- Example method run: python main-ase.py --method baro --dataset online-boutique --test

## Conventions
- New RCA methods should be added under RCAEval/e2e.
- Keep method interfaces compatible with existing callers in main.py and RCAEval/e2e/run.py.
- When returning ranking results, follow existing method output conventions used by benchmark/evaluation.
- Avoid changing Python-version gating logic unless explicitly requested.
- Before adding setup details, check docs first and link to them instead of duplicating.

## Common Pitfalls
- Some dependencies are version-sensitive (for example numpy < 2).
- Installing from source can fail on older pinned packages; prefer versions with wheels for Python 3.12.
- System packages such as graphviz and libxml2-dev may be required for full functionality.

## Documentation Map
- Project overview and quick start: README.md
- Environment setup and platform notes: docs/SETUP.md
- How to extend datasets and methods: docs/EXTENDING.md
- ASE reproduction workflow: legacy/README-ASE.md
