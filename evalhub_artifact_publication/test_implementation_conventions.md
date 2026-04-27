# Test Implementation Conventions - eval-hub

**Source**: odh-test-context/tests/eval-hub.json

## Framework
- **Primary**: Go test (stdlib + godog for BDD)
- **Secondary**: pytest (Python server tests)

## Test Organization
- **Unit Tests**: `*_test.go` alongside code in `auth/`, `internal/`, `cmd/`
- **Integration/FVT Tests**: `*.feature` (Gherkin/godog BDD) in `tests/features/`
- **Step Definitions**: `tests/features/step_definitions_test.go`
- **Test Data**: `tests/features/test_data/`
- **Python Tests**: `python-server/tests/` with `@pytest.mark.unit`

## Naming
- Go: `TestXxx` functions
- Gherkin: `Scenario:` blocks with `@tag` annotations
- Python: `test_xxx` functions

## BDD Tags
- `@cluster` — requires Kubernetes cluster
- `@mlflow` — requires MLflow integration
- `@local` — local-only tests
- `@negative` — error-path tests
- `@gha-wheel-sanity` — GHA wheel validation subset

## Mock Patterns
- godog scenarios with Given/When/Then steps
- In-memory SQLite for storage

## Linting
- `go vet ./...`, `go fmt ./...`
- `ruff check .` (Python), `mypy .` (Python)
- Pre-commit hooks enforce all checks

## Import Style
- Standard Go imports with grouping
- Python managed by ruff (isort rules)

## Version Requirements
- Go 1.25.0, Python 3.11+
- Conventional commits via commitizen
