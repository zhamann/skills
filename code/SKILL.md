---
name: code
description: Write, fix, review, and refactor code as a clear, minimal expression of intended behavior. Use for any implementation work where the result should generalize from bugs and examples to the governing rule, preserve required contracts, and avoid both accumulated patches and speculative abstraction.
---

# Code

## Objective

Treat code as an executable specification of current behavior, not a record of the bugs, workarounds, and shifting assumptions that produced it.

Implement what is required with the fewest necessary concepts. Complexity should grow only when required behavior or constraints grow. Fewer lines are not the goal; clear invariants, ownership, control flow, and verifiability are.

## Core principle

Bugs, examples, and change requests are evidence about the design, not automatic reasons to add another branch, helper, layer, or option.

```text
observed failure → intended invariant → implementation at the owning boundary
```

not:

```text
observed failure → permanent special case
```

A failing example shows what happened. It may not reveal the correct rule, its owner, or its boundary, so establish those before choosing a fix.

When several implementations satisfy the requirements, prefer, in order:

1. Correcting the existing responsibility or abstraction.
2. Consolidating related logic around a shared invariant.
3. Replacing special cases with a rule that represents the domain.
4. Adding a branch or abstraction only for genuinely distinct behavior.

### Example

Intended behavior: archived accounts cannot start new jobs. Observed failure: one archived account started a job through the retry endpoint.

A patch keyed to the incident encodes the instance, not the rule:

```ts
if (account.id === "reported-account") {
  throw new Error("Account cannot start jobs");
}
```

Enforcing account eligibility at the shared job-start boundary, used by both initial and retried requests, states the rule once and covers every route that starts a job.

## Workflow

1. **Establish the contract.** Identify inputs, outputs, side effects, failure behavior, compatibility, performance, and security constraints. For a refactor, separate observable behavior that must stay stable from details that may change.
2. **Understand the current design.** Trace the relevant code paths, data flow, tests, and conventions. Find where the responsibility and its invariant already live before creating a new home for them.
3. **Find accidental complexity.** Look for duplicated decisions, scattered invariants, historical branches, needless indirection, premature generalization, conflicting sources of truth, and stored state that could be derived.
4. **Implement the governing rule.** Put behavior at the boundary that owns it. Prefer a direct change when the problem is local. Keep scope to the requested behavior; a nearby fix is not permission for unrelated cleanup or architectural expansion.
5. **Simplify the result.** Remove code the change made redundant, so the implementation can be understood without reconstructing its edit history.
6. **Verify.** Use the strongest practical evidence: focused tests, type checks, linting, builds, or direct execution. Test the invariant and its meaningful boundaries, not only the reported example, and confirm unrelated behavior and public contracts still hold.

## Design rules

- **Domain over incidents:** Model the category that determines behavior instead of listing known cases. Keep explicit cases when the domain genuinely defines separate behaviors.
- **One source of truth:** Own each invariant and piece of state in one place. Derive secondary values rather than synchronizing copies.
- **Local clarity over speculative reuse:** Small duplication can be cheaper than the wrong abstraction. Generalize once the shared concept is stable enough to name.
- **Rewriting over layering:** When a responsibility changes, update its existing expression. Avoid wrappers, compatibility branches, and parallel paths unless a real compatibility requirement exists.
- **Semantic over syntactic compression:** Remove concepts that add neither behavior nor clarity. Keep structure that makes constraints, ownership, and failure modes explicit.
- **Convergence over accumulation:** Repeated work in one area should reinforce a coherent model. If each defect adds another exception, flag, wrapper, or source of truth, revisit the governing abstraction instead of extending the patchwork.

## Complexity gate

Before adding a branch, abstraction, dependency, configuration option, or state field, ask:

1. What distinct behavior or constraint requires it?
2. Is the same rule already represented elsewhere?
3. Could an existing responsibility express it more clearly?
4. Does it model the domain or memorialize one example?
5. Would removing it change required behavior or hide an important invariant?

Reject complexity that does not add required behavior, preserve a real contract, or make an important invariant clearer.

## Success criteria

The change implements the intended behavior, preserves required contracts, handles the broader class the example represents, keeps ownership and invariants clear, fits the surrounding system, and is verified in proportion to its risk. The design is at least as coherent as before.

**Guiding maxim:** Code should express what the system does, not how it got there.
