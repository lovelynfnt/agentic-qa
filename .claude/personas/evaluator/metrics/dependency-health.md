# Dependency Health Metric

This metric evaluates how up-to-date and secure a project's dependencies are.

## Prerequisites

You should have received this context from the evaluator persona:
- Project Path
- Project Type (dotnet, nodejs, python, java)
- Test Framework
- Test Count

If not provided, you must detect these first.

## Evaluation Steps

This metric executes **Steps 3-5** of the evaluation flow.

---

## STEP 3: Metric-Specific Pre-Check

Validate the project has what's needed for Dependency Health evaluation.

### 3.1 Check for Project Files with Dependencies

**For .NET projects:**
- Find all `.csproj` files using Glob: `**/*.csproj`
- Read at least one `.csproj` file
- Verify it contains `<PackageReference>` elements
- If none found: ⚠️ "No package dependencies found in .csproj files"

**For Node.js projects:**
- Find `package.json` using Glob: `**/package.json`
- Read the file
- Verify it has `dependencies` or `devDependencies` sections
- If empty: ⚠️ "No dependencies found in package.json"

**For Python projects:**
- Find `requirements.txt` or `pyproject.toml`
- Read the file
- Verify file is not empty
- If empty: ⚠️ "No dependencies found"

**For Java projects:**
- Find `pom.xml` or `build.gradle`
- Read the file
- Verify it has dependency declarations
- If none: ⚠️ "No dependencies found"

### 3.2 Check for Required CLI Tools

**For .NET:**
```bash
dotnet --version
```
If command fails: ❌ "dotnet CLI not available - cannot check dependencies"

**For Node.js:**
```bash
npm --version
```
If command fails: ❌ "npm CLI not available - cannot check dependencies"

**For Python:**
```bash
pip --version
```
If command fails: ❌ "pip CLI not available - cannot check dependencies"

**For Java:**
```bash
mvn --version
```
Or:
```bash
gradle --version
```
If both fail: ❌ "Maven/Gradle not available - cannot check dependencies"

### 3.3 Display Pre-Check Results

**If all checks pass:**
```
Validating project for Dependency Health evaluation...

✅ Found: [N] .csproj files with package references
✅ dotnet CLI available (version [X.Y.Z])

Starting Dependency Health evaluation...

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📋 Protocol: Dependency Health Pre-Check | 📄 Step 3 of 5
```

**If checks fail:**
```
Validating project for Dependency Health evaluation...

❌ [Specific issue found]
⚠️ Cannot proceed with Dependency Health evaluation

Would you like to:
• Select a different metric
• Fix the issue and try again
• Cancel evaluation

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📋 Protocol: Dependency Health Pre-Check | 📄 Step 3 of 5
```

Use AskUserQuestion to let user choose how to proceed. If they choose different metric, return control to evaluator.md.

---

## STEP 4: Run Dependency Health Evaluation

Show brief status:
```
Running Dependency Health evaluation...
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📋 Protocol: Running Evaluation | 📄 Step 4 of 5
```

### 4.1 List All Dependencies

Run the appropriate command based on project type.

**For .NET:**
```bash
cd <project_path>
dotnet list package --include-transitive
```

Parse output to extract:
- Package name
- Current version
- Whether it's direct or transitive

Store total package count: `total_packages`, `direct_packages`, `transitive_packages`

**For Node.js:**
```bash
cd <project_path>
npm list --json
```

**For Python:**
```bash
cd <project_path>
pip list --format=json
```

**For Java:**
```bash
cd <project_path>
mvn dependency:list
```

### 4.2 Check for Outdated Packages

**For .NET:**
```bash
cd <project_path>
dotnet list package --outdated
```

Parse output to categorize by semantic versioning:
- **Major** version behind: Current 1.x.x vs Latest 2.x.x (or higher)
- **Minor** version behind: Current 1.1.x vs Latest 1.5.x (difference > 3)
- **Patch** version behind: Current 1.1.1 vs Latest 1.1.8 (difference > 5)

**For Node.js:**
```bash
cd <project_path>
npm outdated --json
```

**For Python:**
```bash
cd <project_path>
pip list --outdated --format=json
```

**For Java:**
```bash
cd <project_path>
mvn versions:display-dependency-updates
```

Store counts:
- `major_outdated_count`
- `major_outdated_list` (array of {package, current, latest})
- `minor_outdated_count`
- `minor_outdated_list`
- `patch_outdated_count`
- `patch_outdated_list`

### 4.3 Check for Security Vulnerabilities

**For .NET:**
```bash
cd <project_path>
dotnet list package --vulnerable --include-transitive
```

Parse output for vulnerabilities by severity: Critical, High, Moderate, Low

**For Node.js:**
```bash
cd <project_path>
npm audit --json
```

**For Python:**
```bash
cd <project_path>
pip-audit --format=json 2>/dev/null
```
Note: pip-audit may not be installed. If command fails, note: "⚠️ pip-audit not installed - security check skipped. Install with: pip install pip-audit"

**For Java:**
```bash
cd <project_path>
mvn dependency-check:check
```

Store vulnerability data:
- `critical_vulns_count`
- `critical_vulns_list` (array of {cve, package, severity, description, fixed_version})
- `high_vulns_count`
- `high_vulns_list`
- `moderate_vulns_count`
- `moderate_vulns_list`
- `low_vulns_count`
- `low_vulns_list`

### 4.4 Calculate Dependency Health Score

Load deduction rules from: `.claude/personas/evaluator/metrics/dependency-health-scoring.json`

Start with base score: **100 points**

**Deduct points for outdated packages:**
- Major versions behind: **-20 points per package** (config: `outdated_major.points_deduction`)
- Minor versions behind (if >3 threshold): **-5 points per package** (config: `outdated_minor.points_deduction`)
- Patch versions behind (if >5 threshold): **-2 points per package** (config: `outdated_patch.points_deduction`)

**Deduct points for vulnerabilities:**
- Critical severity: **-25 points each** (config: `security_vulnerabilities.points_deduction_per_vuln.critical`)
- High severity: **-15 points each** (config: `security_vulnerabilities.points_deduction_per_vuln.high`)
- Moderate severity: **-8 points each** (config: `security_vulnerabilities.points_deduction_per_vuln.moderate`)
- Low severity: **-3 points each** (config: `security_vulnerabilities.points_deduction_per_vuln.low`)

**Final score cannot go below 0.**

Calculate: `dependency_health_score`

**Assign Grade** (from config grade_thresholds):
- 90-100: Grade A
- 80-89: Grade B
- 70-79: Grade C
- 60-69: Grade D
- 0-59: Grade F

### 4.5 Categorize Findings

**Critical Issues:**
- Any package with major version gap (1+ major versions behind)
- Any Critical severity vulnerability
- Any High severity vulnerability

**Warnings:**
- Packages with minor version gaps (>3 minor versions behind per config threshold)
- Moderate severity vulnerabilities

**Info:**
- Packages with patch version gaps (>5 patch versions behind per config threshold)
- Low severity vulnerabilities

### 4.6 Generate Recommendations

For each critical issue and warning, provide specific actionable recommendation.

**For outdated packages:**
```
Update [PackageName] from [CurrentVersion] to [LatestVersion]
Command: dotnet add package [PackageName] --version [LatestVersion]
```

**For vulnerabilities:**
```
Fix [CVE-YYYY-XXXXX]: [Severity] vulnerability in [PackageName]
Impact: [Brief description from vulnerability data]
Action: Update [PackageName] to version [SafeVersion] or higher
Command: dotnet add package [PackageName] --version [SafeVersion]
```

**Long-term recommendations:**
- "Set up Dependabot or Renovate for automated dependency updates"
- "Add dependency security checks to CI/CD pipeline"
- "Schedule regular dependency review meetings (monthly recommended)"
- "Enable GitHub/GitLab security alerts for this repository"

---

## STEP 5: Generate Report

### 5.1 Conversational Summary

Display results in this format:

```
## Dependency Health Evaluation Complete - Score: [X/100] [Emoji]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📋 Protocol: Report Generation | 📄 Step 5 of 5
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

[Brief 2-3 sentence summary of overall health and key findings]

### 📦 Package Analysis
- **Total Packages:** [N] packages ([X] direct, [Y] transitive)
- **Outdated:** [Z] packages need updates ([A] major, [B] minor, [C] patch)
- **Vulnerabilities:** [V] security issues found ([Critical: X, High: Y, Moderate: Z, Low: W])

### 🚨 Critical Issues ([N] found)

1. **[PackageName]** is [N] major versions behind
   - Current: [X.Y.Z] → Latest: [A.B.C]
   - Impact: [Brief explanation - missing features, security fixes, compatibility]
   - Fix: `dotnet add package [PackageName] --version [A.B.C]`

2. **[CVE-YYYY-XXXXX]**: [Severity] vulnerability in [PackageName]
   - Impact: [Security issue description]
   - Fix: Update to version [SafeVersion] or higher
   - Command: `dotnet add package [PackageName] --version [SafeVersion]`

[List all critical issues - be specific, no summaries]

### ⚠️ Warnings ([N] found)

[List warnings in similar format - minor version gaps and moderate vulnerabilities]

### 💡 Top Recommendations

1. ⚡ **Priority:** [Most critical action with specific command]
2. [Second most important action with command]
3. [Long-term improvement suggestion]

---

**Grade:** [A/B/C/D/F] based on score

📊 Detailed report saved to: .claude/evaluations/[timestamp]_[project-name]_dependency-health.md
📈 History saved to: .claude/evaluations/history/[timestamp]_[project-name]_dependency-health.json

Would you like me to:
• Help you fix these issues
• Evaluate another metric
• Explain any of these findings in detail

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✅ Evaluation Complete
🤖 Test Automation Evaluator v1.0
```

**Emoji Guide for Score:**
- 90-100: 🟢 Excellent
- 75-89: 🟡 Good  
- 60-74: 🟠 Needs Attention
- 0-59: 🔴 Critical

### 5.2 Detailed Markdown Report

Generate and save detailed report to: `.claude/evaluations/[YYYY-MM-DD_HHMMSS]_[project-name]_dependency-health.md`

Where `[project-name]` is derived from the project path (use the last directory name, sanitized for filename safety).

**Report Structure:**

```markdown
# Dependency Health Evaluation Report

**Project:** [Name or Path]
**Date:** [YYYY-MM-DD HH:MM:SS]
**Project Type:** [.NET/Node.js/Python/Java]
**Test Framework:** [Detected Framework]
**Metric:** Dependency Health

**Score:** [X/100] (Grade [A/B/C/D/F])

---

## Executive Summary

[2-3 paragraph summary covering:
- Overall dependency health status
- Most critical issues requiring immediate attention
- Key statistics (X packages outdated, Y vulnerabilities)
- Impact on project (security risk, missing features, technical debt)]

---

## Package Analysis

### Overview
- **Total Packages:** [N] ([X] direct, [Y] transitive)
- **Up-to-date:** [N] packages (X%)
- **Outdated:** [N] packages (Y%)
  - Major updates available: [N]
  - Minor updates available: [N]
  - Patch updates available: [N]
- **Security Vulnerabilities:** [N] total
  - Critical: [N]
  - High: [N]
  - Moderate: [N]
  - Low: [N]

### Outdated Packages

#### Major Version Updates Required ([N] packages)

| Package | Current | Latest | Versions Behind | Risk Level |
|---------|---------|--------|----------------|------------|
| [Name] | [X.Y.Z] | [A.B.C] | [N] major | High |
| ... | ... | ... | ... | ... |

**Impact:** Major version updates may include breaking changes, new features, and critical security fixes.

#### Minor Version Updates Available ([N] packages)

| Package | Current | Latest | Versions Behind |
|---------|---------|--------|----------------|
| [Name] | [X.Y.Z] | [A.B.C] | [N] minor |
| ... | ... | ... | ... |

**Impact:** Minor updates typically include new features and bug fixes without breaking changes.

#### Patch Updates Available ([N] packages)

| Package | Current | Latest | Versions Behind |
|---------|---------|--------|----------------|
| [Name] | [X.Y.Z] | [A.B.C] | [N] patch |
| ... | ... | ... | ... |

**Impact:** Patch updates include bug fixes and minor improvements.

---

## Security Vulnerabilities

### Critical Severity ([N] vulnerabilities)

**1. [CVE-YYYY-XXXXX] - [PackageName] [Version]**
- **Severity:** Critical
- **Impact:** [Detailed description of security issue and exploit scenario]
- **Affected Versions:** [Range]
- **Fixed In:** [Version]
- **Action:** 
  ```bash
  dotnet add package [PackageName] --version [SafeVersion]
  ```

[Repeat for each critical vulnerability]

### High Severity ([N] vulnerabilities)

[Same detailed format]

### Moderate Severity ([N] vulnerabilities)

[Same detailed format]

### Low Severity ([N] vulnerabilities)

[Brief list with CVE IDs and package names]

---

## Score Breakdown

**Starting Score:** 100

**Deductions:**
- Major version gaps: -[X] points ([N] packages × 20 points each)
- Minor version gaps: -[X] points ([N] packages × 5 points each)
- Patch version gaps: -[X] points ([N] packages × 2 points each)
- Critical vulnerabilities: -[X] points ([N] × 25 points each)
- High vulnerabilities: -[X] points ([N] × 15 points each)
- Moderate vulnerabilities: -[X] points ([N] × 8 points each)
- Low vulnerabilities: -[X] points ([N] × 3 points each)

**Final Score:** [X/100]  
**Grade:** [A/B/C/D/F]

---

## Prioritized Action Items

### 🔥 Immediate (This Week)
1. [ ] Fix [CVE-YYYY-XXXXX] in [PackageName] - Critical security vulnerability
   - Command: `dotnet add package [PackageName] --version [SafeVersion]`
2. [ ] Update [PackageName] from [X] to [Y] - Major version gap with security fixes
   - Command: `dotnet add package [PackageName] --version [Y]`
3. [ ] [Other critical items with specific commands]

### 📋 Short Term (Next Sprint - 1-2 weeks)
1. [ ] Update [N] packages with minor version gaps
   - [List with commands]
2. [ ] Review and update remaining moderate vulnerabilities
   - [List with commands]
3. [ ] [Other important items]

### 🎯 Long Term (Next Quarter)
1. [ ] Set up Dependabot or Renovate for automated dependency updates
2. [ ] Add `dotnet list package --vulnerable` to CI/CD pipeline
3. [ ] Establish dependency update policy and review schedule
4. [ ] Document approved package versions and update process

---

## Recommendations

### Immediate Actions

[Specific step-by-step instructions for fixing critical issues, including exact commands]

### Process Improvements

**Automation:**
- Set up Dependabot or Renovate for automated pull requests when updates are available
- Configure automated security scanning in your repository

**CI/CD Integration:**
- Add dependency vulnerability checking to build pipeline
- Fail builds on critical/high vulnerabilities
- Generate dependency reports on each build

**Regular Reviews:**
- Schedule monthly dependency review sessions
- Assign ownership for dependency management
- Track dependency health metrics over time

**Security Alerts:**
- Enable GitHub/GitLab/Azure DevOps security alerts
- Subscribe to security advisories for critical packages
- Establish process for emergency security updates

### Best Practices

- Keep major versions up-to-date to avoid large migration efforts later
- Always review release notes before updating major versions
- Test thoroughly after dependency updates (especially major versions)
- Maintain a CHANGELOG documenting dependency updates
- Use lock files to ensure consistent builds (packages.lock.json, package-lock.json)

---

## Evaluation Details

**Evaluated By:** Test Automation Evaluator v1.0  
**Evaluation Duration:** [X] seconds  
**Configuration:** `.claude/personas/evaluator/config.json`  
**CLI Tools Used:**
- [Tool name] version [X.Y.Z]

**Commands Executed:**
- `dotnet list package --include-transitive`
- `dotnet list package --outdated`
- `dotnet list package --vulnerable --include-transitive`

---

*Generated by Test Automation Evaluator | Agentic QA*
```

### 5.3 Save Evaluation History (JSON)

Save complete evaluation data to: `.claude/evaluations/history/[YYYY-MM-DD_HHMMSS]_[project-name]_dependency-health.json`

Where `[project-name]` is derived from the project path (use the last directory name, sanitized for filename safety).

```json
{
  "timestamp": "2026-05-27T17:30:00Z",
  "metric": "dependency_health",
  "project_path": "/absolute/path/to/project",
  "project_type": "dotnet",
  "test_framework": "SpecFlow",
  "test_count": 42,
  "score": 85,
  "grade": "B",
  "findings": {
    "total_packages": 42,
    "direct_packages": 15,
    "transitive_packages": 27,
    "up_to_date_packages": 27,
    "outdated": {
      "major": 2,
      "minor": 5,
      "patch": 8,
      "total": 15
    },
    "vulnerabilities": {
      "critical": 0,
      "high": 1,
      "moderate": 3,
      "low": 2,
      "total": 6
    }
  },
  "critical_issues": [
    {
      "type": "outdated_major",
      "package": "Selenium.WebDriver",
      "current_version": "3.141.0",
      "latest_version": "4.15.0",
      "versions_behind": 1,
      "points_deducted": 20
    },
    {
      "type": "vulnerability",
      "cve": "CVE-2023-12345",
      "package": "Newtonsoft.Json",
      "severity": "High",
      "description": "Deserialization vulnerability",
      "current_version": "12.0.3",
      "fixed_in": "13.0.2",
      "points_deducted": 15
    }
  ],
  "warnings": [],
  "recommendations": [
    "Update Selenium.WebDriver from 3.141.0 to 4.15.0",
    "Fix CVE-2023-12345 by updating Newtonsoft.Json to 13.0.2",
    "Set up Dependabot for automated updates"
  ],
  "score_breakdown": {
    "base_score": 100,
    "deductions": {
      "major_outdated": -40,
      "minor_outdated": -25,
      "patch_outdated": -16,
      "critical_vulns": 0,
      "high_vulns": -15,
      "moderate_vulns": -24,
      "low_vulns": -6
    },
    "final_score": 85
  }
}
```

---

## Error Handling

### If CLI Tool Not Available

```
❌ Cannot check dependencies - [tool] not found

To proceed with Dependency Health evaluation, please:
1. Install [tool]: [installation instructions]
2. Ensure [tool] is in your PATH
3. Try the evaluation again

Would you like to:
• Select a different metric
• Cancel evaluation
```

Use AskUserQuestion. If selecting different metric, return control to evaluator.md.

### If No Dependencies Found

```
⚠️ No dependencies found in this project

This might mean:
• Project has no external dependencies (unusual for test automation)
• Package files are misconfigured or empty
• Wrong path provided

Would you like to:
• Check a different path
• Select a different metric
• Cancel evaluation
```

### If Commands Fail

```
❌ Error running dependency check

Command failed: [command]
Error: [error message]

This might be due to:
• Project not restored/built (try: dotnet restore)
• Network issues (if checking for updates requires internet)
• Permission issues
• Corrupted package cache

Would you like to:
• Try again
• Skip this check and continue with available data
• Cancel evaluation
```

Use AskUserQuestion for user choice.

---

## Output Style

**Be specific:**
- Use exact version numbers
- Provide complete commands
- Reference specific CVE IDs when available

**Be actionable:**
- Every finding must have a clear action
- Provide exact commands to run
- Prioritize by impact (security > features > minor updates)

**Be balanced:**
- Acknowledge what's working well
- Frame issues as improvement opportunities
- Consider project context (legacy projects may have more outdated dependencies)

---

## Notes

- Use parallel Bash commands where possible for speed
- Handle errors gracefully - if one check fails, continue with others
- Consider project context when making recommendations
- Offer to help implement fixes after evaluation
