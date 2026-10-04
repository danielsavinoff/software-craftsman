## Architecture

- Use hexagonal architecture: keep domain and application logic independent of delivery mechanisms and infrastructure.
- Use CQRS: organize application behavior into separate query and command handlers.
- `cmd/cli` and `cmd/api` contain the CLI and API entry points and dependency wiring.
- `app/query`, `app/commands`, and `app/ports` contain query handlers, command handlers, and application boundary interfaces.
- `infra` contains adapters for external systems, including persistence and delivery-related integrations.
- `infra/persistence` contains persistence adapters, with a separate subfolder for each database (for example, `infra/persistence/sqlite`).

## Go Source Formatting

- In every `.go` source file, including tests, write calls to functions or methods with more than one argument using one argument per line, with a trailing comma after the final argument.
- Separate distinct logical blocks of Go code with a blank line. Keep statements that form one continuous operation together.

## Domain Model Construction

- Initialize every domain model through a constructor function whose name starts with `New` and validates all model invariants before returning the model.
- Keep each domain model in its own source file with its constructor in that file; keep each enum in its own source file.
- Do not add `Valid` methods as a substitute for constructor validation. Call the constructor at persistence and application boundaries instead of assembling domain model values directly.
- Define domain errors only in package's `errors.go` file.
