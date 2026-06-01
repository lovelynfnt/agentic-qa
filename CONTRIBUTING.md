# Contributing to Agentic QA

Thank you for contributing to the Agentic QA project! This guide will help you understand how to add or modify metrics.

## 🚨 Important: Do Not Overwrite Existing Metrics

**Before modifying an existing metric, check with the metric owner first.**

Each metric has an owner who is responsible for its accuracy and consistency. Do not rewrite or significantly change a metric without approval, even if Claude suggests improvements.

### Current Metric Owners

| Metric | File | Owner | Status |
|--------|------|-------|--------|
| Dependency Health | `dependency-health.md` | @lovelynfnt | ✅ Complete |
| Test Performance | `test-performance.md` | Unassigned | 🚧 Not Started |
| Code Quality | `code-quality.md` | Unassigned | 🚧 Not Started |

## ✅ How to Contribute

### Adding a New Metric

1. **Claim ownership** - Comment on the JIRA ticket to claim the metric
2. **Create branch** - `git checkout -b metric/test-performance`
3. **Create files:**
   - `metrics/your-metric.md` - Evaluation instructions
   - `metrics/your-metric-scoring.json` - Scoring configuration
4. **Follow template** - Use `dependency-health.md` as reference
5. **Test thoroughly** - Test on 3+ real projects
6. **Document** - Add examples and edge cases
7. **Create PR** - Request review from 1+ team member
8. **Update this file** - Add yourself as owner

### Modifying an Existing Metric

1. **Check with owner** - Ask metric owner if change is needed
2. **Create JIRA ticket** - Document why change is needed
3. **Get approval** - Owner must approve before implementation
4. **Create branch** - `git checkout -b fix/dependency-health-scoring`
5. **Make minimal changes** - Only change what's necessary
6. **Test impact** - Re-run on previous test projects, compare scores
7. **Document changes** - Update CHANGELOG.md
8. **Create PR** - Owner must review and approve

### Bug Fixes

Bug fixes are always welcome! For bugs in existing metrics:
1. **Report issue** - Create GitHub issue or JIRA ticket
2. **Create branch** - `git checkout -b bugfix/dependency-health-parse-error`
3. **Fix bug** - Make minimal change to fix the issue
4. **Add test case** - Add example that would have caught the bug
5. **Create PR** - Owner will review

## 📋 Metric Development Checklist

When creating a new metric, ensure:

### Phase 1: Definition
- [ ] Metric purpose is clear
- [ ] Pre-check criteria defined for all frameworks
- [ ] Scoring algorithm defined with examples
- [ ] Edge cases documented
- [ ] JIRA ticket created with acceptance criteria

### Phase 2: Implementation
- [ ] Metric markdown file created following template
- [ ] Scoring JSON file created with all thresholds
- [ ] Pre-check validation implemented
- [ ] Evaluation logic implemented for primary framework
- [ ] Error handling for missing tools
- [ ] Report generation (markdown + JSON)

### Phase 3: Testing
- [ ] Tested on 3+ real projects
- [ ] Tested with different tech stacks
- [ ] Consistency test (3 runs produce same score ±2 pts)
- [ ] Error cases tested (missing CLI, no dependencies)
- [ ] Edge cases tested (empty project, huge project)

### Phase 4: Documentation
- [ ] README.md updated
- [ ] Examples included in metric file
- [ ] Scoring rationale documented
- [ ] Added to CONTRIBUTING.md as metric owner

### Phase 5: Review
- [ ] Code review by 1+ team member
- [ ] Demo to team
- [ ] Scoring approved by team
- [ ] Merged to main

## 🎯 Quality Standards

### Consistency
- Running the same metric 3 times on the same project should produce the same score (±2 points tolerance)
- Different team members running the metric should get the same results

### Accuracy
- Findings must match actual state (no false positives)
- Recommendations must be actionable (specific commands)
- Scores must reflect actual project health

### Reliability
- Must handle errors gracefully
- Must work on projects of different sizes
- Must work on all advertised frameworks

### Maintainability
- Code/instructions should be clear
- Scoring thresholds should be in config file (not hardcoded)
- Edge cases should be documented

## 🚫 What NOT to Do

### ❌ Don't Redo Existing Work
Just because you're using Claude doesn't mean you should rewrite a completed metric from scratch. Check CONTRIBUTING.md first.

### ❌ Don't Change Scoring Without Approval
Changing scoring thresholds affects comparisons over time. Get team approval first.

### ❌ Don't Commit Generated Reports
The `.claude/evaluations/` directory is gitignored. Don't commit generated reports.

### ❌ Don't Hardcode Project Paths
Metrics should work on any project path. Don't hardcode paths like `C:\Users\YourName\...`

### ❌ Don't Skip Testing
Always test on real projects before submitting PR.

## 📝 Commit Message Format

Use conventional commits:

```
feat(dependency-health): add Java support
fix(dependency-health): handle empty package.json
docs(readme): update installation instructions
test(dependency-health): add test for Python projects
refactor(scoring): extract calculation to function
```

## 🔄 Review Process

1. **Self-review** - Check your own PR for issues
2. **Automated checks** - (Future: linting, tests)
3. **Peer review** - At least 1 team member approves
4. **Owner review** - Metric owner approves (if modifying existing metric)
5. **Merge** - Squash and merge to main

## 📞 Questions?

- **JIRA:** Comment on the ticket
- **Slack:** #agentic-qa channel
- **GitHub:** Create a discussion

## 🎉 Recognition

Contributors will be recognized in:
- Metric owner table (this file)
- CHANGELOG.md
- README.md acknowledgments

Thank you for helping make test automation better! 🚀
