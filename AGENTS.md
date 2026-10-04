# Architecture

- Use hexagonal architecture: keep domain and application logic independent of delivery mechanisms and infrastructure. Use CQRS to separate commands from queries.
- Each `cmd/{cli,api}` entry point has a `main.go` containing only `main`; call `Bootstrap` from its `bootstrap.go` to construct and inject dependencies.
- Keep application, domain, and infrastructure code under `internal/`:
  - `internal/app/{command,query,port}`: command handlers, query handlers, and boundary interfaces.
  - `internal/domain`: domain models and rules.
  - `internal/infra`: external adapters. Put persistence adapters under `internal/infra/persistence/{database}` with one folder per database. Each delivery adapter (for example, REST, WebSocket, or TUI) declares its required use cases in its own `use_cases.go`; inject those dependencies through `Bootstrap`.
- Put shared Go packages intended to be imported by other projects under `pkg/`.
- Use [golang-migrate](https://github.com/golang-migrate/migrate) for database migrations. Store migration files under `migrations/{database}`, with one folder per database (for example, `migrations/sqlite` and `migrations/postgres`).
- If the project uses RESTerm, store request files in `api/requests/{name}.http`.
- If the project has an API, specify it in `api/openapi.yaml`.
- Store deployment configuration under `deploy/{docker,k8,other}/{dev,prod,stage}` as applicable. Name Docker Compose files `docker-compose.development.yaml` and `docker-compose.production.yaml`. Name environment files `.env.development` and `.env.production`, and provide corresponding `.env.development.example` and `.env.production.example` files with example values.
- Document project requirements in `README.md`, including prerequisites and configuration, and explain how to start the project using the available deployment configuration and how to run it locally.
- Write `README.md` with minimal words and complexity; exclude filler.

# Data and Events

- Run SQL queries within a database transaction. Use a saga to coordinate a workflow that spans independent transactions or systems.
- Use the outbox pattern to reliably publish events alongside database changes.

# Go Source Formatting

- In every `.go` file, including tests, put each argument of a call with multiple arguments on its own line, with a trailing comma after the final argument.
- Separate distinct logical blocks with a blank line. Keep statements in one continuous operation together.

# Domain Model Construction

- Initialize each domain model through a `New` constructor that validates all invariants before returning it.
- Keep each domain model and its constructor in its own source file; keep each enum in its own source file.
- Define repository interfaces in the domain package. Name each repository interface file `{name}_repository.go` (for example, `exercise_repository.go`).
- Each repository method must take `context.Context` as its first parameter.
- Do not use `Valid` methods as a substitute for constructor validation. Call constructors at persistence and application boundaries instead of assembling domain models directly.
- Define each error in the package or subpackage that owns the condition, in that package's `errors.go` file. Do not create errors inline with `errors.New`; use the package's declared errors.
