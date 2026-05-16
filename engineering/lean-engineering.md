# Lean Engineering

## Type
Engineering Practice

## Summary
Build the minimum viable version that solves the core problem, validate it works, then iterate. Avoid building features or infrastructure that don't directly serve the current goal. Every line of code or system component should earn its place.

## Core Principles

### 1. Minimum Viable First
- What is the simplest thing that actually works?
- Don't build for "what if we need it later" — build for what you need now
- Complexity is a liability, not an asset

### 2. Validate Before Scaling
- Get it working in the simplest form first
- Confirm it solves the actual problem (not just the imagined problem)
- Only then add performance, reliability, features

### 3. Delete Before Optimize
- Remove bloat before improving what's left
- A simpler system that's faster is better than an optimized complex system
- Refactoring is fine; over-engineering is not

### 4. Fast Feedback Loop
- Short cycle between idea → implementation → validation
- The slower the feedback, the more you build before knowing if it's right
- Automate testing to keep feedback fast

## Lean vs. Waterfall Thinking

| Waterfall | Lean |
|-----------|------|
| Design everything before building | Build minimum → validate → iterate |
| "We need to get it right the first time" | "We'll learn what right is as we build" |
| Complexity as preparation | Simplicity as starting point |
| Weeks of planning → months of building | Hours of planning → days of validating |

## Common Lean Traps

- **False minimum**: Calling something "minimum" when it's still overbuilt
- **Premature optimization**: Building performance before confirming correctness
- **Feature creep in iteration**: Each sprint adds instead of focusing
- **Validation theater**: Testing but not actually checking if it helps the user

## Related Concepts
- [[elon-musk-5-step]] — Delete (step 2) and simplify (step 3) are the core lean moves
- [[physics-first-principles]] — Don't inherit assumptions about "what we need"
- [[vibe-coder-security-checklist]] — The pre-launch counterpart to lean: building the minimum is great, but not if you skip security and privacy checks on the way out

## Sources
- Internal (conversation with Andreas)