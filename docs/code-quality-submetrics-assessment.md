# Code Quality Metric: Submetrics Assessment

**Document Purpose:** Assessment of potential Code Quality submetrics for team discussion and decision-making

**Date:** 2026-05-30  
**Author:** @lovelynfnt  
**Status:** 📋 Proposal - Awaiting Team Discussion  
**Target Audience:** Agentic QA Team

---

## 🎯 Executive Summary

Code Quality is significantly more complex than Dependency Health because it encompasses multiple dimensions of code maintainability. This document proposes focusing on **3 critical submetrics** for v1.0, with a phased approach to additional submetrics.

**Recommendation:** Start with Method Length (easiest, most common issue) → Add Code Duplication → Add Cyclomatic Complexity

---

## 🧩 The Challenge

Unlike Dependency Health (single dimension: are packages up-to-date?), Code Quality has multiple dimensions:
- Code structure (method/class size)
- Code patterns (duplication, complexity)
- Code conventions (naming, formatting)
- Code organization (test structure, AAA pattern)

**Key Question:** Which submetrics give us the most value for the implementation effort?

---

## 📊 Potential Code Quality Submetrics

### 🔴 Tier 1: Critical (Must Have - Start Here)

#### 1. Method Length ⭐ **RECOMMENDED START**

**What it measures:**
- Methods/functions exceeding 50 lines
- Very long methods exceeding 100 lines

**Why it's critical:**
- **Most common issue** in test automation codebases
- Step definitions and test methods balloon to 200+ lines
- Direct impact on readability and maintainability
- Clear, objective threshold

**Real-world examples:**
```csharp
// Bad: 180-line step definition method
[Given(@"user completes entire registration flow")]
public void GivenUserCompletesRegistration() {
    // 180 lines of code here...
}

// Good: 15-line method calling smaller methods
[Given(@"user completes entire registration flow")]
public void GivenUserCompletesRegistration() {
    NavigateToRegistrationPage();
    FillPersonalDetails();
    FillAddressDetails();
    SubmitForm();
    VerifyRegistrationConfirmation();
}
```

**Detection approach:**
- Grep for method signatures: `public void.*\(` (C#), `def ` (Python), `function` (JS)
- Count lines between opening `{` and closing `}`
- Flag if > 50 lines (long) or > 100 lines (very long)

**Scoring proposal:**
- Long method (50-100 lines): **-2 points each**
- Very long method (>100 lines): **-5 points each**
- Max deduction cap: **-20 points total**

**Framework variance:** 🟡 Moderate
- Method signatures differ per language
- But line counting is universal

**Implementation difficulty:** 🟢 Easy

**Team questions:**
- Do 50/100 line thresholds feel right for our projects?
- Should we have different thresholds for different method types (step definitions vs. page objects)?

---

#### 2. Code Duplication

**What it measures:**
- Percentage of code that appears in multiple places
- Repeated setup code, repeated assertions, copy-pasted step definitions

**Why it's critical:**
- **High impact** on maintenance - bug fixes must be applied in multiple places
- Common in test automation - teams copy-paste similar test scenarios
- Leads to inconsistent behavior when one copy is updated but others aren't

**Real-world examples:**
```csharp
// Bad: Same setup code in 5 different test classes
public void SetupTestData() {
    var user = new User { Name = "Test", Email = "test@example.com" };
    Database.Insert(user);
    AuthService.Login(user);
    // ... 30 more lines
}

// Good: Extracted to shared fixture
public class TestDataFixture {
    public User CreateAndLoginTestUser() { ... }
}
```

**Detection approach:**
- **Option A (Simple):** Text comparison - find identical code blocks (20+ lines)
- **Option B (Better):** Use tools like jscpd, simian, or PMD
- Calculate % of total codebase that is duplicated

**Scoring proposal:**
- 5-10% duplication: **-15 points** (Warning)
- >10% duplication: **-25 points** (Critical)

**Framework variance:** 🟢 Universal - duplication is language-agnostic

**Implementation difficulty:** 🟡 Moderate
- Simple text matching is easy but may have false positives
- Using specialized tools requires tool installation

**Team questions:**
- What duplication % is acceptable for our projects?
- Do we count similar-but-not-identical code as duplication?
- Should framework boilerplate (e.g., test setup patterns) be excluded?

---

#### 3. Cyclomatic Complexity

**What it measures:**
- Number of decision points in a method
- Counts: if/else, switch/case, loops (for, while), ternary operators, && and ||

**Why it's critical:**
- **Design smell** - Tests with branching logic are usually doing too much
- High complexity = hard to understand all execution paths
- Indicates methods trying to handle multiple scenarios

**Real-world examples:**
```csharp
// Bad: Complexity = 8 (too high for a test method)
[Then(@"verify order status")]
public void ThenVerifyOrderStatus() {
    if (order.Type == "Standard") {
        if (order.IsPriority) {
            Assert.Equal("Expedited", order.Status);
        } else {
            Assert.Equal("Processing", order.Status);
        }
    } else if (order.Type == "Express") {
        // More branching...
    }
}

// Good: Complexity = 1 (test knows exactly what to expect)
[Then(@"standard order shows processing status")]
public void ThenStandardOrderShowsProcessingStatus() {
    Assert.Equal("Processing", order.Status);
}
```

**Detection approach:**
- Grep for control flow keywords per method: `if`, `else`, `switch`, `case`, `for`, `while`, `&&`, `||`, `?`
- Count occurrences
- Calculate complexity score

**Scoring proposal:**
- Complexity 10-15: Acceptable (no deduction)
- Complexity >15: **-5 points per method**
- Max deduction cap: **-20 points total**

**Framework variance:** 🟡 Moderate - control flow keywords vary slightly per language

**Implementation difficulty:** 🟡 Moderate - need to parse method bodies

**Team questions:**
- What complexity threshold is acceptable for test code?
- Are there valid reasons for high complexity in test automation?

---

### 🟡 Tier 2: Important (Should Have - Phase 2)

#### 4. Class Size

**What it measures:**
- Classes exceeding 300 lines
- God objects exceeding 500 lines

**Why it's important:**
- Page objects and step definition classes become dumping grounds
- Violates Single Responsibility Principle
- Hard to navigate large files

**Detection:** Count lines per class

**Scoring proposal:**
- Large class (300-500 lines): **-3 points each**
- God object (>500 lines): **-10 points each**

**Implementation difficulty:** 🟢 Easy

---

#### 5. Deep Nesting

**What it measures:**
- Nesting depth >3 levels (if inside if inside if...)

**Why it's important:**
- Very hard to read and understand
- High cognitive load

**Detection:** Track indentation/brace depth

**Scoring proposal:** **-3 points per occurrence**

**Implementation difficulty:** 🟡 Moderate - need to track nesting depth

---

#### 6. Magic Numbers/Strings

**What it measures:**
- Hardcoded values without named constants
- Example: `if (count > 5)` vs. `if (count > MAX_RETRY_COUNT)`

**Why it's important:**
- Test data scattered everywhere
- Unclear what values represent
- Hard to update when requirements change

**Detection:** Regex for numeric literals (2+ digits) or hardcoded strings

**Scoring proposal:** **-2 points per file with >5 magic numbers**

**Implementation difficulty:** 🟢 Easy

---

### 🟢 Tier 3: Nice to Have (Phase 3 or Later)

#### 7. Poor Naming Conventions
- Single-letter variables, unclear method names
- Lower priority - more subjective

#### 8. Dead Code
- Commented-out code blocks
- More annoying than critical

#### 9. Test Organization
- AAA pattern (Arrange-Act-Assert)
- Nice for consistency but very subjective

#### 10. Assertion Quality
- Vague assertions vs. specific assertions
- Hard to measure programmatically

---

## 📋 Implementation Complexity Matrix

| Submetric | Detection Difficulty | Parsing Difficulty | Framework Variance | Implementation Effort | Priority |
|-----------|---------------------|-------------------|-------------------|----------------------|----------|
| **Method Length** | 🟢 Easy | 🟢 Easy | 🟡 Some | Low | 🔴 Critical |
| **Code Duplication** | 🟡 Moderate | 🟡 Moderate | 🟢 Universal | Medium | 🔴 Critical |
| **Cyclomatic Complexity** | 🟡 Moderate | 🟡 Moderate | 🟡 Some | Medium | 🔴 Critical |
| Class Size | 🟢 Easy | 🟢 Easy | 🟡 Some | Low | 🟡 Important |
| Deep Nesting | 🟡 Moderate | 🔴 Hard | 🟡 Some | High | 🟡 Important |
| Magic Numbers | 🟢 Easy | 🟢 Easy | 🟢 Universal | Low | 🟡 Important |
| Naming | 🔴 Hard | 🔴 Hard | 🟡 Some | Very High | 🟢 Nice to Have |
| Dead Code | 🟢 Easy | 🟢 Easy | 🟢 Universal | Low | 🟢 Nice to Have |

**Legend:**
- 🟢 Easy / Low / Universal
- 🟡 Moderate / Medium / Some Variance
- 🔴 Hard / High / Significant Variance

---

## 🎯 Recommended Implementation Phases

### Phase 1: Code Quality v1.0 (MVP) - **1 Sprint**

**Scope:** Method Length only

**Why start here:**
- ✅ Easiest to implement (simple line counting)
- ✅ Most common issue in test automation
- ✅ Easy to validate (team can manually verify)
- ✅ Low risk of false positives
- ✅ Delivers immediate value

**Deliverables:**
- `code-quality.md` with Method Length evaluation logic
- `code-quality-scoring.json` with method length thresholds
- Tested on Business Central Automation, Project Enable, Project TDC
- Documentation and examples

**Frameworks:** .NET only initially

**Estimated effort:** 5 story points

---

### Phase 2: Code Quality v1.1 - **1 Sprint**

**Add:** Code Duplication submetric

**Why this next:**
- High impact on maintainability
- Combines well with method length (often correlated)
- Universal across languages

**Estimated effort:** 5 story points

---

### Phase 3: Code Quality v1.2 - **1 Sprint**

**Add:** Cyclomatic Complexity submetric

**Why this third:**
- Completes the "big 3" critical submetrics
- More complex to detect than method length
- Provides different dimension (design quality)

**Estimated effort:** 5 story points

---

### Phase 4: Code Quality v1.3 - **Future**

**Add:** Additional frameworks (Node.js, Python, Java)

**Estimated effort:** 3 story points per framework

---

### Phase 5: Code Quality v2.0 - **Future**

**Add:** Tier 2 submetrics (Class Size, Deep Nesting, Magic Numbers)

**Estimated effort:** 2-3 story points per submetric

---

## ⚙️ Scoring Structure Options

### Option A: Weighted Submetric Scores (More Complex, More Flexible)

```json
{
  "metric_name": "code_quality",
  "base_score": 100,
  "submetrics": {
    "method_length": {
      "weight": 0.4,
      "enabled": true,
      "criteria": {
        "long_method_lines": 50,
        "very_long_method_lines": 100,
        "points_deduction_long": 2,
        "points_deduction_very_long": 5,
        "max_deduction": 20
      }
    },
    "code_duplication": {
      "weight": 0.35,
      "enabled": true,
      "criteria": {
        "warning_threshold_percent": 5,
        "critical_threshold_percent": 10,
        "points_deduction_warning": 15,
        "points_deduction_critical": 25
      }
    },
    "cyclomatic_complexity": {
      "weight": 0.25,
      "enabled": true,
      "criteria": {
        "acceptable": 10,
        "high": 15,
        "points_deduction_per_method": 5,
        "max_deduction": 20
      }
    }
  }
}
```

**Calculation:**
1. Calculate each submetric score (0-100)
2. Weight them: `(method_length * 0.4) + (duplication * 0.35) + (complexity * 0.25)`
3. Final Code Quality score

**Pros:**
- Can adjust importance of each submetric
- Can disable submetrics independently
- More granular control

**Cons:**
- More complex to understand
- Harder to explain to stakeholders

---

### Option B: Simple Additive Deductions (Simpler, Easier to Understand)

```json
{
  "metric_name": "code_quality",
  "base_score": 100,
  "criteria": {
    "method_length": {
      "long_lines": 50,
      "very_long_lines": 100,
      "deduction_long": 2,
      "deduction_very_long": 5,
      "max_deduction": 20
    },
    "code_duplication": {
      "warning_threshold": 5,
      "critical_threshold": 10,
      "deduction_warning": 15,
      "deduction_critical": 25
    },
    "cyclomatic_complexity": {
      "acceptable": 10,
      "high": 15,
      "deduction_per_method": 5,
      "max_deduction": 20
    }
  }
}
```

**Calculation:**
1. Start at 100 points
2. Deduct points for each issue found
3. Apply max deduction caps per category
4. Final Code Quality score

**Pros:**
- Simpler to understand: "You lose X points for Y issue"
- Easier to explain to stakeholders
- Matches Dependency Health scoring model

**Cons:**
- Can't weight submetrics differently
- All submetrics treated equally

---

## 🤔 Open Questions for Team Discussion

### 1. Scope for v1.0

**Question:** Should we implement all 3 critical submetrics at once, or start with Method Length only?

**Option A: Method Length only (MVP approach)**
- ✅ Fastest to deliver value
- ✅ Lowest risk
- ✅ Proves the concept
- ❌ Incomplete picture of code quality

**Option B: All 3 submetrics (Complete v1.0)**
- ✅ Complete Code Quality metric from day 1
- ✅ More valuable to teams
- ❌ Longer development time
- ❌ Higher risk of delays

**My recommendation:** Option A (Method Length only), then iterate

**Team input needed:** ⬜ Vote or discuss

---

### 2. Scoring Approach

**Question:** Should submetrics have different weights, or treat all equally?

**Option A: Weighted (Method Length 40%, Duplication 35%, Complexity 25%)**
**Option B: Equal weight (Simple additive deductions)**

**Team input needed:** ⬜ Which approach feels right?

---

### 3. Threshold Values

**Question:** Are these thresholds reasonable for our projects?

| Threshold | Proposed Value | Too Strict? | Too Lenient? |
|-----------|---------------|-------------|--------------|
| Long method | 50 lines | ⬜ | ⬜ |
| Very long method | 100 lines | ⬜ | ⬜ |
| Duplication warning | 5% | ⬜ | ⬜ |
| Duplication critical | 10% | ⬜ | ⬜ |
| High complexity | >15 | ⬜ | ⬜ |

**Action:** Review against Business Central Automation and Project Enable to see what scores they'd get

**Team input needed:** ⬜ Adjust thresholds based on real projects

---

### 4. Framework Support

**Question:** Should we support all 4 frameworks (.NET, Node.js, Python, Java) from the start?

**Option A: .NET only initially**
- ✅ Faster delivery
- ✅ Covers our primary tech stack
- ✅ Can add others based on demand
- ❌ Less valuable to teams using other stacks

**Option B: All 4 frameworks**
- ✅ More universally useful
- ❌ 4x implementation effort
- ❌ Hard to test thoroughly

**My recommendation:** Option A (.NET only), expand later based on usage

**Team input needed:** ⬜ Confirm this is acceptable

---

### 5. Tool Dependencies

**Question:** Should we require external tools (static analyzers) or use only Grep/Read?

**For Method Length:** Grep/Read sufficient ✅

**For Code Duplication:**
- **Option A:** Simple text comparison (no tools needed)
  - ✅ No dependencies
  - ❌ May have false positives
  
- **Option B:** Use jscpd or similar tool
  - ✅ More accurate
  - ❌ Requires tool installation
  - ❌ Another dependency to maintain

**For Complexity:**
- **Option A:** Count control flow keywords (Grep)
  - ✅ No dependencies
  - ❌ Approximation only
  
- **Option B:** Use Roslyn analyzers (.NET), ESLint (JS), etc.
  - ✅ Precise metrics
  - ❌ Framework-specific tools

**My recommendation:** Start with Option A (no external tools) for all 3

**Team input needed:** ⬜ Is this acceptable, or should we invest in tool integration?

---

### 6. Reporting Detail Level

**Question:** How much detail should the report include?

**Option A: Summary only**
- "Found 12 long methods, 3 very long methods"
- Score: 75/100

**Option B: Full list**
- "Found 12 long methods:"
  - `LoginSteps.cs:45` - `WhenUserLogsIn()` (87 lines)
  - `CheckoutSteps.cs:120` - `GivenUserCompletesCheckout()` (143 lines)
  - ... (all 12 listed)
- Score: 75/100

**My recommendation:** Option B for markdown report, Option A for conversational summary

**Team input needed:** ⬜ Confirm this level of detail is useful

---

## ✅ Proposed Next Steps

### Immediate (This Week)
1. **Team discussion** - Review this document, discuss open questions
2. **Vote on decisions** - Scope, thresholds, approach
3. **Create JIRA ticket** - Use template from `.github/ISSUE_TEMPLATE/metric-story.md`
4. **Assign owner** - Who will implement Code Quality v1.0?

### Sprint Planning
1. **Estimate effort** - Based on scope decision
2. **Add to backlog** - Code Quality v1.0 story
3. **Plan dependencies** - Need access to test projects

### Implementation (Next Sprint)
1. **Phase 1: Method Length** - Implement, test, validate
2. **Get team feedback** - Run on real projects, review scores
3. **Iterate if needed** - Adjust thresholds based on feedback

---

## 📎 Related Documents

- **JIRA Ticket Template:** `.github/ISSUE_TEMPLATE/metric-story.md`
- **Team Workflow Guide:** `docs/team-workflow-guide.md`
- **Dependency Health Example:** `docs/jira-ticket-dependency-health.md`
- **Contributing Guidelines:** `CONTRIBUTING.md`

---

## 💬 Feedback & Discussion

**How to provide input:**
1. **In-person:** Discuss at next team meeting
2. **Async:** Comment in Slack #agentic-qa channel
3. **Formal:** Comment on JIRA ticket once created

**Key decisions needed:**
- [ ] Scope for v1.0 (Method Length only, or all 3 submetrics?)
- [ ] Scoring approach (Weighted vs. Additive?)
- [ ] Threshold values (Are 50/100 line thresholds reasonable?)
- [ ] Framework support (Start with .NET only?)
- [ ] Tool dependencies (Use external tools or just Grep/Read?)

---

## 📊 Appendix: Comparison with Dependency Health

| Aspect | Dependency Health | Code Quality |
|--------|------------------|--------------|
| **Dimensions** | 1 (package freshness) | 3-10 (multiple code aspects) |
| **Detection** | CLI commands | Pattern matching / static analysis |
| **Objectivity** | Highly objective | More subjective (thresholds debatable) |
| **Frameworks** | Different commands per framework | Different syntax per language |
| **Complexity** | 🟢 Low | 🔴 High |
| **Implementation** | 1 sprint | 3-5 sprints (phased) |

**Key insight:** Code Quality is inherently more complex. Phased approach is prudent.

---

**Document Version:** 1.0  
**Last Updated:** 2026-05-30  
**Next Review:** After team discussion  
**Owner:** @lovelynfnt
