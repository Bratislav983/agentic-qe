# 🚀 AQE Fleet - 5-Minute Quick Start

## What is AQE Fleet?

**47 AI agents** that automate your entire quality engineering workflow - from test generation to deployment validation.

---

## ⚡ Quick Commands

### 1️⃣ Generate Tests (10 seconds)
```bash
aqe test generate src/
```
**What it does:** AI generates comprehensive tests for your code

---

### 2️⃣ Find Coverage Gaps (15 seconds)
```bash
aqe coverage analyze
```
**What it does:** O(log n) algorithm finds untested code

---

### 3️⃣ Security Scan (30 seconds)
```bash
aqe security scan
```
**What it does:** OWASP vulnerability detection + compliance check

---

### 4️⃣ Quality Gate (20 seconds)
```bash
aqe quality assess
```
**What it does:** Multi-factor quality assessment → Go/No-Go decision

---

### 5️⃣ Full Fleet Operation (2-5 minutes)
```bash
aqe fleet run full-validation
```
**What it does:** Coordinates multiple agents for complete QE pipeline

---

## 📊 Check Status Anytime

```bash
aqe status          # System overview
aqe health          # Detailed diagnostics
aqe fleet status    # Fleet metrics
```

---

## 🎯 The 13 Domains

1. **test-generation** - Auto-generate tests
2. **test-execution** - Run tests in parallel
3. **coverage-analysis** - Find gaps with AI
4. **quality-assessment** - Deployment readiness
5. **defect-intelligence** - Predict bugs
6. **requirements-validation** - Ensure completeness
7. **code-intelligence** - Understand codebase (80% token reduction)
8. **security-compliance** - OWASP + GDPR/SOC2/HIPAA
9. **contract-testing** - API validation
10. **visual-accessibility** - WCAG 2.2 compliance
11. **chaos-resilience** - Chaos engineering
12. **learning-optimization** - Pattern learning
13. **coordination** - Multi-agent orchestration

---

## 🧪 Try Your First Agent

### Using Command Line:
```bash
aqe test generate src/components/
```

### Using Claude Code Task Tool:
```javascript
Task("Generate comprehensive tests",
     "Create Jest tests for UserService with edge cases and mocks",
     "qe-test-generator")
```

---

## 💡 Common Workflows

### New Feature QE (5 commands)
```bash
aqe test generate src/features/new-feature/
aqe test execute src/features/new-feature/
aqe coverage analyze src/features/new-feature/
aqe security scan src/features/new-feature/
aqe quality gate
```

### Pre-Production Validation (1 command)
```bash
aqe fleet run full-validation
```

### Continuous Learning (1 command)
```bash
aqe hooks install
```

---

## 🎓 Key Features

- **47 Specialized Agents** - Each expert in specific QE tasks
- **13 Quality Domains** - Comprehensive coverage
- **Sublinear Algorithms** - O(log n) for massive codebases
- **Transfer Learning** - Improves with every run
- **Multi-Framework Support** - Jest, Vitest, Playwright, Cypress, etc.
- **Knowledge Graph** - 80% token reduction for code understanding
- **MCP Integration** - Works with Claude Code/Desktop

---

## 📚 Learn More

- **Complete Guide:** [AQE-FLEET-GUIDE.md](./AQE-FLEET-GUIDE.md)
- **Agent Reference:** [reference/agents.md](./reference/agents.md)
- **Skills Reference:** [reference/skills.md](./reference/skills.md)

---

## 🆘 Need Help?

```bash
aqe --help
aqe test --help
aqe fleet --help
aqe [command] --help
```

---

**Your QE team of 47 AI agents is ready! 🎯**
