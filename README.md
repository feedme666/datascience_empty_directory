# Data Science Template & Container Starter

This repository couples a production-ready data science directory layout with the pieces you need to spin up a reproducible Docker/Dev Container workspace. Use it as a scaffold for analytics experiments, notebook-first exploration, and API handoff once your models are ready.

## Why This Template
- Opinionated folders that mirror a typical data-science lifecycle, from raw data capture to packaged models.
- Python environment is pinned with `pyproject.toml`/`uv.lock` so dependency resolution stays deterministic.
- `.devcontainer/devcontainer.json` mounts the repo into a Docker container (`python-base:latest`) so everyone codes against the same toolchain.
- Works equally well for notebook-driven work or Python package style development inside `src/`.

## Directory Map
```bash
.
├── data/                         # raw + processed datasets (kept locally, not versioned)
├── docs/                         # API notes, specs, decisions
├── models/                       # serialized artifacts + logs
├── notebooks/                    # 01→04 numbered workflow
├── references/                   # papers, datasets licenses, etc.
├── reports/
│   └── figures/                  # static exports for stakeholders
├── src/
│   ├── config/                   # settings, secrets templates
│   ├── data/                     # ingestion & loaders
│   ├── features/                 # feature engineering helpers
│   ├── models/                   # training/inference modules
│   ├── test/                     # unit / regression tests
│   └── main.py                   # orchestration entry point
├── .devcontainer/devcontainer.json
├── pyproject.toml
└── uv.lock
```

## Folder Roles
- **data**: `raw/` holds immutable source data. `processed/` stores cleaned tables or feature sets ready for modeling. Keep anything sensitive out of version control.
- **notebooks**: Four numbered stages (prep → EDA → features → training). Prefixing notebooks with numbers enforces execution order when collaborating.
- **src**: Production-grade code that mirrors notebook logic. Transition mature experiments here so they can be tested, linted, and containerized.
- **reports**: Slide decks, KPI exports, and publication-ready figures. Use `reports/reports.csv` as a tracker for runs or stakeholder notes.
- **docs** & **references**: Capture architectural decisions, API contracts, data dictionaries, and any supporting literature for compliance reviews.
- **models/logs**: Persist model binaries, metadata, and training logs for auditability.

## Local Python Workflow
1. Install [uv](https://github.com/astral-sh/uv) (or use `pip install uv`).
2. Sync dependencies into a virtual environment:  
   ```bash
   uv sync
   ```
3. Activate the environment (uv manages `.venv` automatically):  
   ```bash
   source .venv/bin/activate
   ```
4. Launch JupyterLab when you are ready to explore:  
   ```bash
   uv run jupyter lab --notebook-dir=notebooks
   ```
5. Use `uv run python src/main.py` to execute the orchestrated pipeline.

## Docker / Dev Container Workflow
The repository ships with `.devcontainer/devcontainer.json`, which expects an image named `python-base:latest`.

1. Build or pull the base image (point `-f` to the Dockerfile you maintain for this stack):  
   ```bash
   docker build -t python-base:latest -f path/to/Dockerfile .
   ```  
   _If you already host a preferred base image, update the `image` field instead._
2. Open the folder in VS Code and run “Dev Containers: Reopen in Container”, or start it manually:  
   ```bash
   docker run --rm -it \
     -v "$(pwd)":/workspaces \
     -w /workspaces \
     python-base:latest \
     bash
   ```
3. Inside the container, run `uv sync` (the `postCreateCommand` handles this automatically for Dev Containers) and proceed with notebooks or CLI tools.
4. When you are done experimenting, reclaim disk space by pruning stopped containers and dangling resources:  
   ```bash
   docker container prune -f
   # or use the broader cleanup
   docker system prune -a -f
   ```

## Recommended Workflow
- Keep exploratory notebooks lightweight; migrate stable code to `src/` modules with tests under `src/test/`.
- Store credentials or environment secrets in `src/config/` templates and load them via environment variables rather than committing plaintext secrets.
- Use `models/logs/` to snapshot every training run (hyperparameters + metrics) so you can reproduce results during deployment reviews.
- When promoting a model, export deliverables into `reports/figures/` alongside the supporting narrative for stakeholders.

## Contributing
1. **Fork & clone** the repository.
2. **Create a branch** (`git checkout -b feature/my-improvement`).
3. **Run tests/notebooks** relevant to your change.
4. **Commit & push**, then open a PR that links to any related issues.
5. **Discuss & iterate** on review feedback; keep documentation updated alongside code changes.

Questions or suggestions? Open an issue so we can refine the template together.

Run `docker container prune -f`
or `docker system prune -a -f` to remove unused containers.
