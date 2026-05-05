# GitHub Copilot Project Instructions — cloudopt

## Project Context

Read-only Python CLI for collecting Azure VM inventory + performance metrics.
Outputs JSON (primary artifact); `analyze` step produces Excel + local FastAPI dashboard.

## Language & Stack

- Python 3.11+, type hints on all public functions, `from __future__ import annotations`
- Pydantic v2 models, Typer CLI, Rich console output, FastAPI dashboard
- Azure SDK: azure-identity, azure-mgmt-compute, azure-mgmt-monitor, azure-mgmt-resourcegraph
- Async throughout with `asyncio` + token-bucket rate limiting (`throttle.py`)

## Key Constraints

- **Read-only**: NEVER write to Azure resources
- **No secrets in code**: auth via `DefaultAzureCredential` only
- **No `print()`**: use `rich.console.Console` for all output
- **JSON-first**: `collect` writes JSON; Excel/CSV are derived in `analyze`
- **Customer data is gitignored**: `output/` and `output-*/` are never committed

## Code Style

- Functions < 50 lines, files < 800 lines
- Immutable data: `@dataclass(frozen=True)` and Pydantic models, not plain dicts
- Explicit error handling — never silently swallow exceptions
- Validate all user-supplied paths and subscription IDs at CLI boundary

## Active Skills (Claude Code / Copilot)

When working on this project, apply these skills:

| Skill                           | When to Apply                                                     |
| ------------------------------- | ----------------------------------------------------------------- |
| `python-patterns`               | Any new Python module                                             |
| `python-testing`                | All test files in `tests/`                                        |
| `security-review`               | Any auth, credential, or API endpoint code                        |
| `tdd-workflow`                  | New collectors, analyzers, or exporters                           |
| `verification-loop`             | Before any release or PR merge                                    |
| `api-design`                    | FastAPI routes in `dashboard/app.py`                              |
| `backend-patterns`              | Async service layer patterns                                      |
| `content-hash-cache-pattern`    | Caching expensive Azure API responses                             |
| `azure-compliance`              | Validating the tool's own Azure access patterns                   |
| `azure-rbac`                    | Documenting minimum required RBAC roles                           |
| `architecture-decision-records` | Capturing design decisions (throttle strategy, JSON-first output) |
| `deployment-patterns`           | CI/CD and packaging                                               |
| `docker-patterns`               | Containerization                                                  |

## Testing Requirements

- 80%+ coverage target (`pytest --cov=cloudopt`)
- Mock Azure SDK clients — never make real API calls in tests
- Use `pytest-asyncio` for all async collectors
- Factory patterns for building test fixtures (VMs, metrics, quota data)

## Security Checklist (run before every PR)

- [ ] No subscription IDs or tenant IDs in logs at INFO level
- [ ] No hardcoded credentials, keys, or connection strings
- [ ] All file paths validated before read/write
- [ ] Subscription ID format validated (UUID pattern)
- [ ] No `eval()`, `exec()`, or `subprocess` with user-controlled input
