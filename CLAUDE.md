# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with this repository.

## Project Overview

PlanDev-CLI is a command-line interface and Python API for interacting with NASA's PlanDev mission planning system. It provides both interactive CLI commands and a programmatic Python interface for managing mission plans, models, scheduling, and more.

## Repository Structure

```
src/plandev_cli/
├── __main__.py          # Entry point (plandev-cli command)
├── app.py               # Typer application setup
├── plandev_host.py      # PlanDevHost class - handles API connections
├── plandev_client.py    # PlanDevClient class - main API interface
├── persistent.py        # Persistent session/configuration storage
├── commands/            # CLI command modules
│   ├── configurations.py  # Host configuration management
│   ├── plans.py           # Activity plan operations
│   ├── models.py          # Mission model operations
│   ├── scheduling.py      # Scheduling goal operations
│   ├── constraints.py     # Constraint operations
│   ├── expansion.py       # Command expansion operations
│   └── metadata.py        # Metadata operations
├── schemas/             # Data models (attrs-based dataclasses)
│   ├── api.py           # API response schemas
│   └── client.py        # Client-side data structures
└── utils/               # Utility modules
    ├── sessions.py      # Session management utilities
    ├── configurations.py # Configuration utilities
    ├── prompts.py       # Interactive prompt utilities
    └── serialization.py # JSON/data serialization

tests/
├── unit_tests/          # Unit tests (can run without PlanDev)
└── integration_tests/   # Integration tests (require local PlanDev)
```

## Development Setup

```bash
# Create and activate virtual environment
python3 -m venv venv
source venv/bin/activate

# Install in editable mode
python3 -m pip install -e .

# Install dev dependencies with Poetry
poetry install
```

## Common Commands

### Running Tests

```bash
# Unit tests (no PlanDev instance required)
cd tests
python3 -m pytest unit_tests/

# Integration tests (requires local PlanDev via docker-compose)
docker-compose -f docker-compose-test.yml up -d
python3 -m pytest integration_tests/
```

### Code Quality

```bash
# Run pre-commit hooks
pre-commit run --all-files

# Format code with black
black src/ tests/

# Lint with flake8
flake8 src/ tests/
```

### CLI Usage

```bash
# View help
plandev-cli --help

# Activate a session with a PlanDev host
plandev-cli activate

# Check session status
plandev-cli status

# List plans
plandev-cli plans list
```

## Key Architecture Patterns

### PlanDevHost and PlanDevClient

- `PlanDevHost`: Manages connection to a PlanDev instance, handles authentication, and issues GraphQL requests
- `PlanDevClient`: Provides high-level API methods built on top of PlanDevHost

```python
from plandev_cli.plandev_client import PlanDevClient
from plandev_cli.plandev_host import PlanDevHost

host = PlanDevHost(graphql_url, gateway_url)
host.authenticate(username, password)
client = PlanDevClient(host)
```

### CLI Commands with Typer

Commands use the Typer library and follow this pattern:
- Each command module creates a `typer.Typer()` app
- Commands are registered in `app.py`
- Use `@app.command()` decorator for subcommands
- Interactive prompts when arguments aren't provided

### Data Classes with attrs

Data structures use the `attrs` library:
```python
from attrs import define

@define
class ActivityPlan:
    name: str
    id: int
    # ...
```

## Important Files

- `pyproject.toml` - Project configuration and dependencies (Poetry)
- `docker-compose-test.yml` - Local PlanDev instance for integration tests
- `.pre-commit-config.yaml` - Pre-commit hooks configuration
- `.flake8` - Flake8 linting configuration

## Version Management

- Version in `pyproject.toml` is `0.0.0-dev0` on develop branch
- Releases follow semantic versioning
- Uses gitflow workflow (develop -> release -> main)
