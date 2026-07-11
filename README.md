# ollama-actions-week

A set of GitHub Actions pipelines that exercise real CI/CD mechanics end to end —
multi-job orchestration, matrix builds, scheduling, PR automation, and an
AI-powered pull-request review that runs a local LLM inside the runner.

All workflows run on GitHub-hosted runners against this repo; the runs and their
artifacts are visible under the [Actions tab](../../actions).

## Workflows

| Workflow | Trigger | What it does |
|----------|---------|--------------|
| [`push-workflow.yml`](.github/workflows/push-workflow.yml) | push to `main` | Multi-job pipeline (`setup → build → report`) with job dependencies, environment variables, branch/path conditionals (`dorny/paths-filter`), an OS × environment test matrix, artifact upload/download between jobs, a `$GITHUB_STEP_SUMMARY` dashboard, and failure handling. |
| [`pr-workflow.yml`](.github/workflows/pr-workflow.yml) | pull request | Validates required files, reads PR labels, and posts an automated summary comment back on the PR via the `gh` CLI. |
| [`ai-pr-review.yml`](.github/workflows/ai-pr-review.yml) | pull request | Installs Ollama in the runner, pulls `llama3.2:1b`, and runs a **matrix AI review** across three prompt styles (concise / detailed / technical). Each style's output is uploaded as an artifact, then a dependent job downloads and compares them all. |
| [`scheduled-workflow.yml`](.github/workflows/scheduled-workflow.yml) | weekly cron + manual dispatch | Scheduled job that logs a timestamped run; also triggerable on demand via `workflow_dispatch`. |

## CI/CD concepts demonstrated

- Multi-job pipelines with `needs`-based dependency graphs
- Build matrices (across prompt styles, and across OS × environment)
- Passing state between jobs with `upload-artifact` / `download-artifact`
- Conditional steps (`if:`), `success()` / `failure()` gates, and `fail-fast` control
- Repository, environment, and job-level variables
- Cron scheduling and manual `workflow_dispatch`
- Running a local LLM (Ollama) inside a runner and driving it from the pipeline
- Automated PR comments using `GITHUB_TOKEN` and the `gh` CLI

## Where this goes next

[`ollama-actions-week-modular`](https://github.com/gsharp23/ollama-actions-week-modular)
takes the AI-in-CI idea and rebuilds it as a proper, tested Python package with
caching, config-driven model routing, and git-based result history.
