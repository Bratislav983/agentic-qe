# 🚀 AQE Fleet - Complete Practical Guide

## What is the AQE Fleet?

The **Agentic QE (Quality Engineering) Fleet** is a distributed swarm of **47 specialized AI agents** that automate comprehensive software testing and quality assurance. Think of it as having an entire QE team at your fingertips.

---

## 📊 Fleet Composition

### 21 Main QE Agents
Core quality engineering specialists for all aspects of testing

### 15 n8n Workflow Agents
Specialized agents for testing n8n automation workflows

### 11 Subagents
TDD specialists, code reviewers, and focused execution agents

### 46 Skills
Reusable quality engineering methodologies and patterns

---

## 🎯 13 Quality Domains

The fleet is organized into specialized domains:

### 1. **test-generation**
Generate tests automatically using AI pattern recognition
- Unit tests, integration tests, E2E tests
- Multi-framework support (Jest, Vitest, Playwright, Cypress)
- Edge case discovery

### 2. **test-execution**
Execute tests with parallel processing and intelligent retry
- Multi-framework execution
- Real-time progress tracking
- Flaky test handling

### 3. **coverage-analysis**
Find coverage gaps with O(log n) sublinear algorithms
- Incremental coverage analysis
- Critical path identification
- Risk-scored untested code

### 4. **quality-assessment**
Comprehensive quality metrics and deployment readiness
- Multi-factor risk assessment
- Trend analysis and predictions
- Go/no-go deployment decisions

### 5. **defect-intelligence**
AI-powered defect prediction and root cause analysis
- Pattern learning from past defects
- Predictive defect detection
- Root cause investigation

### 6. **requirements-validation**
Ensure complete requirements coverage
- Requirements traceability
- BDD scenario generation
- Acceptance criteria validation

### 7. **code-intelligence**
Knowledge graph-based code understanding (80% token reduction)
- Semantic code search
- AST analysis
- Architecture visualization

### 8. **security-compliance**
Security scanning and compliance validation
- OWASP vulnerability detection
- SAST/DAST scanning
- GDPR/SOC2/HIPAA compliance

### 9. **contract-testing**
API contract validation and breaking change detection
- Consumer-driven contracts
- Schema validation
- Backward compatibility testing

### 10. **visual-accessibility**
Visual regression and WCAG 2.2 accessibility compliance
- AI-powered screenshot comparison
- Screen reader validation
- Copy-paste ready fixes

### 11. **chaos-resilience**
Chaos engineering and resilience testing
- Controlled fault injection
- System recovery validation
- Resilience scoring

### 12. **learning-optimization**
Transfer learning and continuous improvement
- Pattern optimization
- Success/failure learning
- Strategy adaptation

### 13. **coordination**
Multi-agent orchestration and fleet management
- Task distribution
- Work stealing algorithms
- Agent health monitoring

---

## 💡 What Can You Do?

### 🧪 Test Generation

**Generate tests for your entire project:**
```bash
aqe test generate src/
```

**Generate specific test types:**
```bash
aqe test generate src/auth/ --type unit --framework jest
aqe test generate src/api/ --type integration --framework vitest
aqe test generate e2e/ --type e2e --framework playwright
```

**From code (using Task tool in Claude Code):**
```javascript
Task("Generate unit tests",
     "Create comprehensive Jest tests for UserService with edge cases",
     "qe-test-generator")
```

---

### 📈 Coverage Analysis

**Find coverage gaps:**
```bash
aqe coverage analyze
aqe coverage analyze src/services/
```

**With risk scoring:**
```bash
aqe coverage analyze --risk-threshold high
```

**From code:**
```javascript
Task("Analyze coverage gaps",
     "Find untested critical paths using O(log n) algorithms",
     "qe-coverage-analyzer")
```

---

### 🎯 Quality Assessment

**Run comprehensive quality checks:**
```bash
aqe quality assess
aqe quality assess --threshold 85
```

**Deployment readiness:**
```bash
aqe quality gate --target production
```

**From code:**
```javascript
Task("Quality gate check",
     "Assess deployment readiness for production release",
     "qe-quality-gate")
```

---

### 🔒 Security Scanning

**Scan for vulnerabilities:**
```bash
aqe security scan
aqe security scan --compliance gdpr,soc2
```

**From code:**
```javascript
Task("Security audit",
     "OWASP vulnerability scan with GDPR compliance check",
     "qe-security-scanner")
```

---

### 🧠 Code Intelligence

**Index your codebase for semantic search:**
```bash
aqe code index
aqe code query "authentication flow"
```

**Knowledge graph analysis:**
```bash
aqe code analyze --graph
```

**From code:**
```javascript
Task("Code intelligence",
     "Build knowledge graph and analyze architecture patterns",
     "qe-code-intelligence")
```

---

### 🎪 Fleet Operations

**Initialize the fleet:**
```bash
aqe fleet init
```

**Spawn multiple agents:**
```bash
aqe fleet spawn --count 5 --type test-generation
```

**Run coordinated operations:**
```bash
aqe fleet run full-test-suite
aqe fleet run security-audit
aqe fleet run quality-assessment
```

**Check fleet status:**
```bash
aqe fleet status
```

---

### 🔄 Multi-Agent Workflows

**Full QE Pipeline:**
```javascript
// Spawn coordinated fleet for complete QE workflow
[Single Message - Parallel Execution]:
  Task("Test Generator", "Generate tests for all modules", "qe-test-generator")
  Task("Coverage Analyzer", "Find coverage gaps", "qe-coverage-analyzer")
  Task("Security Scanner", "Run OWASP audit", "qe-security-scanner")
  Task("Performance Tester", "Load test with k6", "qe-performance-tester")
  Task("Quality Gate", "Assess deployment readiness", "qe-quality-gate")
```

**TDD Workflow with Subagents:**
```javascript
// RED-GREEN-REFACTOR cycle
Task("Write failing test", "Create test for new feature", "qe-test-writer")
Task("Implement code", "Make tests pass", "qe-test-implementer")
Task("Refactor code", "Improve while keeping tests green", "qe-test-refactorer")
```

---

## 🛠️ Practical Examples

### Example 1: Complete New Feature QE

```bash
# 1. Generate tests for new feature
aqe test generate src/features/payment/

# 2. Run tests and collect coverage
aqe test execute src/features/payment/ --coverage

# 3. Analyze coverage gaps
aqe coverage analyze src/features/payment/

# 4. Security scan
aqe security scan src/features/payment/

# 5. Quality gate
aqe quality gate --target staging
```

### Example 2: Pre-Production Validation

```bash
# Initialize fleet for comprehensive validation
aqe fleet init

# Run full validation suite
aqe fleet run full-validation

# Check results
aqe fleet status
aqe quality assess --verbose
```

### Example 3: Continuous Quality Monitoring

```bash
# Set up learning system
aqe hooks install

# Enable pattern learning
aqe hooks configure --learn-from-failures

# Check learned patterns
aqe hooks list-patterns
```

### Example 4: API Contract Testing

```javascript
Task("API Contract Validation",
     "Validate REST API contracts and detect breaking changes",
     "qe-api-contract-validator")
```

### Example 5: Accessibility Compliance

```javascript
Task("WCAG 2.2 Audit",
     "Check accessibility compliance and generate fixes",
     "qe-a11y-ally")
```

### Example 6: Chaos Engineering

```javascript
Task("Resilience Testing",
     "Inject faults and validate system recovery",
     "qe-chaos-engineer")
```

---

## 🎓 Advanced Features

### Knowledge Graph Code Intelligence

**80% token reduction through semantic search:**
```bash
# Index your codebase
aqe code index --project .

# Query with natural language
aqe code query "find authentication middleware"
aqe code query "list all API endpoints"
aqe code query "show error handling patterns"
```

### Sublinear Optimization

**O(log n) algorithms for massive codebases:**
- Coverage gap detection doesn't scan every line
- Test selection uses intelligent sampling
- Risk scoring uses critical path analysis

### Fleet Commander

**Orchestrate 50+ agents simultaneously:**
```bash
aqe fleet spawn --type mixed --count 50
```

The `qe-fleet-commander` agent manages:
- Dynamic task distribution
- Work stealing for load balancing
- Agent health monitoring
- Topology optimization

### Transfer Learning

**The fleet learns from every execution:**
- Pattern storage in `.agentic-qe/memory.db`
- Success/failure pattern recognition
- Strategy optimization over time
- Cross-project knowledge sharing

---

## 📊 Monitoring & Observability

### Real-Time Status

```bash
aqe status          # Overall system status
aqe health          # Health check with diagnostics
aqe fleet status    # Fleet-specific metrics
```

**What you see:**
- Active agents and utilization
- Task completion rates
- Domain health status
- Performance metrics
- Critical issues/warnings

### Token Usage Tracking

```bash
aqe token-usage              # View token consumption
aqe token-usage --detailed   # Detailed breakdown
```

### Learning Progress

```bash
aqe hooks list-patterns      # View learned patterns
aqe domain status test-generation  # Domain-specific learning
```

---

## 🔧 Configuration

### Initialize with Custom Settings

```bash
aqe init --auto                    # Auto-detect everything
aqe init --framework jest          # Specify framework
aqe init --coverage-threshold 90   # Set coverage target
```

### Domain Configuration

```bash
aqe domain configure test-generation --parallel 10
aqe domain configure security-compliance --strict-mode
```

---

## 💾 Persistent Memory

All learning and patterns are stored in:
```
.agentic-qe/
  ├── memory.db          # SQLite database with HNSW vector index
  ├── patterns/          # Learned test patterns
  ├── knowledge-graph/   # Code intelligence graph
  └── metrics/           # Historical metrics
```

**Backup your learnings:**
```bash
npm run backup                    # Create backup
npm run backup:list               # List backups
npm run backup:restore            # Restore from backup
```

---

## 🎯 Key Capabilities Summary

| Capability | Agent(s) | Use Case |
|------------|----------|----------|
| **Test Generation** | qe-test-generator | Auto-generate comprehensive tests |
| **Coverage Analysis** | qe-coverage-analyzer | Find untested code with O(log n) |
| **Security Scanning** | qe-security-scanner | OWASP + compliance validation |
| **Performance Testing** | qe-performance-tester | Load/stress testing with k6 |
| **Visual Testing** | qe-visual-tester | Screenshot comparison |
| **Accessibility** | qe-a11y-ally | WCAG 2.2 compliance + fixes |
| **API Contracts** | qe-api-contract-validator | Breaking change detection |
| **Code Understanding** | qe-code-intelligence | 80% token reduction via KG |
| **Flaky Tests** | qe-flaky-test-hunter | Auto-detection + remediation |
| **Chaos Testing** | qe-chaos-engineer | Resilience validation |
| **Fleet Orchestration** | qe-fleet-commander | 50+ agent coordination |
| **Quality Gates** | qe-deployment-readiness | Go/no-go decisions |
| **Requirements** | qe-requirements-validator | Traceability + BDD |
| **Defect Prediction** | qe-defect-intelligence | AI-powered prediction |
| **Test Data** | qe-test-data-architect | 10k+ records/sec generation |

---

## 🚀 Next Steps

1. **Try basic test generation:**
   ```bash
   aqe test generate src/
   ```

2. **Explore domains:**
   ```bash
   aqe domain list
   ```

3. **Run quality assessment:**
   ```bash
   aqe quality assess
   ```

4. **Check what the fleet learned:**
   ```bash
   aqe hooks list-patterns
   ```

5. **Spawn your first fleet operation:**
   ```bash
   aqe fleet run full-test-suite
   ```

---

## 📚 Additional Resources

- **Agent Reference:** [docs/reference/agents.md](./reference/agents.md)
- **Skills Reference:** [docs/reference/skills.md](./reference/skills.md)
- **Usage Guide:** [docs/reference/usage.md](./reference/usage.md)
- **MCP Setup:** [docs/mcp-setup.md](./mcp-setup.md)

---

**Ready to start?** Pick a command above and try it! The fleet is at your service. 🎯
