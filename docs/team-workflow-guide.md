# Team Workflow Guide

## Purpose

This guide explains how to work on Agentic QA as a team without stepping on each other's toes or accidentally overwriting completed work.

---

## 🎯 The Problem We're Solving

**Scenario:** Team member uses Claude and says "implement dependency health metric"

**Risk:** Claude might regenerate the entire metric from scratch, overwriting the carefully tested and validated implementation that's already there.

**Solution:** Clear ownership, JIRA tickets, and contribution guidelines.

---

## 🏗️ Project Structure

### Epic Level: Test Automation Quality Audit Framework
**Goal:** Create reusable audit criteria for automation frameworks

### Story Level: Individual Metrics
Each metric is one JIRA story with clear acceptance criteria

**Current Metrics:**
- ✅ **Dependency Health** (Complete, Owner: @lovelynfnt)
- 🚧 **Test Performance** (Not Started)
- 🚧 **Code Quality** (Not Started)

---

## 📋 Workflow for New Metrics

### 1. Claim Ownership
- Comment on JIRA ticket: "I'll take this"
- Update CONTRIBUTING.md to add yourself as owner
- Create branch: `git checkout -b metric/test-performance`

### 2. Use the Template
- Copy `.github/ISSUE_TEMPLATE/metric-story.md`
- Fill in specific details for your metric
- Create JIRA ticket with all sections

### 3. Develop the Metric
Follow the checklist:
- [ ] Phase 1: Definition (purpose, pre-checks, scoring)
- [ ] Phase 2: Implementation (md file, scoring json, logic)
- [ ] Phase 3: Testing (3+ projects, consistency, edge cases)
- [ ] Phase 4: Documentation (README, examples, comments)
- [ ] Phase 5: Review (code review, demo, team approval)

### 4. Create PR
- Title: `feat(metric): implement test-performance metric`
- Link to JIRA ticket
- Request review from 1+ team member
- Update CONTRIBUTING.md with your ownership

### 5. Demo to Team
- Show live evaluation
- Get feedback on scoring
- Get approval to merge

---

## 🚫 Workflow for Existing Metrics

### ⚠️ IMPORTANT: Check Before Modifying

**Before touching an existing metric file:**

1. **Check CONTRIBUTING.md** - Who owns this metric?
2. **Check with owner** - Do they approve this change?
3. **Create JIRA ticket** - Document why change is needed
4. **Get approval** - Don't proceed without owner sign-off

### ✅ Bug Fixes Are Always Welcome

If you find a bug (parsing error, wrong calculation, etc.):
1. Create GitHub issue or JIRA ticket
2. Create branch: `git checkout -b bugfix/dependency-health-npm-parse`
3. Fix the bug (minimal change)
4. Add test case that would have caught it
5. Create PR, owner reviews

### Example: Modifying Dependency Health

**❌ WRONG:**
```
Developer: "Claude, implement dependency health metric"
Claude: *regenerates entire metric from scratch*
Result: Overwrites tested, validated implementation
```

**✅ RIGHT:**
```
Developer: *Reads CONTRIBUTING.md, sees @lovelynfnt owns it*
Developer: *Messages @lovelynfnt: "I think npm parsing has a bug"*
Owner: "Good catch, go ahead and fix it"
Developer: *Creates bugfix branch*
Developer: "Claude, fix the npm parsing in dependency-health.md line 245"
Claude: *Makes targeted fix*
Developer: *Creates PR, owner reviews*
```

---

## 📁 File Ownership Reference

| File | Owner | Status | Can Modify? |
|------|-------|--------|-------------|
| `CLAUDE.md` | @lovelynfnt | ✅ Stable | ⚠️ Ask first |
| `evaluator.md` | @lovelynfnt | ✅ Stable | ⚠️ Ask first |
| `dependency-health.md` | @lovelynfnt | ✅ Complete | ⚠️ Ask first |
| `dependency-health-scoring.json` | @lovelynfnt | ✅ Complete | ⚠️ Ask first |
| `test-performance.md` | Unassigned | 🚧 Not Started | ✅ Claim it! |
| `test-performance-scoring.json` | Unassigned | 🚧 Not Started | ✅ Claim it! |
| `code-quality.md` | Unassigned | 🚧 Not Started | ✅ Claim it! |
| `code-quality-scoring.json` | Unassigned | 🚧 Not Started | ✅ Claim it! |
| `README.md` | @lovelynfnt | ✅ Stable | ✅ PRs welcome |
| `CONTRIBUTING.md` | @lovelynfnt | ✅ Stable | ✅ PRs welcome |

---

## 🎯 JIRA Ticket Template

### Where to Find It
- **Template:** `.github/ISSUE_TEMPLATE/metric-story.md`
- **Example (Dependency Health):** `docs/jira-ticket-dependency-health.md`

### Key Sections

1. **Description** - Purpose, why it matters, scope
2. **Pre-Check Criteria** - What must exist before evaluation runs
3. **Acceptance Criteria** - Clear checkboxes for done/not done
   - Claude evaluator implementation
   - Scoring validation
   - Real-world testing
   - Documentation
4. **Definition of Done** - Final checklist before closing ticket
5. **Sub-Tasks** - Breakdown for tracking progress
6. **Testing Plan** - Consistency testing, framework testing, edge cases
7. **Risk & Dependencies** - What could go wrong

### How to Use

**Copy the template:**
```bash
# For new metric
cp .github/ISSUE_TEMPLATE/metric-story.md temp.md

# Or use the Dependency Health example as reference
cat docs/jira-ticket-dependency-health.md
```

**Fill in the blanks:**
- Replace `[METRIC_NAME]` with your metric name
- Fill in specific pre-check criteria
- Define scoring algorithm
- List test projects
- Estimate story points

**Create JIRA ticket:**
- Copy content to JIRA
- Link to epic
- Assign to yourself
- Set status to "In Progress"

---

## 🧪 Testing Requirements

Every metric MUST be tested on 3+ real projects before merging.

### Consistency Testing
Run metric 3 times on same project, verify:
- Same score (±2 points tolerance)
- Same findings
- Same recommendations

### Framework Testing
Test on at least one project per supported framework:
- .NET
- Node.js
- Python
- Java

### Edge Case Testing
Test scenarios:
- Empty project (no dependencies)
- Large project (100+ dependencies)
- Perfect project (no issues, score 100)
- Broken project (many issues, score <20)
- Missing CLI tools

---

## 📝 Documentation Requirements

Every metric MUST have:

1. **Metric MD File** (`metrics/[name].md`)
   - Purpose clearly stated
   - Pre-check criteria listed
   - Evaluation steps documented
   - Example output shown
   - Error handling documented

2. **Scoring JSON File** (`metrics/[name]-scoring.json`)
   - All thresholds defined
   - Deduction points specified
   - Grade boundaries defined
   - Inline comments explaining choices

3. **README.md Updated**
   - Metric listed with status (✅ or 🚧)
   - Usage example included
   - Expected output shown

4. **CONTRIBUTING.md Updated**
   - Your name added as owner
   - Metric added to ownership table

---

## 🚀 Using Claude Effectively

### ✅ Good Claude Prompts

**For new metrics:**
```
"I'm implementing the test-performance metric. 
Read .claude/personas/evaluator/metrics/dependency-health.md 
as a reference and help me create 
test-performance.md following the same structure."
```

**For bug fixes:**
```
"There's a bug in dependency-health.md at line 245 
where npm parsing fails for scoped packages. 
Fix the regex to handle @scope/package-name format."
```

**For understanding:**
```
"Explain how the scoring algorithm works in 
dependency-health-scoring.json. Walk me through 
a scenario with 2 major outdated and 1 high vulnerability."
```

### ❌ Bad Claude Prompts

**Too vague:**
```
"Implement dependency health metric"
→ Risk: Regenerates from scratch, overwrites existing work
```

**No context:**
```
"Add Python support"
→ Risk: Where? Which file? What's already done?
```

**Too broad:**
```
"Make the evaluator better"
→ Risk: Changes too many things at once, hard to review
```

---

## 🎓 Onboarding New Team Members

### Day 1: Understanding
1. Read `README.md` - Understand what Agentic QA does
2. Read `CONTRIBUTING.md` - Understand how to contribute
3. Read this file - Understand team workflow
4. Review `docs/jira-ticket-dependency-health.md` - See a complete example

### Day 2: Hands-On
1. Clone repo: `git clone https://github.com/lovelynfnt/agentic-qa.git`
2. Run evaluation on a test project
3. Read through `dependency-health.md` and `dependency-health-scoring.json`
4. Understand the 5-step flow

### Day 3: First Task
1. Pick an unassigned metric (test-performance or code-quality)
2. Copy JIRA template
3. Start Phase 1: Definition
4. Ask questions in team chat

---

## 📞 Communication Channels

**For questions:**
- Slack: `#agentic-qa` channel
- JIRA: Comment on relevant ticket
- GitHub: Create discussion

**For approvals:**
- Metric owner (see CONTRIBUTING.md)
- Team lead for scoring changes
- PR reviewer (1+ required)

**For emergencies:**
- Blocker: Tag team lead in JIRA
- Production issue: Slack `#agentic-qa-urgent`

---

## ✅ Checklist: Before You Start Work

Before writing any code, verify:

- [ ] JIRA ticket exists
- [ ] I'm assigned to the ticket
- [ ] I've read CONTRIBUTING.md
- [ ] I've checked file ownership (CONTRIBUTING.md)
- [ ] I have approval if modifying existing metric
- [ ] I've created a feature branch
- [ ] I understand the acceptance criteria
- [ ] I know which projects to test on

---

## 🎉 Success Criteria

You know the workflow is working when:

✅ No accidental overwrites of completed work  
✅ Clear ownership of each metric  
✅ Consistent quality across metrics  
✅ Easy to track who's working on what  
✅ New team members can onboard quickly  
✅ Changes are reviewable and reversible  
✅ Metrics stay consistent over time  

---

## 🔄 Process Improvements

This workflow is a living document. If you find:
- Unclear steps
- Missing information
- Better ways to work

**Create a PR to improve this guide!**

---

Last Updated: 2026-05-29  
Owner: @lovelynfnt  
Reviewers: [Team to review]
