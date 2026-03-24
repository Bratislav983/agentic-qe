# Claude Code Configuration - Agentic QE Fleet

## ⚠️ CRITICAL POLICIES

### Integrity Rule (ABSOLUTE)
- ❌ NO shortcuts - do the work properly or don't do it
- ❌ NO fake data - use real data, real tests, real results
- ❌ NO false claims - only report what actually works and is verified
- ✅ ALWAYS implement all code/tests with proper implementation
- ✅ ALWAYS verify before claiming success
- ✅ ALWAYS use real database queries, not mocks, for integration tests
- ✅ ALWAYS run actual tests, not assume they pass

**We value the quality we deliver to our users.**

### Git Operations
- ❌ NEVER auto-commit/push without explicit user request
- ✅ ALWAYS wait for: "commit this" or "push to main"
- 📋 **Full policy:** [docs/policies/git-operations.md](docs/policies/git-operations.md)

### Release Verification
- ❌ NEVER release without `aqe init` verification
- ✅ ALWAYS test at least one agent with real database queries
- 📋 **Full checklist:** [docs/policies/release-verification.md](docs/policies/release-verification.md)

### Test Execution
- ❌ NEVER run `npm test` (OOM risk in DevPod/Codespaces)
- ✅ ALWAYS use batched scripts: `npm run test:unit`, `npm run test:integration`
- 📋 **Full policy:** [docs/policies/test-execution.md](docs/policies/test-execution.md)

### File Organization
- ❌ NEVER save working files to root folder
- ✅ ALWAYS use: `/docs`, `/tests`, `/src`, `/scripts`, `/examples`

### Data Protection (CRITICAL - Added after 2025-12-29 incident)
- ❌ NEVER run `rm -f` on `.agentic-qe/` directory or `*.db` files without explicit user confirmation
- ❌ NEVER delete database files during test debugging
- ✅ ALWAYS run `npm run backup` before any database operations
- ✅ ALWAYS ask user before running destructive commands on data directories
- 📋 **Incident report:** [docs/incidents/2025-12-29-memory-db-deletion.md](docs/incidents/2025-12-29-memory-db-deletion.md)

**Backup Commands:**
```bash
npm run backup          # Create backup before risky operations
npm run backup:list     # List available backups
npm run backup:restore  # Restore from backup
```

### Release Process
- ❌ NEVER commit directly to main - use feature branches with PRs
- ❌ NEVER forget package-lock.json when updating versions
- ✅ ALWAYS use `mcp__agentic-qe__memory_store` with `persist: true` for learnings (NOT `claude-flow`)

**Version Update Checklist** (all files to update):
1. `package.json` - version field
2. `package-lock.json` - run `npm install --package-lock-only`
3. `README.md` - Version badge (line 12)
4. `CHANGELOG.md` - Add new version section with changes
5. `src/mcp/server-instructions.ts` - `SERVER_VERSION` constant
6. `src/core/memory/HNSWVectorMemory.ts` - version in `getImplementationInfo()`

**Skip**: `.claude/agents/sparc/specification.md` - `FR-X.X.X` are feature requirement IDs, NOT versions

**Release Workflow**:
```bash
git checkout -b release/vX.X.X    # 1. Create branch
# Update all version files above    # 2. Update versions
npm run test:fast                   # 3. Run tests
git commit -m "chore(release): bump version to vX.X.X"
git push -u origin release/vX.X.X  # 4. Push branch
gh pr create                        # 5. Create PR to main
# Wait for CI and review            # 6. Review
# Merge PR                          # 7. Merge
git tag vX.X.X && git push origin vX.X.X  # 8. Tag
gh release create vX.X.X            # 9. GitHub release
monitor gh workflow triggered on release publish and verify npm published succesfully
```

**Post-Release: Code Intelligence Indexing**:
```bash
# After npm publish succeeds, index code changes for semantic search
# Requires: Ollama running + PostgreSQL (agentic-qe-ruvector-dev container)
docker start agentic-qe-ruvector-dev 2>/dev/null || echo "Start PostgreSQL first"
aqe kg index --git-since vPREVIOUS_VERSION  # Index changes since last release
aqe kg stats                                  # Verify indexing succeeded
```

---

## 🤖 Agentic QE Fleet Quick Reference

**21 QE Agents:** Test generation, coverage analysis, performance, security, flaky detection, QX analysis, accessibility, code intelligence
**15 n8n Workflow Agents:** Workflow execution, chaos testing, compliance, security, performance *(contributed by [@fndlalit](https://github.com/fndlalit))*
**11 QE Subagents:** TDD specialists, code reviewers, integration testers
**46 QE Skills:** agentic-quality-engineering, tdd-london-chicago, api-testing-patterns, six-thinking-hats, brutal-honesty-review, sherlock-review, cicd-pipeline-qe-orchestrator, accessibility-testing, shift-left-testing, n8n-workflow-testing, **testability-scoring** *(contributed by [@fndlalit](https://github.com/fndlalit))*
**8 Slash Commands:** `/aqe-execute`, `/aqe-generate`, `/aqe-coverage`, `/aqe-quality`

### 📚 Complete Documentation

- **[Agent Reference](docs/reference/agents.md)** - All 21 main QE agents + 15 n8n agents + 11 subagents with capabilities and usage
- **[Code Intelligence Guide](docs/guides/code-intelligence-quickstart.md)** - Setup and usage for knowledge graph code understanding
- **[Skills Reference](docs/reference/skills.md)** - All 46 QE skills organized by category
- **[Usage Guide](docs/reference/usage.md)** - Complete usage examples and workflows

### 🎯 Quick Start

**Spawn agents:**
```javascript
Task("Generate tests", "Create test suite for UserService", "qe-test-generator")
Task("Analyze coverage", "Find gaps using O(log n)", "qe-coverage-analyzer")
```

**Check learning status:**
```bash
aqe learn status --agent test-gen
aqe patterns list --framework jest
```

### 💡 Key Principles
- Use Task tool for agent execution (not just MCP)
- Batch all operations in single messages (TodoWrite, file ops, etc.)
- Test with actual databases, not mocks
- Document only what actually works

---

# Claude Flow Integration (Preserved from original CLAUDE.md)

## 🚨 CRITICAL: CONCURRENT EXECUTION & FILE MANAGEMENT

**ABSOLUTE RULES**:
1. ALL operations MUST be concurrent/parallel in a single message
2. **NEVER save working files, text/mds and tests to the root folder**
3. ALWAYS organize files in appropriate subdirectories
4. **USE CLAUDE CODE'S TASK TOOL** for spawning agents concurrently, not just MCP

### ⚡ GOLDEN RULE: "1 MESSAGE = ALL RELATED OPERATIONS"

**MANDATORY PATTERNS:**
- **TodoWrite**: ALWAYS batch ALL todos in ONE call (5-10+ todos minimum)
- **Task tool (Claude Code)**: ALWAYS spawn ALL agents in ONE message with full instructions
- **File operations**: ALWAYS batch ALL reads/writes/edits in ONE message
- **Bash commands**: ALWAYS batch ALL terminal operations in ONE message
- **Memory operations**: ALWAYS batch ALL memory store/retrieve in ONE message

### 🎯 CRITICAL: Claude Code Task Tool for Claude Flow Agent Execution

**Claude Code's Task tool is the PRIMARY way to spawn Claude Flow agents:**
```javascript
// ✅ CORRECT: Use Claude Code's Task tool for parallel agent execution
[Single Message]:
  Task("Research agent", "Analyze requirements and patterns...", "researcher")
  Task("Coder agent", "Implement core features...", "coder")
  Task("Tester agent", "Create comprehensive tests...", "tester")
  Task("Reviewer agent", "Review code quality...", "reviewer")
  Task("Architect agent", "Design system architecture...", "system-architect")
```

**Claude Flow MCP tools are ONLY for coordination setup:**
- `mcp__claude-flow__swarm_init` - Initialize coordination topology
- `mcp__claude-flow__agent_spawn` - Define agent types for coordination
- `mcp__claude-flow__task_orchestrate` - Orchestrate high-level workflows

### 📁 File Organization Rules

**NEVER save to root folder. Use these directories:**
- `/src` - Source code files
- `/tests` - Test files
- `/docs` - Documentation and markdown files
- `/config` - Configuration files
- `/scripts` - Utility scripts
- `/examples` - Example code

## Project Overview

This project uses SPARC (Specification, Pseudocode, Architecture, Refinement, Completion) methodology with Claude-Flow orchestration for systematic Test-Driven Development.

## SPARC Commands

### Core Commands
- `npx claude-flow sparc modes` - List available modes
- `npx claude-flow sparc run <mode> "<task>"` - Execute specific mode
- `npx claude-flow sparc tdd "<feature>"` - Run complete TDD workflow
- `npx claude-flow sparc info <mode>` - Get mode details

### Batchtools Commands
- `npx claude-flow sparc batch <modes> "<task>"` - Parallel execution
- `npx claude-flow sparc pipeline "<task>"` - Full pipeline processing
- `npx claude-flow sparc concurrent <mode> "<tasks-file>"` - Multi-task processing

### Build Commands
- `npm run build` - Build project
- `npm run test` - Run tests
- `npm run lint` - Linting
- `npm run typecheck` - Type checking

## SPARC Workflow Phases

1. **Specification** - Requirements analysis (`sparc run spec-pseudocode`)
2. **Pseudocode** - Algorithm design (`sparc run spec-pseudocode`)
3. **Architecture** - System design (`sparc run architect`)
4. **Refinement** - TDD implementation (`sparc tdd`)
5. **Completion** - Integration (`sparc run integration`)

## Code Style & Best Practices

- **Modular Design**: Files under 500 lines
- **Environment Safety**: Never hardcode secrets
- **Test-First**: Write tests before implementation
- **Clean Architecture**: Separate concerns
- **Documentation**: Keep updated

## 🚀 Claude Flow Available Agents (54 Total)

### Core Development
`coder`, `reviewer`, `tester`, `planner`, `researcher`

### Swarm Coordination
`hierarchical-coordinator`, `mesh-coordinator`, `adaptive-coordinator`, `collective-intelligence-coordinator`, `swarm-memory-manager`

### Consensus & Distributed
`byzantine-coordinator`, `raft-manager`, `gossip-coordinator`, `consensus-builder`, `crdt-synchronizer`, `quorum-manager`, `security-manager`

### Performance & Optimization
`perf-analyzer`, `performance-benchmarker`, `task-orchestrator`, `memory-coordinator`, `smart-agent`

### GitHub & Repository
`github-modes`, `pr-manager`, `code-review-swarm`, `issue-tracker`, `release-manager`, `workflow-automation`, `project-board-sync`, `repo-architect`, `multi-repo-swarm`

### SPARC Methodology
`sparc-coord`, `sparc-coder`, `specification`, `pseudocode`, `architecture`, `refinement`

### Specialized Development
`backend-dev`, `mobile-dev`, `ml-developer`, `cicd-engineer`, `api-docs`, `system-architect`, `code-analyzer`, `base-template-generator`

### Claude Flow Testing & Validation
`tdd-london-swarm`, `production-validator`

### Migration & Planning
`migration-planner`, `swarm-init`

## 🎯 Claude Code vs MCP Tools

### Claude Code Handles ALL EXECUTION:
- **Task tool**: Spawn and run agents concurrently for actual work
- File operations (Read, Write, Edit, MultiEdit, Glob, Grep)
- Code generation and programming
- Bash commands and system operations
- Implementation work
- Project navigation and analysis
- TodoWrite and task management
- Git operations
- Package management
- Testing and debugging

### MCP Tools ONLY COORDINATE:
- Swarm initialization (topology setup)
- Agent type definitions (coordination patterns)
- Task orchestration (high-level planning)
- Memory management
- Neural features
- Performance tracking
- GitHub integration

**KEY**: MCP coordinates the strategy, Claude Code's Task tool executes with real agents.

## 🚀 Quick Setup

```bash
# Add MCP servers (Claude Flow required, others optional)
claude mcp add claude-flow npx claude-flow@alpha mcp start
claude mcp add ruv-swarm npx ruv-swarm mcp start  # Optional: Enhanced coordination
```

## MCP Tool Categories

### Coordination
`swarm_init`, `agent_spawn`, `task_orchestrate`

### Monitoring
`swarm_status`, `agent_list`, `agent_metrics`, `task_status`, `task_results`

### Memory & Neural
`memory_usage`, `neural_status`, `neural_train`, `neural_patterns`

### GitHub Integration
`github_swarm`, `repo_analyze`, `pr_enhance`, `issue_triage`, `code_review`

### System
`benchmark_run`, `features_detect`, `swarm_monitor`


## 🚀 Agent Execution Flow with Claude Code

### The Correct Pattern:

1. **Optional**: Use MCP tools to set up coordination topology
2. **REQUIRED**: Use Claude Code's Task tool to spawn agents that do actual work
3. **REQUIRED**: Each agent runs hooks for coordination
4. **REQUIRED**: Batch all operations in single messages

### Example Full-Stack Development:

```javascript
// Single message with all agent spawning via Claude Code's Task tool
[Parallel Agent Execution]:
  Task("Backend Developer", "Build REST API with Express. Use hooks for coordination.", "backend-dev")
  Task("Frontend Developer", "Create React UI. Coordinate with backend via memory.", "coder")
  Task("Database Architect", "Design PostgreSQL schema. Store schema in memory.", "code-analyzer")
  Task("Test Engineer", "Write Jest tests. Check memory for API contracts.", "tester")
  Task("DevOps Engineer", "Setup Docker and CI/CD. Document in memory.", "cicd-engineer")
  Task("Security Auditor", "Review authentication. Report findings via hooks.", "reviewer")
  
  // All todos batched together
  TodoWrite { todos: [...8-10 todos...] }
  
  // All file operations together
  Write "backend/server.js"
  Write "frontend/App.jsx"
  Write "database/schema.sql"
```

## 📋 Claude Flow Agent Coordination Protocol

### Every Claude Flow Agent Spawned via Task Tool MUST:

**1️⃣ BEFORE Work:**
```bash
npx claude-flow@alpha hooks pre-task --description "[task]"
npx claude-flow@alpha hooks session-restore --session-id "swarm-[id]"
```

**2️⃣ DURING Work:**
```bash
npx claude-flow@alpha hooks post-edit --file "[file]" --memory-key "swarm/[agent]/[step]"
npx claude-flow@alpha hooks notify --message "[what was done]"
```

**3️⃣ AFTER Work:**
```bash
npx claude-flow@alpha hooks post-task --task-id "[task]"
npx claude-flow@alpha hooks session-end --export-metrics true
```

## 🎯 Claude Flow Concurrent Execution Examples

### ✅ Claude Flow CORRECT WORKFLOW: MCP Coordinates, Claude Code Executes

```javascript
// Step 1: MCP tools set up coordination (optional, for complex tasks)
[Single Message - Coordination Setup]:
  mcp__claude-flow__swarm_init { topology: "mesh", maxAgents: 6 }
  mcp__claude-flow__agent_spawn { type: "researcher" }
  mcp__claude-flow__agent_spawn { type: "coder" }
  mcp__claude-flow__agent_spawn { type: "tester" }

// Step 2: Claude Code Task tool spawns ACTUAL agents that do the work
[Single Message - Parallel Agent Execution]:
  // Claude Code's Task tool spawns real agents concurrently
  Task("Research agent", "Analyze API requirements and best practices. Check memory for prior decisions.", "researcher")
  Task("Coder agent", "Implement REST endpoints with authentication. Coordinate via hooks.", "coder")
  Task("Database agent", "Design and implement database schema. Store decisions in memory.", "code-analyzer")
  Task("Tester agent", "Create comprehensive test suite with 90% coverage.", "tester")
  Task("Reviewer agent", "Review code quality and security. Document findings.", "reviewer")
  
  // Batch ALL todos in ONE call
  TodoWrite { todos: [
    {id: "1", content: "Research API patterns", status: "in_progress", priority: "high"},
    {id: "2", content: "Design database schema", status: "in_progress", priority: "high"},
    {id: "3", content: "Implement authentication", status: "pending", priority: "high"},
    {id: "4", content: "Build REST endpoints", status: "pending", priority: "high"},
    {id: "5", content: "Write unit tests", status: "pending", priority: "medium"},
    {id: "6", content: "Integration tests", status: "pending", priority: "medium"},
    {id: "7", content: "API documentation", status: "pending", priority: "low"},
    {id: "8", content: "Performance optimization", status: "pending", priority: "low"}
  ]}
  
  // Parallel file operations
  Bash "mkdir -p app/{src,tests,docs,config}"
  Write "app/package.json"
  Write "app/src/server.js"
  Write "app/tests/server.test.js"
  Write "app/docs/API.md"
```

### ❌ WRONG (Multiple Messages):
```javascript
Message 1: mcp__claude-flow__swarm_init
Message 2: Task("agent 1")
Message 3: TodoWrite { todos: [single todo] }
Message 4: Write "file.js"
// This breaks parallel coordination!
```


## Hooks Integration

### Pre-Operation
- Auto-assign agents by file type
- Validate commands for safety
- Prepare resources automatically
- Optimize topology by complexity
- Cache searches

### Post-Operation
- Auto-format code
- Train neural patterns
- Update memory
- Analyze performance
- Track token usage

### Session Management
- Generate summaries
- Persist state
- Track metrics
- Restore context
- Export workflows

## 📊 Visualization Dashboard

Agent activity is automatically visualized in real-time when services are running.

### Starting the Visualization

```bash
# Terminal 1: Start backend services (WebSocket + REST API)
npx tsx scripts/start-visualization-services.ts

# Terminal 2: Start frontend dashboard
cd frontend && npm run dev
```

Then open http://localhost:3000 to view the dashboard.

### Auto-Emit Events (Hook Integration)

Task agents automatically emit visualization events via Claude Code hooks:
- **PreToolUse hook**: Emits `agent:spawned` and `agent:started` events when Task tool is invoked
- **PostToolUse hook**: Emits `agent:completed` or `agent:error` events when Task completes

No manual action required - just use the Task tool and agents appear in the visualization!

### Manual Event Emission

For custom workflows or debugging:

```bash
# Emit spawn event with agent type
npx tsx scripts/emit-agent-event.ts spawn <agentId> <agentType>

# Emit start event
npx tsx scripts/emit-agent-event.ts start <agentId>

# Emit completion event with duration (ms)
npx tsx scripts/emit-agent-event.ts complete <agentId> [duration]

# Emit error event
npx tsx scripts/emit-agent-event.ts error <agentId> "Error message"
```

### Programmatic API

```typescript
import { emitAgentSpawn, emitAgentComplete, emitAgentError } from './src/visualization';

// Emit events in your code
await emitAgentSpawn('my-agent', 'researcher');
await emitAgentComplete('my-agent', 5000);
await emitAgentError('my-agent', 'Something went wrong');
```

### Event Types

| Event | Status | When |
|-------|--------|------|
| `agent:spawned` | `idle` | Agent created |
| `agent:started` | `running` | Agent begins work |
| `agent:completed` | `completed` | Agent finishes |
| `agent:error` | `error` | Agent fails |

## Integration Tips

1. Start with basic swarm init
2. Scale agents gradually
3. Use memory for context
4. Monitor progress regularly
5. Train patterns from success
6. Enable hooks automation
7. Use GitHub tools first

## Support

- Documentation: https://github.com/ruvnet/claude-flow
- Issues: https://github.com/ruvnet/claude-flow/issues

---

Remember: **Claude Flow coordinates, Claude Code creates!**

# important-instruction-reminders
Do what has been asked; nothing more, nothing less.
NEVER create files unless they're absolutely necessary for achieving your goal.
ALWAYS prefer editing an existing file to creating a new one.
NEVER proactively create documentation files (*.md) or README files. Only create documentation files if explicitly requested by the User.
Never save working files, text/mds and tests to the root folder.


---

**Generated by**: Agentic QE Fleet v2.5.0
**Initialization Date**: 2025-12-14T13:48:54.968Z
**Fleet Topology**: hierarchical
- We always implement all the code/tests, with proper implementation. We value the quality we deliver to our users.

## Agentic QE v3

This project uses **Agentic QE v3** - a Domain-Driven Quality Engineering platform with 12 bounded contexts, ReasoningBank learning, and HNSW vector search.

---

## 🐝 AQE FLEET ORCHESTRATION

### Fleet Initialization

**For QE-specific tasks, initialize the AQE fleet:**

```javascript
// Initialize AQE Fleet with MCP tool
mcp__agentic-qe__mcp__agentic_qe__fleet_init({
  config: {
    topology: "hierarchical",  // Queen-led for QE coordination
    maxAgents: 15,
    testingFocus: ["unit", "integration", "e2e", "performance"],
    frameworks: ["vitest", "jest", "playwright"],
    environments: ["node", "browser"]
  },
  projectContext: {
    language: "typescript",
    buildSystem: "npm"
  }
})
```

### 12 DDD Domains → Agent Mapping

| Domain | Primary Agents | Focus Area |
|--------|---------------|------------|
| `test-generation` | qe-test-architect, qe-tdd-specialist | AI-powered test creation |
| `test-execution` | qe-parallel-executor, qe-flaky-hunter, qe-retry-handler | Parallel execution, flaky detection |
| `coverage-analysis` | qe-coverage-specialist, qe-gap-detector | O(log n) sublinear coverage |
| `quality-assessment` | qe-quality-gate, qe-deployment-advisor | Quality gates, risk scoring |
| `defect-intelligence` | qe-defect-predictor, qe-root-cause-analyzer | ML-powered defect prediction |
| `learning-optimization` | qe-learning-coordinator, qe-pattern-learner | Cross-domain pattern learning |
| `requirements-validation` | qe-tdd-specialist, qe-property-tester | BDD scenarios, property tests |
| `code-intelligence` | qe-knowledge-manager, code-analyzer | Knowledge graphs, 80% token reduction |
| `security-compliance` | qe-security-scanner, qe-security-auditor | OWASP, CVE detection |
| `contract-testing` | qe-contract-validator, qe-api-tester | Pact, schema validation |
| `visual-accessibility` | qe-visual-tester, qe-a11y-validator | Visual regression, WCAG |
| `chaos-resilience` | qe-chaos-engineer, qe-performance-tester | Fault injection, load testing |

### Fleet MCP Tools

```javascript
// Spawn specialized QE agent
mcp__agentic-qe__mcp__agentic_qe__agent_spawn({
  spec: {
    type: "test-generator",  // or: coverage-analyzer, quality-gate, performance-tester, security-scanner, chaos-engineer, visual-tester
    capabilities: ["unit-tests", "integration-tests"],
    name: "test-gen-1"
  },
  fleetId: "fleet-123"  // From fleet_init
})

// AI-enhanced test generation
mcp__agentic-qe__mcp__agentic_qe__test_generate_enhanced({
  sourceCode: "...",
  language: "typescript",
  testType: "unit",  // unit, integration, e2e, property-based, mutation
  coverageGoal: 90,
  aiEnhancement: true,
  detectAntiPatterns: true
})

// Parallel test execution with retry
mcp__agentic-qe__mcp__agentic_qe__test_execute_parallel({
  testFiles: ["tests/**/*.test.ts"],
  parallelism: 4,
  retryFailures: true,
  maxRetries: 3,
  collectCoverage: true
})

// Orchestrate QE task across fleet
mcp__agentic-qe__mcp__agentic_qe__task_orchestrate({
  task: {
    type: "comprehensive-testing",  // or: quality-gate, defect-prevention, performance-validation
    priority: "high",
    strategy: "adaptive",
    maxAgents: 5
  },
  context: {
    project: "my-project",
    environment: "test"
  },
  fleetId: "fleet-123"
})
```

### QE Memory Operations

```javascript
// Store QE pattern with namespace
mcp__agentic-qe__mcp__agentic_qe__memory_store({
  key: "coverage-pattern-auth",
  value: { pattern: "...", successRate: 0.95 },
  namespace: "qe-patterns",
  ttl: 86400,  // 24 hours
  persist: true
})

// Query memory with pattern matching
mcp__agentic-qe__mcp__agentic_qe__memory_query({
  pattern: "coverage-*",
  namespace: "qe-patterns",
  limit: 10
})
```

### QE Task Routing by Domain

| Task Type | MCP Tool | Agents Spawned |
|-----------|----------|----------------|
| Generate tests | `test_generate_enhanced` | qe-test-architect, qe-tdd-specialist |
| Run tests | `test_execute_parallel` | qe-parallel-executor, qe-retry-handler |
| Analyze coverage | `task_orchestrate` (coverage) | qe-coverage-specialist, qe-gap-detector |
| Quality gate | `task_orchestrate` (quality-gate) | qe-quality-gate, qe-deployment-advisor |
| Security scan | `agent_spawn` (security-scanner) | qe-security-scanner, qe-security-auditor |
| Chaos test | `agent_spawn` (chaos-engineer) | qe-chaos-engineer, qe-load-tester |

---

## Quick Reference

```bash
# Run tests
npm test -- --run

# Check quality
aqe quality assess

# Generate tests
aqe test generate <file>

# Coverage analysis
aqe coverage <path>
```

### MCP Server

The AQE v3 MCP server is configured in `.claude/mcp.json`. Available tools:

| Tool | Description |
|------|-------------|
| `fleet_init` | Initialize QE fleet with topology |
| `agent_spawn` | Spawn specialized QE agent |
| `test_generate_enhanced` | AI-powered test generation |
| `test_execute_parallel` | Parallel test execution with retry |
| `task_orchestrate` | Orchestrate multi-agent QE tasks |
| `coverage_analyze_sublinear` | O(log n) coverage analysis |
| `quality_assess` | Quality gate evaluation |
| `memory_store` / `memory_query` | Pattern storage with namespacing |
| `security_scan_comprehensive` | SAST/DAST scanning |
| `fleet_status` | Get fleet and agent status |

### Configuration

- **Enabled Domains**: test-generation, test-execution, coverage-analysis, quality-assessment, defect-intelligence, requirements-validation (+6 more)
- **Learning**: Enabled (transformer embeddings)
- **Max Concurrent Agents**: 15
- **Background Workers**: pattern-consolidator, routing-accuracy-monitor, coverage-gap-scanner, flaky-test-detector

### V3 QE Agents

V3 QE agents are installed in `.claude/agents/v3/`. Use with Claude Code's Task tool:

```javascript
// Example: Generate tests
Task({ prompt: "Generate unit tests for auth module", subagent_type: "qe-test-architect", run_in_background: true })

// Example: Analyze coverage
Task({ prompt: "Find coverage gaps in src/", subagent_type: "qe-coverage-specialist", run_in_background: true })

// Example: Security scan
Task({ prompt: "Run security audit", subagent_type: "qe-security-scanner", run_in_background: true })
```

### Integration with Claude Flow

**AQE Fleet + Claude Flow work together:**

```javascript
// STEP 1: Initialize Claude Flow swarm for coordination
Bash("npx @claude-flow/cli@latest swarm init --topology hierarchical --max-agents 15")

// STEP 2: Initialize AQE Fleet for QE-specific work
mcp__agentic-qe__mcp__agentic_qe__fleet_init({
  config: { topology: "hierarchical", maxAgents: 10, testingFocus: ["unit", "integration"] }
})

// STEP 3: Spawn agents via Claude Code Task tool (do the actual work)
Task({ prompt: "Generate tests for auth module", subagent_type: "qe-test-architect", run_in_background: true })
Task({ prompt: "Analyze coverage gaps", subagent_type: "qe-coverage-specialist", run_in_background: true })

// STEP 4: Store learnings in both systems
mcp__agentic-qe__mcp__agentic_qe__memory_store({ key: "pattern-1", value: "...", namespace: "qe-patterns", persist: true })
Bash("npx @claude-flow/cli@latest memory store --key 'qe-pattern-1' --value '...' --namespace patterns")
```

### Data Storage

- **Memory Backend**: `.agentic-qe/memory.db` (SQLite)
- **Pattern Storage**: `.agentic-qe/data/qe-patterns.db` (ReasoningBank)
- **HNSW Index**: `.agentic-qe/data/hnsw/index.bin`
- **Configuration**: `.agentic-qe/config.yaml`

### Best Practices

1. **Test Execution**: Always use `npm test -- --run` (not `npm test` which runs in watch mode)
2. **Coverage Targets**: Aim for 80%+ coverage on critical paths
3. **Quality Gates**: Run `quality_assess` before merging PRs
4. **Pattern Learning**: AQE learns from successful test patterns - consistent naming helps
5. **Fleet Coordination**: Use `fleet_init` before spawning multiple QE agents
6. **Memory Persistence**: Use `persist: true` for patterns you want to keep across sessions

### Troubleshooting

If MCP tools aren't working:
```bash
# Verify MCP server is configured
cat .claude/mcp.json

# Check fleet status
mcp__agentic-qe__mcp__agentic_qe__fleet_status({ includeAgentDetails: true })

# Reinitialize if needed
aqe init --auto
```

---
*Generated by AQE v3 init - 2026-01-20T10:51:23.805Z*
