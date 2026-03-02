---
name: test
description: Run tests in Docker with the correct image and flags for the current project.
argument-hint: "[optional: test path or pytest args]"
disable-model-invocation: true
---

# Run Tests

Run the project's tests in Docker using the appropriate configuration.

## Determine the right command

Check what project you're in and select the correct Docker test command:

### Grillex (has `grillex-test` Docker image)
```bash
docker run --rm -v $(pwd):/app grillex-test pytest $ARGUMENTS -v --no-header -o 'addopts='
```
- Uses `grillex-test` image (has C++ deps, gmsh). Plain `python:3.11` will NOT work.
- The `-o 'addopts='` overrides setup.cfg's `--cov` args which cause issues.

### Standard Python project (no special C++ deps)
```bash
docker run --rm -v $(pwd):/app -w /app python:3.11 sh -c "pip install -e '.[dev]' && pytest $ARGUMENTS"
```

### With private PyPI dependencies
```bash
docker run --rm -v $(pwd):/app -w /app python:3.11 sh -c \
  "pip install --extra-index-url https://pypi-dev.jimzwartveld.com/simple/ -e '.[dev]' && pytest $ARGUMENTS"
```

## How to decide
- If `grillex-test` image exists (check with `docker images grillex-test --format '{{.Repository}}'`), use the grillex command
- If `setup.cfg` or `pyproject.toml` references `pypi-dev.jimzwartveld.com`, use the private PyPI command
- Otherwise use the standard Python command

## Behavior
- If no arguments are given, run the full test suite
- If a test path is given (e.g., `tests/python/test_ec3.py`), run only that
- If pytest args are given (e.g., `-k test_name`), pass them through
- Report results clearly: passed, failed, errors
