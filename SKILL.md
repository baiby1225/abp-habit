---
name: pino-habit
description: Apply Pino's ABP/.NET development habits when Codex edits, generates, reviews, or explains C# code in ABP-based solutions. Use for ABP application services, domain services, DTOs, validators, object mapping, constructors, method signatures, async flows, and code-review tasks where project style and low-disruption changes matter.
---

# Pino Habit

## Core Rule

Keep changes aligned with the existing ABP project style. Prefer the smallest code change that satisfies the request, reuse existing project patterns first, and do not introduce new abstractions unless they clearly remove real complexity.

## Formatting Habits

- Keep method parameters on one line when the line remains readable.
- Keep class constructors on one line when the line remains readable.
- Preserve local file formatting when it conflicts with general preferences.
- Avoid broad formatting churn in unrelated code.

## Validation Habits

- Prefer `AbstractValidator<T>` for request/DTO validation.
- Avoid piling manual parameter checks into application service methods when a validator is the natural fit.
- Keep simple guard clauses only when they protect internal invariants or match nearby code.
- Reuse existing validator base classes, localization, constants, and rule style before inventing new helpers.

## Mapping Habits

- Prefer ABP `ObjectMapper` for DTO/entity/view-model conversion.
- Avoid hand-written property-by-property mapping unless the mapping has conditional logic, computed values, or the project already uses manual mapping in that area.
- Check existing AutoMapper/ObjectMapper profiles before adding new conversion code.

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

## Review Checklist

When reviewing or finishing ABP/.NET changes, check:

- Method and constructor signatures did not get unnecessarily wrapped.
- No new `CancellationToken.None` was introduced.
- DTO/request validation is in `AbstractValidator<T>` when appropriate.
- Object conversion uses `ObjectMapper` when appropriate.
- The change is scoped to the requested behavior and does not reformat unrelated code.
