# Agentic QA: Metrics Reference Guide

**Document Purpose:** Quick reference for all evaluation metrics - what we measure, why it matters, and target tech stacks

**Last Updated:** 2026-06-02  
**Status:** Living document - updated as metrics evolve  
**Audience:** Team members, stakeholders, new contributors

---

## 📊 Overview

Agentic QA evaluates test automation projects across **3 core metrics** to provide a comprehensive quality assessment with actionable recommendations.

### Metrics at a Glance

| # | Metric | Status | Weight | Target Tech Stacks | Submetrics |
|---|--------|--------|--------|-------------------|------------|
| 1 | **Dependency Health** | ✅ Implemented | ⏳ Pending | .NET, Node.js, Python, Java | • Outdated packages<br>• Security vulnerabilities |
| 2 | **Code Quality** | 🚧 Planned | ⏳ Pending | .NET (others later) | **Critical (v1.0):**<br>• Method Length<br>• Code Duplication<br>• Cyclomatic Complexity<br>**Important (v2.0):**<br>• Class Size<br>• Deep Nesting<br>• Magic Numbers |
| 3 | **Test Performance** | 🚧 Planned | ⏳ Pending | .NET, Node.js, Python, Java | • Avg test duration<br>• Total suite time<br>• Tests per minute<br>• Slow tests (>30s)<br>• Parallelization |

### Overall Score Calculation

**Status:** ⏳ **PENDING - Requires team discussion**

**Decision needed:** How should metrics be weighted?

**Proposed options:**
- **Option A:** Equal weight (33.33% each)
- **Option B:** Code Quality prioritized (40% Quality, 30% Dependency, 30% Performance)
- **Option C:** Custom weights based on team priorities

**Once decided:**
```
Overall Score = (Dependency Health × weight₁) + (Code Quality × weight₂) + (Test Performance × weight₃)
```

**Grading Scale:**
- 90-100: Grade A 🟢 Excellent
- 80-89: Grade B 🟡 Good
- 70-79: Grade C 🟡 Acceptable
- 60-69: Grade D 🟠 Needs Attention
- 0-59: Grade F 🔴 Critical

---

## 1️⃣ Metric 1: Dependency Health

**Status:** ✅ Implemented  
**Files:** `dependency-health.md`, `dependency-health-scoring.json`

### What It Measures

- Outdated packages (major, minor, patch versions behind)
- Known security vulnerabilities (critical, high, moderate, low severity)

### Why It Matters

- **Security:** Outdated dependencies contain exploitable vulnerabilities
- **Technical Debt:** Major version gaps make upgrades increasingly difficult
- **Cost:** Bug fixes and support only available for current versions
- **Compliance:** Failed audits and security requirements

### Target Tech Stacks

| Framework | CLI Tool | Status |
|-----------|----------|--------|
| .NET | `dotnet` | ✅ Supported |
| Node.js | `npm` | ✅ Supported |
| Python | `pip` | ✅ Supported |
| Java | `mvn`/`gradle` | ✅ Supported |

### Scoring (Base: 100 points)

**Deductions:**
- Major version outdated: **-20 points** per package
- Minor version outdated (>3 versions): **-5 points** per package
- Patch version outdated (>5 versions): **-2 points** per package
- Critical vulnerability: **-25 points** each
- High vulnerability: **-15 points** each
- Moderate vulnerability: **-8 points** each
- Low vulnerability: **-3 points** each

### Example Result

**Project:** Business Central Automation  
**Score:** 65/100 (Grade D)  
**Issues:** 2 major outdated, 1 high vulnerability, 5 minor outdated

---

## 2️⃣ Metric 2: Code Quality

**Status:** 🚧 Planned  
**Priority:** Implement after Dependency Health  
**Details:** See `code-quality-submetrics-assessment.md` for full analysis

### What It Measures

Test code maintainability through **multiple submetrics** (3 critical + 3 important):

**Critical Submetrics (Must Have - v1.0):**
1. **Method Length** - Methods exceeding 50 lines (very long: >100)
2. **Code Duplication** - Repeated code blocks (>5% threshold)
3. **Cyclomatic Complexity** - Decision points per method (>15 problematic)

**Important Submetrics (Phase 2):**
4. Class Size - Classes exceeding 300 lines (god objects: >500)
5. Deep Nesting - Nesting depth >3 levels
6. Magic Numbers - Hardcoded values without constants

### Why It Matters

- **Maintenance Cost:** Poor code = expensive changes and debugging
- **Bug Risk:** Complex code leads to more bugs and flaky tests
- **Team Velocity:** Bad code slows down feature development
- **Reliability:** Hard-to-understand tests don't catch bugs effectively

### Target Tech Stacks

| Framework | v1.0 Support | Future |
|-----------|-------------|--------|
| .NET (C#) | ✅ Yes | - |
| Node.js (JS/TS) | ❌ No | 🔜 Phase 2 |
| Python | ❌ No | 🔜 Phase 2 |
| Java | ❌ No | 🔜 Phase 2 |

### Proposed Scoring (Base: 100 points)

**⏳ PENDING - Team must decide:**
- Which submetrics for v1.0? (All 3 critical, or just Method Length?)
- Weighted scoring or simple deductions?
- Are thresholds reasonable? (50/100 lines, 5% duplication, complexity >15)

**Proposed Deductions (Option: Simple Additive):**

**Method Length:**
- Long method (50-100 lines): **-2 points** each (max -20)
- Very long method (>100 lines): **-5 points** each (max -30)

**Code Duplication:**
- 5-10% duplication: **-15 points**
- >10% duplication: **-25 points**

**Cyclomatic Complexity:**
- Per method with complexity >15: **-5 points** (max -20)

### Example Result (Projected)

**Project:** Business Central Automation  
**Score:** 76/100 (Grade C)  
**Issues:** 12 long methods, 8% duplication, 5 complex methods

**Detailed Analysis:** See `code-quality-submetrics-assessment.md`

---

## 3️⃣ Metric 3: Test Performance

**Status:** 🚧 Planned  
**Priority:** After Code Quality or in parallel

### What It Measures

- Average test duration (milliseconds per test)
- Total suite execution time (minutes)
- Tests per minute (throughput)
- Slow tests (>30-second threshold)
- Parallelization status

### Why It Matters

- **Productivity:** Slow tests = slow feedback loop, developers skip running them
- **Cost:** CI/CD minutes are expensive, long-running pipelines block deployments
- **Quality:** Slow suites run less frequently, reducing confidence
- **Morale:** Waiting for tests is frustrating and demotivating

### Target Tech Stacks

| Framework | Test Runner | Result Format | Status |
|-----------|-------------|---------------|--------|
| .NET | `dotnet test` | `.trx` files | 🚧 Planned |
| Node.js | Jest, Mocha, Playwright | JSON output | 🚧 Planned |
| Python | pytest | `junit.xml` | 🚧 Planned |
| Java | JUnit, TestNG | XML reports | 🚧 Planned |

### Proposed Scoring (Base: 100 points)

**Scoring Dimensions (average of three):**

1. **Average Test Duration:**
   - < 1s: 100 pts | 1-3s: 85 pts | 3-5s: 70 pts | 5-10s: 50 pts | >10s: 30 pts

2. **Total Suite Duration:**
   - < 5 min: 100 pts | 5-15 min: 85 pts | 15-30 min: 70 pts | 30-60 min: 50 pts | >60 min: 30 pts

3. **Tests Per Minute:**
   - > 10: 100 pts | 5-10: 85 pts | 2-5: 70 pts | 1-2: 50 pts | < 1: 30 pts

**Adjustments:**
- Slow tests (>30s each): **-5 points** per test (max -30)
- No parallelization: **-10 points**

### Example Result (Projected)

**Project:** Business Central Automation  
**Score:** 72/100 (Grade C)  
**Issues:** 156 tests in 8.2 min (3.2s avg), 5 slow tests, sequential execution

---

## 📋 Implementation Roadmap

### Phase 1: Foundation ✅ **COMPLETE**
- Modular architecture (CLAUDE.md → evaluator → metrics)
- Dependency Health metric implemented
- Tested on real projects
- Team workflow and documentation

### Phase 2: Code Quality 🎯 **NEXT**
- **v1.0:** Method Length submetric (.NET only)
- **v1.1:** Add Code Duplication submetric
- **v1.2:** Add Cyclomatic Complexity submetric
- **v2.0:** Add remaining submetrics + other frameworks

### Phase 3: Test Performance 📅 **AFTER CODE QUALITY**
- Implement for .NET first
- Expand to other frameworks
- Historical trend tracking

### Phase 4: Overall Score 🎓 **AFTER ALL 3 METRICS**
- Finalize metric weights
- Implement weighted scoring
- Generate combined reports

---

## 🎯 Framework Support Matrix

| Framework | Dependency Health | Code Quality | Test Performance |
|-----------|------------------|--------------|-----------------|
| **.NET** | ✅ v1.0 | 🚧 v1.0 Planned | 🚧 Planned |
| **Node.js** | ✅ v1.0 | 🔜 Future | 🚧 Planned |
| **Python** | ✅ v1.0 | 🔜 Future | 🚧 Planned |
| **Java** | ✅ v1.0 | 🔜 Future | 🚧 Planned |

---

## 💡 Design Principles

### Why These 3 Metrics?

**Comprehensive Coverage:**
- **Dependency Health:** External quality (third-party packages)
- **Code Quality:** Internal quality (our test code)
- **Test Performance:** Operational quality (execution efficiency)

**Balanced Approach:**
- Can't compensate for terrible code with fast execution
- Can't ignore security while claiming good code quality
- Forces attention to all dimensions

**Automation-Specific:**
- Tailored to test automation challenges
- Addresses common anti-patterns
- Focuses on maintainability (tests ARE code!)

**Actionable:**
- Every finding has specific recommendation
- Clear commands to fix issues
- Prioritized action items

---

## 🤔 Open Questions (Team Discussion Needed)

### 1. Overall Score Weights
- [ ] Should all metrics be weighted equally?
- [ ] Or should Code Quality carry more weight (40%)?
- [ ] Custom weights based on organizational priorities?

### 2. Code Quality Scope
- [ ] Start with Method Length only (MVP)?
- [ ] Or implement all 3 critical submetrics at once?
- [ ] See `code-quality-submetrics-assessment.md` for details

### 3. Code Quality Thresholds
- [ ] Are 50/100 line thresholds reasonable?
- [ ] Is 5% duplication warning / 10% critical fair?
- [ ] Is complexity >15 the right cutoff?

### 4. Metric Priority Order
- [ ] Implement Code Quality next (more complex)?
- [ ] Or Test Performance next (simpler)?
- [ ] Or both in parallel?

---

## 🔗 Related Documents

**For Detailed Context:**
- Code Quality Deep Dive: `code-quality-submetrics-assessment.md`
- Dependency Health Example: `jira-ticket-dependency-health.md`

**For Implementation:**
- Team Workflow: `team-workflow-guide.md`
- JIRA Template: `metric-story-jira-template.md`
- Contributing Guide: `CONTRIBUTING.md`

**For Reference:**
- Metric Implementation: `.claude/personas/evaluator/metrics/[metric-name].md`
- Scoring Configuration: `.claude/personas/evaluator/metrics/[metric-name]-scoring.json`

---

## 📊 Summary Table

| Aspect | Dependency Health | Code Quality | Test Performance |
|--------|------------------|--------------|-----------------|
| **Implementation Status** | ✅ Done | 🚧 Planned (Next) | 🚧 Planned |
| **Complexity** | 🟢 Low | 🔴 High (submetrics) | 🟡 Medium |
| **Value** | 🔴 High (security) | 🔴 High (maintainability) | 🟡 Medium (efficiency) |
| **Framework Support** | 4 frameworks | .NET only initially | 4 frameworks planned |
| **Detection Approach** | CLI commands | Pattern matching | Parse test results |
| **Estimated Effort** | 1 sprint (done) | 2-3 sprints (phased) | 1-2 sprints |

---

**Document Owner:** @lovelynfnt  
**Last Updated:** 2026-06-02  
**Version:** 1.1  
**Next Review:** After team discussion on weights and Code Quality scope
