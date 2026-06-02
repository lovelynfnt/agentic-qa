# Agentic QA

> AI-powered test automation quality evaluator - Assess your test automation projects with structured, repeatable evaluations

[![Claude Code](https://img.shields.io/badge/Built%20for-Claude%20Code-8A2BE2)](https://claude.ai/code)
[![Version](https://img.shields.io/badge/version-1.0.0-green.svg)](CHANGELOG.md)

**Agentic QA** is a specialized evaluation tool for test automation projects. It provides comprehensive, structured assessments of test automation quality across multiple dimensions, helping teams identify issues and track improvements over time.

## 🎯 What is Agentic QA?

Agentic QA uses Claude Code's CLAUDE.md feature to provide structured, multi-step evaluations of test automation projects. Unlike generic AI assistance, it follows a systematic evaluation protocol with:

- **Structured workflows** - Consistent 5-step evaluation process
- **Metric-specific analysis** - Deep-dive into dependency health, performance, and code quality
- **Actionable recommendations** - Specific commands and steps to improve
- **Historical tracking** - Compare evaluations over time to measure progress
- **Framework-agnostic** - Works with .NET, Node.js, Python, and Java projects

## 🤖 Available Evaluations

### 📊 Dependency Health ✅ (Available Now)

Evaluate package freshness and security vulnerabilities.

**What it checks:**
- Outdated packages (major, minor, patch versions)
- Security vulnerabilities (critical, high, moderate, low)
- Framework-specific dependency commands
- Generates actionable update commands

**How to use:**
```bash
cd /path/to/agentic-qa
claude
# Then type:
"Evaluate this project /path/to/your-automation-project"
```

---

### ⚡ Test Performance 🚧 (Coming Soon)

Measure test execution speed and identify bottlenecks.

**Planned checks:**
- Average test duration
- Total suite execution time
- Slow test identification (>30s threshold)
- Parallelization opportunities
- Setup/teardown overhead

---

### 🧹 Code Quality 🚧 (Coming Soon)

Evaluate test code maintainability using Clean Code principles.

**Planned checks:**
- Long methods (>50 lines)
- Large classes (>300 lines)
- Code duplication
- Magic numbers
- Deep nesting (>3 levels)
- Naming conventions

## 🚀 Getting Started

### Prerequisites

- [Claude Code](https://claude.ai/code) installed (CLI, Desktop, or Web)
- A test automation project to evaluate
- Project-specific CLI tools (dotnet, npm, pip, or mvn)

### Installation

**Option 1: Use as Standalone Tool (Recommended)**

Clone this repository to use as a central evaluation workspace:

```bash
git clone https://github.com/lovelynfnt/agentic-qa.git
cd agentic-qa
```

**Option 2: Copy into Your Project**

Copy the `.claude` directory into your test automation project:

```bash
cp -r agentic-qa/.claude /path/to/your-test-project/
cd /path/to/your-test-project
```

### Usage

**From Standalone Workspace:**
```bash
cd agentic-qa
claude
# Then ask Claude:
"Evaluate this project /path/to/your-automation-project"
```

**From Your Project:**
```bash
cd /path/to/your-test-project
claude
# Then ask Claude:
"Evaluate this project"
```

### Evaluation Flow

1. **High-Level Pre-Check** - Detects project type, test framework, test count
2. **Metric Selection** - Choose which metric to evaluate
3. **Metric-Specific Pre-Check** - Validates requirements for selected metric
4. **Run Evaluation** - Executes analysis and calculates score
5. **Generate Report** - Creates markdown report and JSON history

### Sample Output

```
🤖 Test Automation Evaluator v1.0
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Analyzing project structure...

✅ Detected: .NET project with SpecFlow
📊 Found: 42 test scenarios across 8 files

Ready to evaluate!

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📋 Protocol: High-Level Pre-Check | 📄 Step 1 of 5

[After selecting Dependency Health...]

## Dependency Health Evaluation Complete - Score: 85/100 🟡

### 📦 Package Analysis
- Total Packages: 42 packages (15 direct, 27 transitive)
- Outdated: 8 packages (2 major, 3 minor, 3 patch)
- Vulnerabilities: 1 security issue (High: 1)

### 🚨 Critical Issues (2 found)
1. Selenium.WebDriver is 1 major version behind
   - Current: 3.141.0 → Latest: 4.15.0
   - Fix: `dotnet add package Selenium.WebDriver --version 4.15.0`

2. CVE-2023-12345: High vulnerability in Newtonsoft.Json
   - Fix: Update to version 13.0.2 or higher

📊 Detailed report: .claude/evaluations/2026-05-28_091234_dependency-health.md
```

## 🏗️ Architecture

### Modular Design

```
.claude/
├── CLAUDE.md                              # Entry point (routes to evaluator)
├── personas/
│   └── evaluator/
│       ├── evaluator.md                   # Main orchestrator (Steps 1-2)
│       └── metrics/
│           ├── dependency-health.md       # Metric implementation
│           ├── dependency-health-scoring.json
│           ├── test-performance.md        # Coming soon
│           ├── test-performance-scoring.json
│           ├── code-quality.md            # Coming soon
│           └── code-quality-scoring.json
└── evaluations/                           # Generated reports
    └── history/                           # JSON data for trends
```

### Framework Support

Works with any test automation stack:
- ✅ **.NET** (SpecFlow, NUnit, xUnit, MSTest)
- ✅ **JavaScript/TypeScript** (Jest, Playwright, Cypress, Mocha)
- ✅ **Python** (pytest, unittest, Robot Framework)
- ✅ **Java** (JUnit, TestNG, Cucumber)

### Data Persistence

Evaluations are saved for trend analysis:
- **Markdown reports** - Human-readable, shareable (.md files)
- **JSON history** - Machine-readable for tracking trends (.json files)
- **Configurable scoring** - Adjust thresholds per metric

## 🎨 Use Cases

### 1. Sprint Health Checks
Run evaluations at the start of each sprint to identify technical debt and prioritize improvements.

### 2. Pre-Release Audits
Evaluate dependency security before major releases to ensure no critical vulnerabilities.

### 3. Onboarding New Team Members
Use evaluation reports to help new QA engineers understand project health and improvement areas.

### 4. Continuous Improvement Tracking
Regular evaluations show trends over time, helping teams measure the impact of quality initiatives.

### 5. Compliance & Security Audits
Generate reports showing dependency health and security posture for compliance reviews.

## 🛠️ Customization

### Adjust Scoring Thresholds

Edit `.claude/personas/evaluator/metrics/dependency-health-scoring.json`:

```json
{
  "criteria": {
    "outdated_major": {
      "threshold": 0,
      "severity": "critical",
      "points_deduction": 20
    },
    "security_vulnerabilities": {
      "points_deduction_per_vuln": {
        "critical": 25,
        "high": 15,
        "moderate": 8,
        "low": 3
      }
    }
  }
}
```

### Change Grade Thresholds

```json
{
  "grade_thresholds": {
    "A": 90,
    "B": 80,
    "C": 70,
    "D": 60,
    "F": 0
  }
}
```

### Add Custom Metrics

1. Create `metrics/my-metric.md` with evaluation instructions
2. Create `metrics/my-metric-scoring.json` with scoring rules
3. Update `evaluator.md` to include the new metric option

## 📖 Directory Structure

```
agentic-qa/
├── README.md                              # This file
├── .claude/
│   ├── CLAUDE.md                          # Entry point
│   ├── settings.json                      # Claude Code settings
│   ├── personas/
│   │   └── evaluator/
│   │       ├── evaluator.md               # Main persona
│   │       └── metrics/
│   │           ├── dependency-health.md
│   │           ├── dependency-health-scoring.json
│   │           ├── test-performance.md
│   │           ├── test-performance-scoring.json
│   │           ├── code-quality.md
│   │           └── code-quality-scoring.json
│   └── evaluations/                       # Generated reports
│       └── history/                       # JSON data
└── docs/                                  # Additional documentation (Coming Soon)
```

## 🤝 Contributing

We welcome contributions! Whether it's:
- ✨ New metrics (test coverage, flakiness detection, etc.)
- 📝 Documentation improvements
- 🎨 Scoring templates for different project types
- 💡 Feature suggestions
- 🐛 Bug reports

## 📊 Roadmap

### Version 1.0 (Current)
- ✅ Modular architecture with per-metric files
- ✅ Dependency Health metric (complete)
- ✅ Framework-agnostic detection (.NET, Node.js, Python, Java)
- ✅ Structured 5-step evaluation flow
- ✅ Historical tracking with JSON data
- ✅ Visual protocol indicators (banners, step tracking)

### Version 1.1 (Planned)
- 🚧 Test Performance metric
- 🚧 Code Quality metric
- 🚧 "All Metrics" evaluation option
- 🚧 Trend comparison (current vs previous)
- 🚧 Grade improvement suggestions

## 🙏 Acknowledgments

Built with [Claude Code](https://claude.ai/code) by Anthropic.

---

**Note:** Agentic QA requires Claude Code and uses natural language commands (not custom slash commands). Type phrases like "Evaluate this project [path]" to activate the evaluator.
