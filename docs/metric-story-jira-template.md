---
name: Metric Story Template
about: Template for creating a new metric implementation story
title: 'Define & Implement [METRIC_NAME] Metric'
labels: metric, story
assignees: ''
---

## Story: Define & Implement [METRIC_NAME] Metric

**Epic:** Test Automation Quality Audit Framework  
**Priority:** [High/Medium/Low]  
**Story Points:** [To be estimated]  
**Owner:** @[github-username]

---

### Description

**Purpose:**  
[Describe what this metric evaluates and why it matters]

**Why This Matters:**
- [Business value point 1]
- [Business value point 2]
- [Business value point 3]

**Scope:**
- Define pre-check criteria
- Define scoring algorithm and thresholds
- Implement evaluation logic for supported frameworks
- Test on 3+ real projects with different tech stacks
- Document metric completely

---

### Pre-Check Criteria

Before evaluation can run, verify:

**For .NET:**
- [ ] [Specific requirement 1]
- [ ] [Specific requirement 2]

**For Node.js:**
- [ ] [Specific requirement 1]
- [ ] [Specific requirement 2]

**For Python:**
- [ ] [Specific requirement 1]
- [ ] [Specific requirement 2]

**For Java:**
- [ ] [Specific requirement 1]
- [ ] [Specific requirement 2]

---

### Acceptance Criteria

#### Claude Evaluator Implementation
- [ ] **Pre-check validation implemented** for all supported frameworks
- [ ] **Scoring algorithm is accurate** - deducts correct points based on `[metric-name]-scoring.json`
- [ ] **Evaluation is consistent** - running 3x on same project produces same score (±2 points tolerance)
- [ ] **Handles errors gracefully** - if CLI tool/data missing, shows helpful error message
- [ ] **Generates both outputs** - markdown report AND JSON history file
- [ ] **Reports are accurate** - all findings match actual tool output

#### Scoring Validation
- [ ] **Scoring thresholds are reasonable** (document proposed values and get team approval)
- [ ] **Grade boundaries make sense** (test with mock data at different score levels)
- [ ] **Validated with team** that scoring feels fair

#### Real-World Testing
- [ ] **Tested on [Project 1]** - [Framework/Tech Stack]
  - Evaluation completes successfully
  - Score is reasonable given actual state
  - Recommendations are actionable
  
- [ ] **Tested on [Project 2]** - [Framework/Tech Stack]
  - Evaluation completes successfully
  - Framework detection works correctly
  
- [ ] **Tested on [Project 3]** - [Framework/Tech Stack]
  - Commands work correctly
  - Edge cases handled properly

#### Documentation
- [ ] **Metric is fully documented** in `[metric-name].md`
- [ ] **Scoring rules documented** in `[metric-name]-scoring.json`
- [ ] **README.md updated** to reflect implementation status
- [ ] **Examples included** showing expected output

---

### Definition of Done

- [ ] All acceptance criteria met
- [ ] Code reviewed by at least 1 team member
- [ ] Tested on 3+ real projects across different tech stacks
- [ ] Documentation complete and reviewed
- [ ] Added to CONTRIBUTING.md as metric owner
- [ ] Merged to main branch
- [ ] Demo completed with team

---

### Sub-Tasks

Create sub-tasks as needed:

1. **Define pre-check criteria** ([X] pts) - @assignee
   - Document requirements
   - Define error messages

2. **Implement [Framework] evaluation** ([X] pts) - @assignee
   - CLI commands
   - Parsing logic
   - Test on real project

3. **Validate scoring algorithm** ([X] pts) - @assignee
   - Run on multiple projects
   - Verify consistency
   - Team review

4. **Create test cases** ([X] pts) - @assignee
   - Mock projects
   - Edge cases
   - Document in `/tests/`

5. **Documentation** ([X] pts) - @assignee
   - Update files
   - Add examples
   - Team review

---

### Testing Plan

**Consistency Testing:**
- Run metric 3 times on same project
- Expected: Same score (±2 points)
- Projects to test: [list]

**Framework Testing:**
- Test on .NET project: [project name]
- Test on Node.js project: [project name]
- Test on Python project: [project name]
- Test on Java project: [project name]

**Edge Cases:**
- Empty project (no dependencies)
- Very large project (100+ dependencies)
- Project with missing CLI tools
- Project with no issues (perfect score)
- Project with many issues (low score)

---

### Risk & Dependencies

**Risks:**
- [What could go wrong?]
- [Technical challenges?]

**Dependencies:**
- [Blocked by other tickets?]
- [Requires specific tools?]

---

### Notes

[Any additional context, research, or decisions made during planning]
