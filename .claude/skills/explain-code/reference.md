# Explain Code Reference

## Explanation Structure

Every explanation should include these sections:

### Overview
One-sentence summary of what the code does and why it exists.

### Flow
Step-by-step breakdown of the logic. Number each step and explain what happens and why.

### Why This Pattern
Explain the design decisions behind the code. Why was this approach chosen over alternatives?

### Dependencies
What other parts of the codebase this code relies on, and what relies on it.

### Gotchas (optional)
Any non-obvious behavior, edge cases, or common mistakes when working with this code.

## Guidelines

- **Adjust depth to audience**: If someone asks "how does auth work?" give a high-level flow. If they ask "why does line 42 use a Map instead of an object?" go deep on that specific choice.
- **Use analogies for complex concepts**: Compare middleware to airport security checkpoints, event emitters to radio broadcasts, etc.
- **Reference the actual code**: Use file:line references so the reader can follow along.
- **Don't over-explain the obvious**: If the code is `const name = user.name`, don't explain variable assignment.
