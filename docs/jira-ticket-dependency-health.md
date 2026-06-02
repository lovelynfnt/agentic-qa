# JIRA Ticket: Dependency Health Metric

**Copy this to JIRA to track the Dependency Health metric work**

---

## Story: Define & Implement Dependency Health Metric

**Type:** Story  
**Epic Link:** Test Automation Quality Audit Framework  
**Priority:** High  
**Story Points:** 8  
**Owner:** @lovelynfnt  
**Status:** In Progress

---

### Description

**Purpose:**  
Evaluate how up-to-date and secure a project's dependencies are by checking for outdated packages and known security vulnerabilities across .NET, Node.js, Python, and Java projects.

**Why This Matters:**
- Outdated dependencies introduce security risks and missing features
- Major version gaps make future upgrades increasingly difficult and risky
- Known security vulnerabilities expose projects to potential attacks
- Provides objective scoring (0-100) and specific, actionable remediation commands
- Establishes baseline for tracking dependency health improvements over time

**Scope:**
- Define pre-check criteria for this metric (what must exist before evaluation runs)
- Define scoring algorithm and thresholds (how points are deducted)
- Implement evaluation logic for .NET, Node.js, Python, and Java frameworks
- Test on 3+ real projects with different tech stacks
- Document metric implementation and usage

---

### Pre-Check Criteria

Before evaluation can run, the metric validates:

**For .NET:**
- ✅ At least one `.csproj` file exists with `<PackageReference>` elements
- ✅ `dotnet` CLI is available and returns version successfully

**For Node.js:**
- ✅ `package.json` exists with `dependencies` or `devDependencies` sections
- ✅ `npm` CLI is available and returns version successfully

**For Python:**
- ✅ `requirements.txt` or `pyproject.toml` exists and is not empty
- ✅ `pip` CLI is available and returns version successfully
- ⚠️ `pip-audit` is optional (skips vulnerability check if not installed)

**For Java:**
- ✅ `pom.xml` or `build.gradle` exists with dependency declarations
- ✅ `mvn` or `gradle` CLI is available and returns version successfully

**If pre-check fails:**
- Display clear error message indicating what's missing
- Offer to select different metric or cancel evaluation

---

### Acceptance Criteria

#### ✅ Claude Evaluator Implementation
- [x] **Pre-check validation implemented** for all 4 frameworks (.NET, Node.js, Python, Java)
  - Validates project files exist and have dependency declarations
  - Validates CLI tools are available
  - Shows helpful error if validation fails
  
- [x] **Scoring algorithm is accurate** - deducts correct points based on `dependency-health-scoring.json`
  - Major outdated: -20 points each
  - Minor outdated (>3 threshold): -5 points each
  - Patch outdated (>5 threshold): -2 points each
  - Critical vuln: -25 points each
  - High vuln: -15 points each
  - Moderate vuln: -8 points each
  - Low vuln: -3 points each
  
- [ ] **Evaluation is consistent** - running 3x on same project produces same score (±2 points tolerance)
  - **Action:** Run on Business Central Automation 3 times, verify scores match
  
- [x] **Handles errors gracefully** - if CLI tool missing, shows helpful error message with installation instructions
  
- [x] **Generates both outputs:**
  - Markdown report: `.claude/evaluations/[timestamp]_[project-name]_dependency-health.md`
  - JSON history: `.claude/evaluations/history/[timestamp]_[project-name]_dependency-health.json`
  
- [ ] **Reports are accurate** - all findings match actual CLI output
  - **Action:** Compare report findings to manual `dotnet list package --outdated` run

#### ✅ Scoring Validation
- [ ] **Scoring thresholds are reasonable:**
  - Major outdated: -20 points ⏳ **Needs team approval**
  - Minor outdated (>3): -5 points ⏳ **Needs team approval**
  - Patch outdated (>5): -2 points ⏳ **Needs team approval**
  - Critical vuln: -25 points ⏳ **Needs team approval**
  - High vuln: -15 points ⏳ **Needs team approval**
  - Moderate vuln: -8 points ⏳ **Needs team approval**
  - Low vuln: -3 points ⏳ **Needs team approval**
  
- [ ] **Grade boundaries make sense:**
  - Test scenario: 0 outdated packages = 100 points (Grade A) ✅
  - Test scenario: 3 major outdated = 40 points (Grade F) ✅
  - Test scenario: 5 minor outdated + 2 high vulns = 75 - 25 - 30 = 20 (Grade F) ✅
  - **Action:** Present to team, get feedback on whether these feel fair

#### 🧪 Real-World Testing
- [ ] **Tested on Business Central Automation** - .NET/SpecFlow
  - **Owner:** @lovelynfnt
  - **Expected:** Evaluation completes successfully
  - **Expected:** Score reflects actual dependency state
  - **Expected:** Recommendations are specific dotnet commands
  - **Status:** 🔲 Not started
  
- [ ] **Tested on Project Enable** - .NET/SpecFlow
  - **Owner:** @lovelynfnt
  - **Expected:** Evaluation completes successfully
  - **Expected:** Framework detection works (should detect .NET + SpecFlow)
  - **Status:** 🔲 Not started
  
- [ ] **Tested on Project TDC** - [Confirm tech stack]
  - **Owner:** @[team-member]
  - **Expected:** Framework detected correctly
  - **Expected:** Commands work for that framework
  - **Status:** 🔲 Not started

**Additional Testing (Optional but Recommended):**
- [ ] **Tested on Node.js project** - Jest/Playwright
  - Verify `npm outdated` and `npm audit` work correctly
  
- [ ] **Tested on Python project** - pytest
  - Verify `pip list --outdated` works
  - Verify graceful handling if `pip-audit` not installed

#### 📝 Documentation
- [x] **Metric is fully documented** in `dependency-health.md`
  - Pre-check steps documented
  - Evaluation steps documented
  - Report generation documented
  - Error handling documented
  
- [x] **Scoring rules documented** in `dependency-health-scoring.json`
  - All thresholds defined
  - Deduction points specified
  - Grade boundaries defined
  
- [x] **README.md updated** to reflect current implementation
  - Dependency Health marked as "✅ Available Now"
  - Usage instructions included
  - Sample output shown
  
- [x] **Examples included** in metric file
  - Shows expected commands for each framework
  - Shows expected output format

---

### Definition of Done

- [ ] All acceptance criteria checked (see above)
- [ ] Code reviewed by at least 1 team member
  - **Reviewer:** @[team-member]
  - **Review date:** [TBD]
- [ ] Tested on 3+ real projects across different tech stacks
  - Business Central Automation ✅
  - Project Enable ⏳
  - Project TDC ⏳
- [ ] Documentation complete and reviewed
  - Technical review: ⏳
  - User-facing review: ⏳
- [ ] Added to CONTRIBUTING.md as metric owner ✅
- [ ] Merged to main branch ⏳
- [ ] Demo completed with team
  - **Demo date:** [TBD]
  - **Attendees:** [Team members]

---

### Sub-Tasks

#### 1. Define Pre-Check Criteria (2 pts) ✅ DONE
- [x] Document what must exist before evaluation runs
- [x] Define error messages for missing requirements
- **Owner:** @lovelynfnt
- **Completed:** 2026-05-28

#### 2. Implement .NET Evaluation (3 pts) ✅ DONE
- [x] `dotnet list package` commands
- [x] `dotnet list package --outdated` parsing logic
- [x] `dotnet list package --vulnerable` parsing logic
- [x] Test on Business Central Automation project
- **Owner:** @lovelynfnt
- **Completed:** 2026-05-28

#### 3. Implement Node.js Evaluation (2 pts) ✅ DONE
- [x] `npm outdated --json` command
- [x] `npm audit --json` command
- [x] Parsing logic for npm output
- [ ] Test on real Node.js project
- **Owner:** @lovelynfnt
- **Status:** Implemented, needs testing

#### 4. Implement Python Evaluation (2 pts) ✅ DONE
- [x] `pip list --outdated --format=json` command
- [x] `pip-audit --format=json` command (with graceful fallback)
- [x] Parsing logic
- [ ] Test on real Python project
- **Owner:** @lovelynfnt
- **Status:** Implemented, needs testing

#### 5. Implement Java Evaluation (2 pts) ✅ DONE
- [x] `mvn versions:display-dependency-updates` command
- [x] `mvn dependency-check:check` command
- [x] Parsing logic
- [ ] Test on real Java project
- **Owner:** @[team-member]
- **Status:** Implemented, needs testing

#### 6. Validate Scoring Algorithm (3 pts) 🔲 TODO
- [ ] Run on Business Central Automation (3 times)
- [ ] Run on Project Enable (3 times)
- [ ] Run on Project TDC (3 times)
- [ ] Verify consistency (same score ±2 points)
- [ ] Team review of scores - are they fair?
- [ ] Document results
- **Owner:** @lovelynfnt
- **Dependencies:** Needs sub-tasks 2, 3, 4, 5 complete

#### 7. Create Test Cases (2 pts) 🔲 TODO
- [ ] Create mock .NET project with known outdated packages
- [ ] Verify expected score calculation
- [ ] Document test cases in `/docs/testing/`
- [ ] Create test for edge cases (empty project, huge project, no issues)
- **Owner:** @[team-member]

#### 8. Documentation Review (1 pt) 🔲 TODO
- [ ] Review README.md with team
- [ ] Add usage examples
- [ ] Review CONTRIBUTING.md
- [ ] Get team feedback
- **Owner:** @lovelynfnt

---

### Testing Plan

#### Consistency Testing
**Objective:** Verify evaluation produces consistent results

**Method:**
1. Run evaluation on Business Central Automation
2. Note the score
3. Run evaluation again immediately (no code changes)
4. Run evaluation a third time
5. Compare scores - should be identical or ±2 points max

**Projects to test:**
- Business Central Automation (.NET)
- Project Enable (.NET)
- [Node.js project TBD]

**Success Criteria:**
- All 3 runs produce same score ±2 points
- All 3 runs list same outdated packages
- All 3 runs list same vulnerabilities

---

#### Framework Testing
**Objective:** Verify evaluation works across different tech stacks

| Framework | Project | Expected Detection | CLI Commands | Status |
|-----------|---------|-------------------|--------------|--------|
| .NET | Business Central Automation | .NET + SpecFlow | dotnet list package | ✅ Ready |
| .NET | Project Enable | .NET + SpecFlow | dotnet list package | ⏳ Pending |
| Node.js | [TBD] | Node.js + Jest/Playwright | npm outdated, npm audit | 🔲 Need project |
| Python | [TBD] | Python + pytest | pip list --outdated | 🔲 Need project |
| Java | [TBD] | Java + JUnit/TestNG | mvn versions | 🔲 Need project |

---

#### Edge Case Testing
**Objective:** Verify metric handles unusual scenarios

| Scenario | Expected Behavior | Status |
|----------|------------------|--------|
| Empty project (no dependencies) | Shows warning, skips evaluation or scores 100 | 🔲 TODO |
| Very large project (100+ deps) | Completes within reasonable time (<5 min) | 🔲 TODO |
| Missing CLI tool (no dotnet) | Clear error message with installation link | ✅ Implemented |
| No outdated packages | Score = 100, Grade A | 🔲 TODO |
| All major outdated | Score = 0 (after ~5 packages), Grade F | 🔲 TODO |
| Network issues (can't check updates) | Graceful error, offer to retry or skip | 🔲 TODO |

---

### Risk & Dependencies

**Risks:**
- ⚠️ **CLI output format changes** - If dotnet/npm/pip changes output format, parsing breaks
  - *Mitigation:* Version check before parsing, document supported CLI versions
  
- ⚠️ **Inconsistent scores** - Same project might yield different scores if dependencies update during evaluation
  - *Mitigation:* Run all commands in sequence quickly, timestamp report
  
- ⚠️ **Large projects timeout** - Projects with 500+ dependencies might take too long
  - *Mitigation:* Show progress indicators, optimize parallel commands

**Dependencies:**
- ✅ Modular architecture complete (CLAUDE.md, evaluator.md, metrics/)
- ✅ Scoring file structure defined
- ⏳ Access to real projects for testing
- ⏳ Team availability for scoring review/approval

---

### Notes

**Decisions Made:**
- 2026-05-28: Decided to use per-metric scoring files instead of single config.json
- 2026-05-28: Decided pip-audit is optional (graceful fallback if missing)
- 2026-05-28: Threshold for "outdated minor" set to >3 versions (configurable)

**Open Questions:**
- Should we fail the evaluation if vulnerabilities found, or just report them?
  - **Decision pending:** Team discussion needed
  
- Should transitive dependencies be weighted differently than direct dependencies?
  - **Current approach:** Treat equally
  - **Alternative:** Weight transitive lower (they're harder to control)

**Future Improvements (Out of Scope for v1.0):**
- Auto-generate PR with dependency updates
- Integration with Dependabot/Renovate
- Historical trend charts (score over time)
- Comparison across multiple projects

---
