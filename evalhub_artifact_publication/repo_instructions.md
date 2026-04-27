# Repository-Specific Instructions

## From CLAUDE.md

### Testing

```bash
make test               # Run unit tests (./auth/..., ./internal/..., ./cmd/...)
make test-fvt           # Run FVT tests using godog (tests/features/...)
make test-all           # Run unit tests, FVT, then FVT against a started server

# Run specific unit test
go test -v ./internal/eval_hub/handlers -run TestHandleName

# Run specific FVT test
go test -v ./tests/features -run TestFeatureName
```

### Architecture
- Standard Go project layout: `cmd/`, `internal/`, `pkg/api/`
- ExecutionContext pattern: handlers take `*executioncontext.ExecutionContext` + HTTP wrappers
- Routing: `net/http.ServeMux`, no web framework
- Unit tests alongside code in `*_test.go` files, standard `testing` package
- FVT: BDD-style tests using godog in `tests/features/`
- Conventional commits enforced: `feat:`, `fix:`, `chore:`, etc.

### FVT Tags
- `@cluster` — tests requiring Kubernetes cluster
- `@local` — local-only tests
- `@mlflow` — tests requiring MLflow integration
- `@negative` — negative/error-path tests
- `@gha-wheel-sanity` — subset for GHA wheel validation
