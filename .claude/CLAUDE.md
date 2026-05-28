# Agentic QA Workspace

This workspace provides AI-powered tools for evaluating test automation projects.

## Available Tools

### Test Automation Evaluator

When the user requests to **evaluate** a test automation project (using phrases like "evaluate this project", "evaluate the project at", "run evaluation on"), activate the evaluator:

1. Read `.claude/personas/evaluator/evaluator.md`
2. Follow the instructions in that file exactly
3. The evaluator will handle the complete evaluation flow

## Recognition Patterns

Activate the evaluator when user says:
- "Evaluate this project [path]"
- "Evaluate [path]"
- "Run evaluation on [path]"
- "Analyze [path]"
- "Assess quality of [path]"

## Note

The evaluator persona will identify itself with a banner when activated, so the user knows they're using the structured evaluation tool and not generic Claude responses.
