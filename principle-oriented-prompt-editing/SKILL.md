---
name: principle-oriented-prompt-editing
description: Maintain prompts as clear, minimal specifications by expressing general principles instead of accumulating unnecessary complexity.
---

# Principle-Oriented Prompt Editing

## Objective

Treat prompts as specifications of intended behavior.

The goal is to maximize behavioral accuracy while minimizing unnecessary semantic complexity.

A prompt should describe what behavior is desired, not preserve the history of how it was created.

This skill can be used when:

- creating a new prompt,
- reviewing an existing prompt,
- incorporating feedback,
- refactoring instructions,
- debugging unexpected behavior,
- improving maintainability.

---

# Specification Preservation Principle

The semantic complexity of a prompt should increase only when the intended behavior increases.

When multiple prompts can produce equivalent behavior, prefer the one that:

- expresses broader principles,
- contains fewer independent concepts,
- avoids redundant constraints,
- is easier to understand and maintain.

Prefer, in order:

1. Rewriting an existing rule.
2. Merging related rules.
3. Replacing specific cases with broader principles.
4. Adding a new rule only when it expresses a genuinely new requirement.

A prompt should become clearer over time, not longer because it accumulates historical fixes.

---

# Core Rule

Examples and failures are evidence.

They are not automatically requirements.

Convert:

```
specific observation → general principle
```

not:

```
specific observation → permanent exception
```

However, do not infer the desired rule from a failure example alone.

A reported failure describes what happened, not necessarily what should have happened.

Determine the intended behavior first, then generalize the correction.

---

# Review Process

When creating or reviewing a prompt:

## 1. Identify the intended behavior

Determine what the prompt is supposed to accomplish.

Do not assume the desired rule from an observed failure alone.

---

## 2. Identify unnecessary complexity

Look for:

- overly specific constraints,
- redundant instructions,
- enumerated examples that should be categories,
- historical patches,
- conflicting rules.

---

## 3. Generalize where appropriate

Replace narrow cases with the broader principle that accurately represents the intent.

---

## 4. Simplify

Remove instructions that are already implied by other instructions.

The prompt should contain only meaningful, non-redundant concepts.

---

## 5. Validate

Confirm that the revised prompt:

- preserves the intended behavior,
- generalizes beyond individual examples,
- does not introduce unnecessary restrictions,
- is simpler or more expressive than before.

---

# Example: Generalizing a Failure

User intent:

> Characters should have realistic, naturally occurring hair colors.

Observed failure:

> The model generated a character with neon blue hair.

Poor revision:

> Do not generate neon blue hair.

Why:

This encodes one example of the failure instead of the underlying requirement.

Better revision:

> Use realistic, naturally occurring hair colors.

Why:

This captures the intended behavior and applies to the broader category of similar cases.

---

# Editing Heuristics

## Prefer principles over examples

Examples should teach reasoning patterns, not become permanent constraints.

Good:

> Communicate respectfully and professionally.

Bad:

> Never use the phrase "that's stupid."

---

## Prefer abstraction over enumeration

Good:

> Use natural colors.

Bad:

> Do not use blue, green, purple, pink, or orange.

---

## Prefer rewriting over appending

Good:

Rewrite:

> Responses should be concise.

into:

> Responses should be concise and direct.

Bad:

Add another instruction that repeats the same idea.

---

## Prefer semantic compression

Shorter is not always better.

Remove text that is redundant.

Keep text that improves the model's ability to correctly interpret and apply the specification.

The goal is not minimum word count. The goal is minimum unnecessary complexity.

---

# Complexity Review

Before adding or modifying any instruction, ask:

1. Does this introduce a genuinely new concept?
2. Is the behavior already implied elsewhere?
3. Could this be expressed more generally?
4. Is this a principle or merely a historical example?
5. Would removing this instruction change expected behavior?

If a change increases complexity without increasing expressive power, reject it.

---

# Stability Test

A well-maintained prompt should converge.

After applying this process, applying it again should produce little or no meaningful change.

If repeated reviews continue adding exceptions, the prompt is likely accumulating history rather than expressing principles.

---

# Success Criteria

A successful revision:

- captures intended behavior,
- generalizes beyond individual examples,
- removes redundancy,
- avoids unnecessary restrictions,
- maintains internal consistency,
- preserves or reduces complexity whenever possible,
- adds complexity only when required.

---

# Guiding Maxim

> A prompt is not a record of past mistakes. It is a concise specification of intended behavior.
