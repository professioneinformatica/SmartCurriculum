---
description: 'Clean Architecture plus Domain-Driven Design guidance for the solution layers'
applyTo: '**/*.cs, **/*.razor, **/*.razor.cs'
---

## Architecture Overview

- Keep the solution layered: Blazor Web UI, API (Web or Minimal) project, Application services, Domain model, and Repository/Infrastructure adapters.
- Depend on abstractions going inward; outer layers reference inner layers via interfaces and DTOs.
- Move shared concerns (logging, configuration, DTOs) into dedicated projects rather than spreading them across layers.
- Favor small, cohesive projects that match bounded contexts in your domain.

## Domain Layer

- Model the ubiquitous language: entities, value objects, aggregates, domain events, and domain services.
- Ensure aggregates enforce invariants through constructor and method logic; avoid mutable state outside the aggregate root.
- Keep persistence concerns out of domain classes; use repository interfaces for persistence operations.
- Emit domain events for important state changes and handle them via dedicated handlers in Application or Infrastructure layers.

## Application Layer

- Implement use cases through services or command/query handlers; these orchestrate domain logic and repository calls.
- Use DTOs for communication between layers; map to/from entities via factories or mapping helpers.
- Keep application services free of framework details. Accept interfaces (repositories, message buses) as constructor parameters.
- Validate inputs before invoking domain operations; prefer shared validation helpers or FluentValidation rules.

## API Backend and Infrastructure

- Controllers or minimal endpoints should translate HTTP requests into Application use-case calls and map results back to API DTOs.
- Register repository implementations, message buses, and other infrastructure services during startup using dependency injection.
- Apply policies like authentication, authorization, and exception handling in middleware so use cases remain clean.
- Use Swagger/OpenAPI to document surface area; keep controllers thin by delegating to the Application layer.

## Blazor Web UI

- Treat Blazor components as presentation; avoid embedding business logic and instead call Application services via injected APIs.
- Keep component state minimal and use shared services for cross-component state management (scoped services, Fluxor, etc.).
- Use DTOs or view models to bind data to the UI; map them courtesy of dedicated mappers.
- Handle navigation, validation, and UI-side caching here while keeping persistence logic in the Repository layer.

## Repository & Persistence

- Define repository interfaces in the Domain or Application layer so other projects depend only on abstractions.
- Implement persistence (EF Core, Dapper, etc.) in the Infrastructure project; expose high-level methods like `SaveChangesAsync` and `FindByIdAsync`.
- Keep ORM-specific attributes/configuration in the persistence project, not inside domain entities, unless using clean persistence mapping helpers.
- Unit test application services and domain rules using in-memory repositories or mocks of the repository interfaces.

## Cross-Cutting Guidelines

- Favor constructor injection over service locators or static access.
- Keep configuration values read from strongly typed options and avoid `ConfigurationManager` calls spread across layers.
- Use logging abstractions (`ILogger<T>`) in each layer but log across boundaries consistently.
- Document each projects responsibilities in README sections or workspace notes so new contributors understand the separation.
