# Ask Mode Before Code Mode

For complex tasks, start with Ask mode to create an implementation plan, then switch to Code mode to execute. This two-step pattern prevents expensive wrong turns and keeps the model grounded.

## The Pattern

```
Step 1: Ask mode
  → Prompt for implementation plan
  → Review and refine the plan
  → Identify dependencies and edge cases

Step 2: Code mode
  → Execute based on the approved plan
  → Claude has clear targets to hit
  → Less drift from the intended approach
```

## Why It Works

Ask mode alone: Good for exploring, but no execution
Code mode alone: Good for execution, but prone to drift on large changes

Ask + Code:
- Ask mode gives Claude space to think without the pressure of producing perfect output
- The plan becomes a contract: Claude knows what it's building toward
- Errors in the plan are caught before code is written (cheaper to fix)
- Claude stays grounded because it has a reference to check against

## When to Use It

**Use for:**
- Changes spanning multiple files or packages
- Refactoring that touches dozens of files
- Migration with dependencies across services
- Any task that would take an hour or a few hundred lines to implement

**Skip for:**
- Simple, well-scoped tasks (one file, obvious changes)
- Exploratory work where the goal isn't clear yet
- Quick fixes where the overhead isn't worth it

## Practical Implementation

**Step 1 — Ask mode prompt:**
```
I need to refactor [describe what]. Here's the current structure:
[relevant code snippets]

Please create an implementation plan covering:
1. What files need to change
2. Order of changes (dependencies)
3. Risks or edge cases to watch for
4. How to validate the changes work
```

**Step 2 — Code mode prompt:**
```
Following the plan from our Ask session, implement:
[reference the plan or reiterate key points]

Focus on [specific priorities]. I'll review before we proceed.
```

## Related Pattern: Prompt Like a GitHub Issue

Codex (and similar agents) respond better when prompts mirror how you'd describe a change in a PR or issue. Include:
- File paths and component names
- Diff snippets or current code
- Doc fragments for context
- Reference to similar patterns elsewhere in the codebase

## Relationship to My Work

When I'm tackling a complex task (multi-file changes, unfamiliar codebase, high-stakes changes), I should use this pattern. Start in Ask mode to explore and plan, then switch to execute. This reduces rework and keeps me from going down paths that won't work.

This also applies to skill building — define the skill's structure and approach in Ask mode before writing the full SKILL.md.

## See Also

- [[progressive-disclosure-pattern]] — The three-level system for skill architecture
- [[skill-trigger-design]] — How to make skills trigger reliably