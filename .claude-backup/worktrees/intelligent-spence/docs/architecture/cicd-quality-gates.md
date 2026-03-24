# CI/CD Quality Gates Integration

## Overview

The CI/CD quality gates system integrates with GitHub Actions to automatically evaluate code quality, test coverage, and security metrics before allowing deployments. It uses the `ControlLoopReporter` to make intelligent deployment decisions based on configurable thresholds.

## Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                     GitHub Actions Workflow                      │
└───────────────────────────┬─────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────────┐
│                    Quality Gate Scripts                          │
│  ┌──────────────────┐              ┌──────────────────┐        │
│  │  quality-gate.ts │              │ quality-gate.sh  │        │
│  │  (TypeScript)    │              │ (Bash fallback)  │        │
│  └────────┬─────────┘              └────────┬─────────┘        │
└───────────┼──────────────────────────────────┼──────────────────┘
            │                                  │
            ▼                                  ▼
┌─────────────────────────────────────────────────────────────────┐
│                    ControlLoopReporter                           │
│  • Evaluates test results                                       │
│  • Checks coverage thresholds                                   │
│  • Analyzes security vulnerabilities                            │
│  • Generates deployment decision                                │
└───────────────────────────┬─────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────────┐
│                     Deployment Decision                          │
│  ┌────────────────┐              ┌────────────────┐            │
│  │   canDeploy    │              │   Violations   │            │
│  │  (true/false)  │              │   & Actions    │            │
│  └────────────────┘              └────────────────┘            │
└─────────────────────────────────────────────────────────────────┘
```

## Components

### 1. Quality Gate Scripts

#### TypeScript Script (`scripts/quality-gate.ts`)

- **Purpose**: Node.js-based quality gate evaluation using `ControlLoopReporter`
- **Features**:
  - Integrates directly with reporting system
  - Extracts coverage and test results
  - Generates PR comments
  - Produces machine-readable feedback

**Usage**:
```bash
npx ts-node scripts/quality-gate.ts \
  --min-coverage 80 \
  --min-pass-rate 0.95 \
  --max-critical-vulns 0 \
  --output-dir ./quality-reports \
  --pr-comment
```

#### Bash Script (`scripts/quality-gate.sh`)

- **Purpose**: Fallback shell script for environments without Node.js
- **Features**:
  - Parses Jest output
  - Evaluates coverage JSON
  - Simple threshold checking
  - Generates reports

**Usage**:
```bash
bash scripts/quality-gate.sh \
  --min-coverage 80 \
  --min-pass-rate 0.95
```

### 2. GitHub Actions Workflow

**File**: `.github/workflows/quality-gate.yml`

**Key Steps**:
1. Run unit tests with coverage
2. Run integration tests
3. Evaluate quality gates using `ControlLoopReporter`
4. Post results to PR (if applicable)
5. Upload quality reports as artifacts
6. Block deployment if gates fail

**Triggers**:
- Push to `main` or `develop` branches
- Pull requests to `main` or `develop`
- Manual workflow dispatch with custom thresholds

### 3. Reusable Action

**File**: `.github/actions/quality-gate/action.yml`

A reusable composite action that can be used in any workflow:

```yaml
- name: Evaluate Quality Gates
  uses: ./.github/actions/quality-gate
  with:
    min-coverage: '80'
    min-pass-rate: '0.95'
    max-critical-vulns: '0'
    max-high-vulns: '2'
    fail-on-violation: 'true'
    post-pr-comment: 'true'
```

**Outputs**:
- `can-deploy`: Whether deployment is approved
- `quality-score`: Overall quality score (0-100)
- `violations-count`: Number of violations
- `test-pass-rate`: Test pass rate
- `coverage-percentage`: Coverage percentage

## Quality Thresholds

### Default Thresholds

| Metric | Threshold | Severity |
|--------|-----------|----------|
| Test Pass Rate | ≥ 95% | Critical |
| Code Coverage | ≥ 80% | High |
| Critical Vulnerabilities | 0 | Critical |
| High Vulnerabilities | ≤ 2 | High |
| Quality Score | ≥ 70 | High |

### Customizing Thresholds

Thresholds can be customized via:

1. **Workflow inputs** (manual dispatch):
```yaml
workflow_dispatch:
  inputs:
    min_coverage:
      default: '80'
    min_pass_rate:
      default: '0.95'
```

2. **Environment variables**:
```bash
export MIN_COVERAGE=85
export MIN_PASS_RATE=0.98
```

3. **Script arguments**:
```bash
npx ts-node scripts/quality-gate.ts --min-coverage 85
```

## Integration with ControlLoopReporter

The quality gate scripts use `ControlLoopReporter` to generate structured feedback:

```typescript
import { ControlLoopReporter, ControlLoopConfig } from '../src/reporting';

const config: ControlLoopConfig = {
  minPassRate: 0.95,
  minCoverage: 80,
  maxCriticalVulnerabilities: 0,
  maxHighVulnerabilities: 2
};

const reporter = new ControlLoopReporter(config);
const output = reporter.report(aggregatedResults);
const feedback: ControlLoopFeedback = JSON.parse(output.content);

// Deployment decision
if (feedback.signals.canDeploy) {
  // Approve deployment
  process.exit(0);
} else {
  // Block deployment
  console.log('Violations:', feedback.violations);
  process.exit(1);
}
```

### ControlLoopFeedback Structure

```json
{
  "executionId": "github-run-123456",
  "timestamp": "2025-11-29T12:00:00Z",
  "status": "failure",
  "success": false,
  "qualityScore": 75,
  "metrics": {
    "testPassRate": 0.92,
    "coveragePercentage": 78.5,
    "securityScore": 100,
    "performanceScore": 95
  },
  "signals": {
    "canDeploy": false,
    "criticalIssuesFound": true,
    "coverageDecreased": true,
    "performanceDegraded": false,
    "securityRisks": false,
    "testsUnstable": false
  },
  "actions": [
    {
      "type": "block_deployment",
      "priority": "critical",
      "reason": "Quality gates not met",
      "resolution": "Fix failing tests and improve coverage"
    }
  ],
  "violations": [
    {
      "metric": "testPassRate",
      "threshold": 0.95,
      "actualValue": 0.92,
      "operator": "gte",
      "severity": "critical",
      "impact": "Deployment blocked due to test failures"
    },
    {
      "metric": "coverage",
      "threshold": 80,
      "actualValue": 78.5,
      "operator": "gte",
      "severity": "high",
      "impact": "Insufficient test coverage"
    }
  ],
  "nextSteps": [
    "✗ Quality gates failed - deployment blocked",
    "→ Address 2 critical violation(s) immediately",
    "→ Fix violations and re-run quality checks"
  ]
}
```

## Integration with Alerting System

Quality gate results integrate with the Prometheus alerting system:

### Prometheus Metrics (from alerting-rules.yml)

```yaml
# Quality Gate Failure Alert
- alert: QualityGateFailed
  expr: aqe_quality_gate_pass_rate < 1.0
  for: 1m
  labels:
    severity: error
    component: quality
    alert_type: quality_gate
    feedback_action: adjust_strategy
  annotations:
    summary: "Quality gate evaluation failed"
    description: "Quality gate pass rate: {{ $value }}"
    feedback_strategy: "incremental_improvement"
```

### Coverage Drop Alert

```yaml
- alert: CriticalCoverageDrop
  expr: aqe_quality_coverage_line < 80
  for: 1m
  labels:
    severity: critical
    component: quality
    alert_type: coverage_drop
    feedback_action: auto_remediate
  annotations:
    summary: "Code coverage dropped below 80%"
    feedback_action: "generate_additional_tests"
```

## Usage Examples

### Example 1: Basic Quality Gate

```yaml
name: CI Pipeline

on: [push, pull_request]

jobs:
  quality-check:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'

      - run: npm ci
      - run: npm run build
      - run: npm run test:unit
      - run: npm run test:integration

      - name: Quality Gate
        uses: ./.github/actions/quality-gate
        with:
          min-coverage: '80'
          min-pass-rate: '0.95'
```

### Example 2: Custom Thresholds

```yaml
- name: Strict Quality Gate
  uses: ./.github/actions/quality-gate
  with:
    min-coverage: '90'
    min-pass-rate: '0.98'
    max-critical-vulns: '0'
    max-high-vulns: '0'
    fail-on-violation: 'true'
```

### Example 3: Conditional Deployment

```yaml
jobs:
  quality-gate:
    runs-on: ubuntu-latest
    outputs:
      can-deploy: ${{ steps.gate.outputs.can-deploy }}
    steps:
      - name: Evaluate Gates
        id: gate
        uses: ./.github/actions/quality-gate

  deploy:
    needs: quality-gate
    if: needs.quality-gate.outputs.can-deploy == 'true'
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to Production
        run: ./deploy.sh
```

## PR Comment Example

When quality gates fail, a comment is posted to the PR:

```markdown
## ❌ Quality Gate: FAILED

### 📊 Metrics

| Metric | Value | Threshold | Status |
|--------|-------|-----------|--------|
| Test Pass Rate | 92.0% | 95.0% | ❌ |
| Coverage | 78.5% | 80% | ❌ |
| Quality Score | 75/100 | 70/100 | ✅ |

### ⚠️ Violations

- **testPassRate**: 0.92 < 0.95 (critical)
  - Deployment blocked due to test failures
- **coverage**: 78.5 < 80 (high)
  - Insufficient test coverage may lead to undetected bugs

### 🎯 Required Actions

🔴 **block_deployment** (critical)
- Quality gates not met - deployment blocked
- Resolution: Fix failing tests, address security vulnerabilities, and improve coverage

### 🚀 Deployment Decision

❌ **BLOCKED** - Quality gates failed. Fix violations before deployment.
```

## Outputs

### Control Loop Feedback JSON

**Location**: `quality-reports/control-loop-feedback.json`

Machine-readable deployment decision with full metrics and violations.

### Human-Readable Report

**Location**: `quality-reports/quality-report.txt`

Text-based summary for developers.

### PR Comment

**Location**: `quality-reports/pr-comment.md`

Markdown-formatted comment for GitHub PRs.

## Exit Codes

| Code | Meaning |
|------|---------|
| 0 | Quality gates passed - deployment approved |
| 1 | Quality gates failed - deployment blocked |
| 2 | Script execution error |

## Best Practices

1. **Set realistic thresholds** - Start conservative and tighten over time
2. **Monitor trends** - Track metrics over time, not just absolute values
3. **Fail fast** - Block deployment early if critical issues found
4. **Provide context** - Include actionable remediation steps
5. **Automate fixes** - Use feedback to trigger automated improvements
6. **Review regularly** - Adjust thresholds based on project maturity

## Troubleshooting

### No coverage data found

**Symptom**: Coverage shows 0%

**Solutions**:
- Ensure tests run before quality gate
- Check `coverage/coverage-summary.json` exists
- Verify Jest coverage configuration

### Quality gate always passes/fails

**Symptom**: Incorrect deployment decisions

**Solutions**:
- Check threshold values
- Verify test result parsing
- Review `control-loop-feedback.json` for details

### PR comment not posted

**Symptom**: No comment appears on PR

**Solutions**:
- Verify `pull-requests: write` permission
- Check GitHub token has correct scopes
- Review workflow logs for errors

## Related Documentation

- [ControlLoopReporter API](/workspaces/agentic-qe-cf/src/reporting/reporters/ControlLoopReporter.ts)
- [Alerting Rules](/workspaces/agentic-qe-cf/config/alerting-rules.yml)
- [Phase 4 Implementation Plan](/workspaces/agentic-qe-cf/docs/implementation-plans/phase4-alerting-implementation-plan.md)
