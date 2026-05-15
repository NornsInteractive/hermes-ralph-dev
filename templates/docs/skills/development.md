
# Development Guidelines

## 1. Think Before Coding

- State assumptions explicitly
- If uncertain, ask instead of guessing
- Present multiple interpretations when ambiguity exists
- Push back if a simpler solution exists
- Stop and ask when confused

## 2. Simplicity First

- Minimum code that solves the problem
- No speculative abstractions
- No unnecessary configurability
- No features beyond what was asked
- If 200 lines can become 50, simplify

## 3. Surgical Changes

- Touch only what is necessary
- Don’t refactor unrelated code
- Don’t “clean up” adjacent files
- Match existing style
- Remove only dead code introduced by your changes

## 4. Goal-Driven Execution

- Define success criteria first
- Prefer tests before implementation
- Verify outcomes instead of assuming correctness
- Iterate until verifiable success

## 5. Reliability Over Cleverness

- Prefer boring and reliable solutions
- Avoid premature optimization
- Do not introduce new dependencies unless necessary
- Explicit is better than implicit

## 6. Read Before Write

- Read existing code before modifying it
- Understand surrounding patterns first
- Reuse existing utilities when appropriate
- Do not duplicate logic already present

## 7. Fail Loudly

- Never silently swallow errors
- Surface uncertainty explicitly
- If verification is impossible, say so
- Do not pretend code was tested if it was not

## Behavioral Expectations

Before implementing:

1. Briefly explain the plan
2. Identify assumptions
3. Mention tradeoffs
4. Prefer modifying existing code over creating abstractions
5. Keep changes minimal
6. Verify results after changes
7. Ask before destructive operations

## Anti-Patterns

Avoid:

- Overengineering
- Architecture astronautics
- Premature abstractions
- Large unrelated refactors
- Inventing new patterns without need
- Hidden side effects
- Silent fallbacks
- Fake confidence