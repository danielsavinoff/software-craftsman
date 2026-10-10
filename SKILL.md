---
name: software-craftsman
description: "Build or extend software using hexagonal architecture, CQRS, validated domain models, and transactional persistence. Use when scaffolding a project or implementing application use cases and adapters with these conventions. Supported: Go, Node.js."
---

# Software Craftsman

Supported: Go, Node.js

Apply these conventions to the requested work. Respect the user's chosen language, stack, and project instructions; in an existing project, avoid restructuring unrelated code.

## Language Guidance

For Go work, read [languages/go/conventions.md](languages/go/conventions.md). Reusable Go snippets live in [languages/go/snippets/](languages/go/snippets/); inspect and adapt only the snippets needed for the task.

For Node.js work, read [languages/nodejs/conventions.md](languages/nodejs/conventions.md).

## Architecture

- Use SOLID principles: single responsibility, open/closed, Liskov substitution, interface segregation, and dependency inversion.
- Use enums for fixed sets of values and named constants for other fixed values; do not use magic numbers or strings.
- Use hexagonal architecture: keep domain and application logic independent of delivery mechanisms and infrastructure. Use CQRS to separate commands from queries.
- Keep entry points under `cmd/{api,cli,etc}`.
- Keep application, domain, and infrastructure code under `internal/`:
  - `internal/app/{command,query,port}`: command handlers, query handlers, and boundary interfaces.
  - `internal/domain`: domain models and rules.
  - `internal/infra`: external adapters.
- If the project uses RESTerm, store request files in `api/requests/{name}.http`.
- If the project has an API, specify it in `api/openapi.yaml`.
- Use consistent casing for REST API fields across the whole project, including request and response payloads, the OpenAPI specification, and serialization mappings.
- Store deployment configuration under `deploy/{docker,k8,other}/{dev,prod,stage}` as applicable. Name Docker Compose files `docker-compose.development.yaml` and `docker-compose.production.yaml`. Name environment files `.env.development` and `.env.production`, and provide corresponding `.env.development.example` and `.env.production.example` files with example values.
- Document project requirements in `README.md`, including prerequisites and configuration, and explain how to start the project using the available deployment configuration and how to run it locally.
- Write `README.md` with minimal words and complexity; exclude filler.
- Use the applicable templates from [github/gitignore](https://github.com/github/gitignore) for project `.gitignore` files. Replace environment-file ignore rules such as `.env` with:

  ```gitignore
  # Environment files, except checked-in examples
  .env*
  !.env*.example
  ```

## Data and Events

- If a project does not use an ORM, prefer raw SQL queries in repository implementations for performance. Validate and sanitize user input, and use parameterized queries for user-supplied values to prevent SQL injection.
- Run SQL queries within a database transaction. Use a saga to coordinate a workflow that spans independent transactions or systems.
- Use the outbox pattern to reliably publish events alongside database changes.
- If a project uses a cache, implement caching as a repository wrapper: on reads, return cached data when present; otherwise call the wrapped repository and store successful results using a supplied caching function. On saves, call the wrapped repository first, then use the caching function to update or populate the cache after a successful save.
