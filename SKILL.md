---
name: pino-habit
description: Apply Pino's FuecnMedEcoSystem ABP/.NET development habits when Codex edits, generates, reviews, or explains C# code. Use for ABP application services, domain services, DTOs, validators, object mapping, controllers, ResponseOutput contracts, paging, dynamic proxy/gateway diagnostics, constructors, method signatures, async flows, and code-review tasks where project style and low-disruption changes matter.
---

# Pino Habit

## Core Rule

Keep changes aligned with the existing FuecnMedEcoSystem ABP style. Prefer the smallest code change that satisfies the request, verify the current module before applying remembered patterns, reuse existing project conventions first, and do not introduce new abstractions unless they clearly remove real complexity.

## Formatting Habits

- Keep method parameters on one line when the line remains readable, especially service and controller method signatures.
- Keep class constructors on one line when the line remains readable, even with several injected dependencies.
- Preserve local file formatting when it conflicts with general preferences.
- Avoid broad formatting churn in unrelated code.

## Service Boundary Habits

- Prefer existing module base classes such as `ApplicationServiceNotApi`, `ApplicationServiceWithRedisNotApi`, or module-specific application service bases.
- Use `ApplicationServiceNotApi` for application services that should not be exposed through ABP conventional API metadata.
- Keep hand-written HTTP controllers thin: bind route/body/query attributes, delegate to the app service, and return the service result.
- Do not mix hand-written controller routes and ABP conventional routes for the same contract without checking `api/abp/api-definition`.

## Response Contract Habits

- Return the project wrapper types: `ResponseOutput`, `ResponseOutput<T>`, and ABP DTOs such as `PagedResultDto<T>`.
- Prefer `ResponseOutput.Ok(...)` and `ResponseOutput.Fail(...)` factory methods over constructing response wrappers directly.
- Be careful with implicit conversions and overloads around primitive values such as `int` or `decimal`; verify the returned payload shape.
- Preserve existing domain error codes and Chinese user-facing messages when the surrounding module uses them.

## Validation Habits

- Prefer `AbstractValidator<T>` for request/DTO validation.
- Avoid piling manual parameter checks into application service methods when a validator is the natural fit.
- Keep simple guard clauses only when they protect internal invariants or match nearby code.
- Put validators beside input DTOs under the module's existing `Inputs/Validators` pattern when that pattern exists.
- Reuse existing validator base classes, localization, constants, maximum lengths, and rule style before inventing new helpers.

## Mapping Habits

- Prefer ABP `ObjectMapper` for DTO/entity/view-model conversion.
- Avoid hand-written property-by-property mapping unless the mapping has conditional logic, computed values, snapshots, aggregation, nested sorting, or the project already uses manual mapping in that area.
- Check existing AutoMapper/ObjectMapper profiles before adding new conversion code.
- Keep local `MapOutput` helpers acceptable for admin pages, aggregation outputs, order/address snapshots, and other non-trivial projections already using that style.

## Query And Paging Habits

- Build queries with repository queryables, `WhereIf`, `Include` when needed, deterministic `OrderBy/ThenBy`, `Skip`, and `Take`.
- Use `AsyncExecuter.CountAsync` and `AsyncExecuter.ToListAsync` for IQueryable execution in application services.
- Return `PagedResultDto<T>` for ABP-style paged responses unless the contract already uses a custom offset/page output.
- Trim and normalize user input near the query boundary when existing code does so.

## CancellationToken Habits

- Do not use `CancellationToken.None`.
- Pass through an available `CancellationToken` from the current method when one exists.
- If no token is available, follow the existing project convention rather than adding noisy parameters everywhere.
- Do not change public method signatures just to thread a token unless the requested change or surrounding pattern justifies it.

## ABP Implementation Habits

- Prefer existing repository, manager, domain service, app service, unit-of-work, authorization, localization, and exception patterns.
- Keep business validation in domain/application validation layers rather than scattering checks across controllers or endpoints.
- Prefer ABP-friendly exceptions and result contracts already used by the module.
- For async code, preserve existing `ConfigureAwait` and await style.
- For injectable helper services, follow existing ABP dependency patterns such as `ITransientDependency` or module registration before adding broad manual registration.

## Gateway And Dynamic Proxy Habits

- For cross-service `Could not find remote action` or gateway 404 issues, start from the failing call chain and current `api/abp/api-definition` output.
- Remember ABP conventional routing may drop the `Async` suffix in generated action URLs; verify live routes before changing controllers.
- Distinguish gateway API-definition aggregation/cache issues from caller-side ABP dynamic client metadata caching.
- Prefer caller restart/metadata refresh or the narrow registration fix when evidence points there; do not rewrite service contracts first.

## Review Checklist

When reviewing or finishing ABP/.NET changes, check:

- Method and constructor signatures did not get unnecessarily wrapped.
- No new `CancellationToken.None` was introduced.
- DTO/request validation is in `AbstractValidator<T>` when appropriate.
- Object conversion uses `ObjectMapper` when appropriate.
- Manual `MapOutput` exists only for justified projections or existing local style.
- Responses use `ResponseOutput.Ok/Fail` factories and preserve expected payload shape.
- Paging uses the module's existing `PagedResultDto` or custom offset contract consistently.
- Controllers remain thin and route metadata still matches the exposed contract.
- The change is scoped to the requested behavior and does not reformat unrelated code.
