# Go Conventions

Read this file when creating or changing Go code with software-craftsman. Apply it together with the shared rules in `SKILL.md`.

## File Naming

- Use snake_case for Go source filenames, for example `transaction_runner.go`.

## Architecture

- Each `cmd/{cli,api}` entry point has a `main.go` containing only `main`; call `Bootstrap` from its `bootstrap.go` to construct and inject dependencies.
- Put persistence adapters under `internal/infra/persistence/{database}` with one folder per database.
- Each delivery adapter (for example, REST, WebSocket, or TUI) declares its required use cases in its own `use_cases.go`; inject those dependencies through `Bootstrap`.
- Put shared Go packages intended to be imported by other projects under `pkg/`.
- Use [golang-migrate](https://github.com/golang-migrate/migrate) for database migrations. Store migration files under `migrations/{database}`, with one folder per database (for example, `migrations/sqlite` and `migrations/postgres`).

## Go Source Formatting

- Use `gofmt` as the formatter for Go source files.
- In every `.go` file, including tests, put each argument of a call with multiple arguments on its own line, with a trailing comma after the final argument.
- Separate distinct logical blocks with a blank line. Keep statements in one continuous operation together.

## Domain Model Construction

- Initialize each domain model through a `New` constructor that validates all invariants before returning it.
- Keep each domain model and its constructor in its own source file; keep each enum in its own source file.
- Give every domain model struct field an explicit `json` tag with a snake_case field name (for example, `json:"user_id"`).
- Define repository interfaces in the domain package. Name each repository interface file `{name}_repository.go` (for example, `exercise_repository.go`).
- Each repository method must take `context.Context` as its first parameter.
- Keep each repository method to at most two return values.
- A repository method that finds a single model must return a not-found error when no match exists. The use case must handle that error appropriately or propagate it to the infrastructure delivery adapter.
- Repository method names must not repeat the repository's name or its components. For example, use `Find` and `Save` on `IdentityCredentialsRepository`, without `Identity` or `Credentials` in the method names.
- Do not put blank lines between repository methods unless they separate logical groups.
- Do not use `Valid` methods as a substitute for constructor validation. Call constructors at persistence and application boundaries instead of assembling domain models directly.
- Define each error in the package or subpackage that owns the condition, in that package's `errors.go` file. Do not create errors inline with `errors.New`; use the package's declared errors.

## Snippets

Store reusable Go code in [snippets/](snippets/), preserving its intended project paths. Adapt package names and integrate only the files needed for the requested use case.

For transaction coordination, inspect these snippets:

- [Application transaction port](snippets/internal/app/port/transaction_runner.go): a generic callback boundary for application handlers.
- [SQL transaction runner](snippets/internal/infra/persistence/transaction_runner.go): propagates transactions through context, reuses an existing transaction, and commits or rolls back transactions it owns.
- [SQL executor](snippets/internal/infra/persistence/executor.go): selects the active transaction from context, with a database fallback.

The runner and executor belong together. Although the executor supports a database fallback, SQL operations following this skill's conventions must run inside a transaction. An inner runner reuses the outer transaction; use a saga when the workflow requires independent transactions or systems.
