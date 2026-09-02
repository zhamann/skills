---
name: principle-oriented-code-engineering
description: Write and refactor code as a clear, minimal expression of intended behavior, favoring coherent design principles over accumulated patches and speculative abstractions.
---

# Principle-oriented code engineering

## Objective

Treat code as an executable specification of intended behavior.

The goal is to implement the required behavior with the least unnecessary structural complexity.

Code should express what the system does now, not preserve the history of bugs, workarounds, and changing assumptions that produced it.

Use this skill when writing new code, fixing defects, reviewing an implementation, or refactoring existing code.

## Behavior preservation principle

Implementation complexity should increase only when required behavior or constraints increase.

When multiple implementations satisfy the same requirements, prefer the one that:

- makes invariants and control flow easier to understand,
- uses fewer independent concepts,
- fits the existing architecture and conventions,
- avoids duplicated logic and unnecessary state,
- is easier to verify and change.

Prefer, in order:

1. Correcting the existing abstraction or responsibility.
2. Consolidating related logic around a shared invariant.
3. Replacing special cases with a rule that represents the domain.
4. Adding a new abstraction or branch only for a genuinely distinct behavior.

A codebase should become more coherent over time, not accumulate the shape of every defect it has encountered.

## Core rule

Bugs, examples, and requested changes are evidence about the design.

They are not automatically reasons to add another conditional, helper, layer, or configuration option.

Convert:

```text
observed failure -> intended invariant -> appropriate implementation
```

not:

```text
observed failure -> permanent special case
```

Determine the intended behavior before choosing the implementation. A failing example shows what happened, but may not reveal the correct boundary, ownership, or general rule.

## Engineering process

### Establish the contract

Identify the behavior being added or preserved, including relevant inputs, outputs, side effects, failure behavior, compatibility requirements, and performance or security constraints.

For a refactor, distinguish observable behavior that must remain stable from implementation details that may change.

### Understand the current design

Inspect the code paths, data flow, tests, and repository conventions that govern the change. Identify where the relevant responsibility and invariant currently live before introducing a new place for them.

### Find accidental complexity

Look for duplicated decisions, scattered invariants, historical branches, unnecessary indirection, premature generalization, conflicting sources of truth, and state that can be derived instead of stored.

### Implement the governing rule

Put the behavior at the boundary or abstraction that owns it. Prefer a direct implementation when the problem is local. Introduce an abstraction when it represents a stable concept with more than one meaningful use, not merely to shorten a function or anticipate unknown requirements.

Keep the change scoped to the requested behavior. Do not use a nearby fix as permission for unrelated cleanup or architectural expansion.

### Simplify the result

Remove code made redundant by the change. Keep naming, data flow, and responsibilities aligned so the implementation can be understood without reconstructing its edit history.

### Validate behavior

Use the strongest practical evidence available: focused tests, type checking, linting, builds, or direct execution. Test the governing invariant and meaningful boundaries, not only the reported example or the internal structure of the implementation.

Confirm that the change:

- satisfies the intended behavior,
- preserves unrelated behavior and public contracts,
- handles the broader class represented by the example,
- introduces no unnecessary abstraction or restriction,
- leaves the design at least as coherent as before.

## Example: generalizing a defect

Intended behavior:

> Archived accounts cannot start new jobs.

Observed failure:

> One archived account started a job through the retry endpoint.

Poor revision:

```ts
if (account.id === "reported-account") {
  throw new Error("Account cannot start jobs");
}
```

This encodes the reported instance rather than the domain rule.

Better revision:

Enforce the account eligibility invariant at the shared job-start boundary used by both initial and retried requests.

This represents the intended behavior once and covers every route that starts a job.

## Design heuristics

### Prefer domain rules over case lists

Represent the category that determines behavior instead of enumerating known instances. Use explicit cases when the domain genuinely defines separate behaviors.

### Prefer one source of truth

Keep each invariant or piece of state owned in one place. Derive secondary values where practical instead of synchronizing copies.

### Prefer local clarity over speculative reuse

Small duplication can be cheaper than the wrong abstraction. Generalize when the shared concept is understood and stable enough to name.

### Prefer rewriting over layering

When a responsibility has changed, update its existing expression where possible. Avoid wrappers, compatibility branches, or parallel paths that preserve an obsolete design without a real compatibility requirement.

### Prefer semantic compression

Fewer lines are not automatically better. Remove concepts that do not add behavior or clarity. Keep structure that makes constraints, ownership, and failure modes explicit.

## Complexity review

Before adding a branch, abstraction, dependency, configuration option, or state field, ask:

1. What distinct behavior or constraint requires it?
2. Is the same rule already represented elsewhere?
3. Can the existing responsibility express it more clearly?
4. Does this model the domain or memorialize one example?
5. Would removing it change required behavior?

Reject complexity that does not add required behavior, preserve a real contract, or make an important invariant clearer.

## Stability test

A well-maintained implementation should converge.

Repeated work in the same area should reinforce a coherent model. If each defect adds another exception, flag, wrapper, or source of truth, revisit the governing abstraction instead of extending the patchwork.

## Success criteria

Successful code:

- implements the intended behavior,
- preserves required contracts,
- generalizes beyond the reported example,
- keeps invariants and ownership clear,
- fits the surrounding system,
- avoids speculative or redundant complexity,
- is verified by evidence proportional to the change.

## Guiding maxim

> Code is not a record of past mistakes. It is an executable specification of current behavior.
