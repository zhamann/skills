---
name: prompt
description: Create, review, debug, and refactor prompts, system instructions, and skills as clear, minimal specifications of intended behavior. Use when writing a prompt, incorporating feedback, or fixing unexpected model behavior, so corrections generalize into principles instead of accumulating as exceptions.
---

# Prompt

## Objective

Treat a prompt as a specification of intended behavior, not a record of past corrections.

Maximize behavioral accuracy with the fewest necessary independent concepts. Semantic complexity should grow only when the intended behavior grows. The goal is not minimum word count; it is minimum unnecessary complexity.

## Core principle

Examples and failures are evidence, not automatic requirements.

```text
observed behavior → intended behavior → general principle
```

not:

```text
observed behavior → permanent exception
```

A failure shows what happened, not necessarily what should have happened. Establish the intended behavior first, then generalize the correction.

When several prompts would produce the same behavior, prefer, in order:

1. Rewriting an existing rule.
2. Merging related rules.
3. Replacing specific cases with a broader principle.
4. Adding a rule only for a genuinely new requirement.

### Example

Intent: characters should have realistic, naturally occurring hair colors. Observed failure: the model gave a character neon blue hair.

"Do not generate neon blue hair" encodes one instance and invites the next one (green, purple, pink). "Use realistic, naturally occurring hair colors" states the requirement and covers the whole category.

## Workflow

1. **Establish intent.** Identify the behavior, scope, constraints, and outcomes the prompt must produce or preserve. Do not infer the rule from a failure alone.
2. **Diagnose complexity.** Find redundant or conflicting instructions, overly narrow constraints, enumerated examples that should be categories, and historical patches.
3. **Generalize.** Replace narrow cases with the principle that accurately represents the intent. Keep explicit distinctions where behavior genuinely differs.
4. **Simplify.** Rewrite the governing instruction instead of appending a correction, merge related rules, and remove instructions already implied elsewhere.
5. **Validate.** Confirm the result preserves intended behavior, generalizes beyond the examples, adds no unintended restrictions, stays internally consistent, and is clearer or more expressive than before. Test against real inputs when possible.

## Editing rules

- **Principles over examples:** Examples should teach a reasoning pattern, not become permanent constraints. "Communicate respectfully" beats "Never say 'that's stupid.'"
- **Abstraction over enumeration:** Name the category when it accurately covers the intended cases. "Use natural colors" beats a list of forbidden ones.
- **Rewriting over appending:** Strengthen the existing instruction ("Be concise" → "Be concise and direct") rather than adding a second one that restates it.
- **Semantic over textual compression:** Remove redundant concepts, not wording the model needs to interpret and apply the instruction correctly.
- **Consistency over isolated fixes:** Resolve conflicting instructions at the level of the specification, not by adding a tie-breaker for each collision.

## Complexity gate

Before adding or changing an instruction, ask:

1. Does it express a genuinely new requirement?
2. Is the behavior already implied elsewhere?
3. Could it be expressed more generally?
4. Is it a principle or merely an observed example?
5. Would removing it change expected behavior?

Reject changes that add complexity without improving behavioral accuracy or expressive power.

## Stability test

A well-maintained prompt converges. Applying this process again should produce little or no meaningful change. If each review adds more exceptions, the prompt is accumulating history instead of expressing principles, and the underlying specification needs rethinking.

**Guiding maxim:** A prompt should describe what the system must do, not the history of mistakes that shaped it.
