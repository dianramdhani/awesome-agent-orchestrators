# PRD: Bernstein Multi-Agent Orchestration Setup
## Nanobot (PM+QA) + Gemini CLI (Frontend) + Codex CLI (Backend)

**Document Version:** 1.0  
**Last Updated:** 2026-05-10  
**Status:** Ready for Implementation  
**Target Audience:** Development Team, DevOps Engineers

---

## 📋 Table of Contents

1. [Executive Summary](#executive-summary)
2. [Architecture Overview](#architecture-overview)
3. [Agent Specifications](#agent-specifications)
4. [Bernstein Configuration](#bernstein-configuration)
5. [Implementation Phases](#implementation-phases)
6. [Usage Guide](#usage-guide)
7. [Monitoring & Observability](#monitoring--observability)
8. [Troubleshooting](#troubleshooting)
9. [QA Checklist](#qa-checklist)
10. [Rollback Plan](#rollback-plan)

---

## Executive Summary

### Objective
Implement a deterministic multi-agent orchestration system using **Bernstein** to coordinate three specialized coding agents (Nanobot, Gemini CLI, Codex CLI) for parallel development workflows with quality assurance, testing, and automated commits.

### Success Criteria
- ✅ All 3 agents operational and coordinated via Bernstein
- ✅ Tasks completed in parallel with proper isolation (git worktrees)
- ✅ Automated testing & verification before commits
- ✅ Complete audit trail (HMAC) of all operations
- ✅ Zero manual intervention in happy path
- ✅ <5% error rate in agent task execution

### Key Benefits
| Benefit | Impact |
|---------|--------|
| **Parallel Development** | 3x faster feature delivery |
| **Role Specialization** | Better code quality per domain |
| **Deterministic Execution** | Reproducible, debuggable workflows |
| **Audit Trail** | Compliance & security ready |
| **Auto-Testing** | Quality gates before merge |
| **Git Integration** | Clean history with atomic commits |

### Timeline
- **Phase 1 (Setup):** 4-6 hours
- **Phase 2 (Configuration):** 2-3 hours
- **Phase 3 (Worktree Init):** 1-2 hours
- **Phase 4 (Test Run):** 3-4 hours
- **Phase 5 (Production):** 2-3 hours
- **Total:** 12-18 hours

---

## Architecture Overview

### System Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│                   BERNSTEIN ORCHESTRATOR                        │
│  (Deterministic Scheduler + Task Manager + Audit Trail)        │
└───────────┬──────────────────────────────────────────────────────┘
            │
    ┌───────┴────────────────────────────────────────┐
    │                                                │
    ▼                                                ▼
┌──────────────────────┐                   ┌────────────────────┐
│   NANOBOT (PM+QA)    │                   │  TASK DEFINITION   │
│  Git Worktree #0     │                   │  ├─ Decompose      │
│  ├─ Assign tasks     │                   │  ├─ Dev workflow   │
│  ├─ QA verification  │                   │  ├─ Test & verify  │
│  ├─ Review PRs       │                   │  └─ Auto-commit    │
│  └─ Auto-merge       │                   └────────────────────┘
└──────────────────────┘
    │
    ├──────────────────────────┬──────────────────────────┐
    │                          │                          │
    ▼                          ▼                          ▼
┌──────────────────────┐  ┌──────────────────────┐  ┌──────────────────────┐
│  GEMINI CLI (FE)     │  │  CODEX CLI (BE)      │  │  TESTING AGENT       │
│  Git Worktree #1     │  │  Git Worktree #2     │  │  (Parallel)          │
│  ├─ UI components    │  │  ├─ API endpoints    │  │  ├─ Unit tests       │
│  ├─ State mgmt       │  │  ├─ DB migrations    │  │  ├─ Integration      │
│  ├─ Styling          │  │  ├─ Auth logic       │  │  ├─ E2E tests        │
│  └─ Documentation    │  │  └─ Error handling   │  │  └─ Coverage report  │
└──────────────────────┘  └──────────────────────┘  └──────────────────────┘
    │                          │                          │
    │                          │                          │
    └──────────────┬───────────┴──────────────────────────┘
                   │
                   ▼
         ┌──────────────────────┐
         │  GIT INTEGRATION     │
         │  ├─ Merge results    │
         │  ├─ HMAC audit log   │
         │  ├─ Commit message   │
         │  └─ Branch cleanup   │
         └──────────────────────┘
                   │
                   ▼
         ┌──────────────────────┐
         │  MAIN BRANCH         │
         │  (Production Ready)  │
         └──────────────────────┘
```

### Agent Responsibilities

| Phase | Nanobot (PM) | Gemini (FE) | Codex (BE) | Testing |
|-------|--------------|------------|-----------|---------|
| **1. Decompose** | Parse requirements → Create subtasks | - | - | - |
| **2. Design** | Review architecture | Create UI spec | Create API spec | - |
| **3. Develop** | Coordinate | Build UI components | Build APIs | - |
| **4. Integrate** | Coordinate | Integration testing | Integration testing | Run test suite |
| **5. QA** | Run final tests | Validate UI/UX | Validate APIs | Generate report |
| **6. Commit** | Merge & cleanup | - | - | - |

### Data Flow

```
User Input / GitHub Issue
    │
    ▼
BERNSTEIN: Parse & Decompose
    │
    ├─→ Task #1 (FE) → Gemini CLI → Output: UI code
    │
    ├─→ Task #2 (BE) → Codex CLI → Output: API code
    │
    ├─→ Task #3 (Testing) → Parallel → Output: Test results
    │
    └─→ Nanobot: Aggregate & Verify
         │
         ├─→ Conflict detection & resolution
         ├─→ Test validation
         ├─→ PR review & approval
         └─→ Auto-merge to main
              │
              ▼
         Production Code
```

---

## Agent Specifications

### Agent #1: Nanobot (Project Manager + QA Lead)

**Role:** Orchestrator, coordinator, quality gatekeeper

**Responsibilities:**
- Parse user requirements and decompose into subtasks
- Assign tasks to specialized agents
- Monitor task progress
- Validate code quality and test results
- Conduct final QA review
- Merge approved PRs to main branch
- Manage git worktree cleanup

**Capabilities:**
- Natural language understanding (requirements parsing)
- Decision making (task prioritization, conflict resolution)
- Testing frameworks (pytest, jest, mocha)
- Git operations (merge, review, cleanup)
- Documentation generation

**Constraints:**
- No direct code writing (coordination only)
- Cannot override automated tests
- Must verify before merge
- 30-minute task timeout

**System Prompt:** See `.bernstein/prompts/nanobot-pm.txt`

**Configuration Example:**
```yaml
agents:
  nanobot:
    name: "Nanobot (PM+QA)"
    model: "claude-3-5-sonnet"  # Balance of speed & intelligence
    adapter: "nanobot"
    max_tokens: 8000
    temperature: 0.3  # Deterministic for decisions
    tools:
      - git_operations
      - test_execution
      - code_review
      - documentation
    timeout: 30m
    retry_policy: "exponential_backoff"
    memory: "persistent"  # Remember previous tasks
    context_window: 20000
```

---

### Agent #2: Gemini CLI (Frontend Developer)

**Role:** Frontend development specialist

**Responsibilities:**
- Build UI components based on specifications
- Implement state management
- Write CSS/styling
- Create component documentation
- Implement error boundaries and loading states
- Ensure accessibility (WCAG 2.1 AA)

**Tech Stack:**
- JavaScript/TypeScript
- React/Vue/Svelte (framework-agnostic)
- CSS Modules / Tailwind / Styled Components
- Testing: Jest / Vitest
- Build: Vite / Webpack

**Quality Standards:**
- TypeScript strict mode (no `any`)
- Component test coverage ≥ 80%
- Accessibility compliance (axe, lighthouse)
- Performance: LCP < 2.5s, CLS < 0.1
- Code style: ESLint + Prettier

**Constraints:**
- Frontend code only (no backend changes)
- Must write tests alongside components
- Cannot merge directly (QA required)
- 25-minute task timeout

**System Prompt:** See `.bernstein/prompts/gemini-frontend.txt`

**Configuration Example:**
```yaml
agents:
  gemini:
    name: "Gemini CLI (Frontend Dev)"
    model: "gemini-2.0-flash"
    adapter: "gemini"
    max_tokens: 6000
    temperature: 0.4  # Allow creative solutions
    tools:
      - jsx_components
      - styling
      - state_management
      - testing
      - documentation
    timeout: 25m
    retry_policy: "exponential_backoff"
    memory: "session"
    context_window: 12000
    scope:
      paths: ["src/components/", "src/pages/", "src/hooks/"]
      exclude: ["src/api/", "src/server/"]
```

---

### Agent #3: Codex CLI (Backend Developer)

**Role:** Backend development specialist

**Responsibilities:**
- Design and implement API endpoints
- Handle database operations and migrations
- Implement authentication & authorization
- Error handling and validation
- API documentation (OpenAPI/GraphQL)
- Performance optimization

**Tech Stack:**
- Node.js / Python / Go (runtime-agnostic)
- Express / FastAPI / Echo (framework-agnostic)
- PostgreSQL / MongoDB (database-agnostic)
- Testing: pytest / jest / gotest
- Deployment: Docker / Kubernetes

**Quality Standards:**
- API response time < 200ms (p99)
- Database query optimization (EXPLAIN ANALYZE)
- Unit test coverage ≥ 85%
- API documentation completeness
- Security: OWASP Top 10 compliance
- Error handling: Proper HTTP status codes

**Constraints:**
- Backend code only (no frontend changes)
- Database migrations must be reversible
- API changes require documentation
- Cannot skip tests
- 25-minute task timeout

**System Prompt:** See `.bernstein/prompts/codex-backend.txt`

**Configuration Example:**
```yaml
agents:
  codex:
    name: "Codex CLI (Backend Dev)"
    model: "claude-3-5-sonnet"
    adapter: "codex"
    max_tokens: 6000
    temperature: 0.2  # Strict for backend
    tools:
      - api_endpoints
      - database
      - authentication
      - testing
      - documentation
    timeout: 25m
    retry_policy: "exponential_backoff"
    memory: "session"
    context_window: 12000
    scope:
      paths: ["src/api/", "src/server/", "src/db/"]
      exclude: ["src/components/", "src/hooks/"]
```

---

## Bernstein Configuration

### Installation

```bash
# Install Bernstein
pip install bernstein-orchestrator

# Install adapters
pip install bernstein-nanobot bernstein-gemini bernstein-codex

# Verify installation
bernstein --version
```

### Project Structure

```
your-project/
├── .bernstein/
│   ├── orchestrator.yaml          # Main config
│   ├── agents.yaml                # Agent definitions
│   ├── prompts/
│   │   ├── nanobot-pm.txt         # PM system prompt
│   │   ├── gemini-frontend.txt    # FE system prompt
│   │   └── codex-backend.txt      # BE system prompt
│   └── workflows/
│       ├── feature-development.yaml
│       ├── bug-fix.yaml
│       └── refactor.yaml
├── .git/
│   └── worktrees/                 # Git worktrees per agent
│       ├── agent-nanobot/
│       ├── agent-gemini/
│       └── agent-codex/
├── src/
│   ├── components/                # FE scope
│   ├── api/                       # BE scope
│   └── shared/                    # Shared scope
└── tests/
    ├── unit/
    ├── integration/
    └── e2e/
```

### Main Configuration File (`.bernstein/orchestrator.yaml`)

```yaml
version: "1.0"

metadata:
  project_name: "multi-agent-development"
  description: "Bernstein orchestration for parallel development"
  owner: "dev-team"
  created_at: "2026-05-10"

orchestrator:
  mode: "deterministic"
  max_parallel_tasks: 3
  task_timeout: 25m
  retry_policy:
    max_retries: 2
    backoff: "exponential"
    backoff_factor: 2
  
  # Audit trail configuration
  audit:
    enabled: true
    hmac_key: "${BERNSTEIN_HMAC_KEY}"  # Set via env var
    log_destination: ".bernstein/logs/audit.jsonl"
    retention_days: 90

  # Git integration
  git:
    default_branch: "main"
    worktree_base: ".git/worktrees"
    auto_cleanup: true
    commit_signing: true  # GPG sign commits
    gpg_key_id: "${GPG_KEY_ID}"
    
  # Monitoring
  monitoring:
    enabled: true
    metrics_backend: "prometheus"  # or "datadog", "cloudwatch"
    log_level: "INFO"
    dashboard_url: "http://localhost:9090/bernstein"

# Agent definitions
agents:
  nanobot:
    id: "nanobot-pm-qa"
    name: "Nanobot (PM+QA)"
    adapter: "nanobot"
    model: "claude-3-5-sonnet"
    provider: "anthropic"
    api_key: "${ANTHROPIC_API_KEY}"
    max_tokens: 8000
    temperature: 0.3
    tools:
      - "git_operations"
      - "test_execution"
      - "code_review"
      - "documentation_generation"
    timeout: 30m
    memory:
      type: "persistent"
      backend: "sqlite"
      path: ".bernstein/memory/nanobot.db"
    context_window: 20000
    
  gemini:
    id: "gemini-frontend"
    name: "Gemini CLI (Frontend Dev)"
    adapter: "gemini"
    model: "gemini-2.0-flash"
    provider: "google"
    api_key: "${GEMINI_API_KEY}"
    max_tokens: 6000
    temperature: 0.4
    tools:
      - "jsx_components"
      - "styling"
      - "state_management"
      - "component_testing"
    timeout: 25m
    memory:
      type: "session"
      backend: "memory"
    context_window: 12000
    file_scope:
      allowed_paths:
        - "src/components/"
        - "src/pages/"
        - "src/hooks/"
        - "src/utils/"
      disallowed_paths:
        - "src/api/"
        - "src/server/"
        - ".git/"
        - "node_modules/"
    
  codex:
    id: "codex-backend"
    name: "Codex CLI (Backend Dev)"
    adapter: "codex"
    model: "claude-3-5-sonnet"
    provider: "anthropic"
    api_key: "${ANTHROPIC_API_KEY}"
    max_tokens: 6000
    temperature: 0.2
    tools:
      - "api_endpoints"
      - "database_operations"
      - "authentication"
      - "backend_testing"
    timeout: 25m
    memory:
      type: "session"
      backend: "memory"
    context_window: 12000
    file_scope:
      allowed_paths:
        - "src/api/"
        - "src/server/"
        - "src/db/"
        - "src/auth/"
      disallowed_paths:
        - "src/components/"
        - "src/hooks/"
        - ".git/"
        - "node_modules/"

# Task workflow definition
workflows:
  feature_development:
    name: "Feature Development"
    description: "Complete workflow for new feature development"
    trigger: "github_issue"
    phases:
      - phase_id: "decompose"
        name: "Decompose Requirements"
        agent: "nanobot"
        prompt_template: "prompts/nanobot-pm.txt"
        timeout: 15m
        output:
          type: "task_breakdown"
          format: "markdown"
        next_phase: ["design"]
        
      - phase_id: "design"
        name: "Design Architecture"
        parallel_agents: ["gemini", "codex"]
        dependencies:
          - "decompose"
        tasks:
          - agent: "gemini"
            task: "Design UI components and state architecture"
            timeout: 10m
          - agent: "codex"
            task: "Design API endpoints and database schema"
            timeout: 10m
        output:
          type: "design_document"
          format: "markdown+yaml"
        next_phase: ["develop"]
        
      - phase_id: "develop"
        name: "Parallel Development"
        parallel_agents: ["gemini", "codex"]
        dependencies:
          - "design"
        worktrees:
          gemini:
            base_branch: "main"
            branch_name: "feature/gemini-${feature_id}"
            cleanup_on_merge: true
          codex:
            base_branch: "main"
            branch_name: "feature/codex-${feature_id}"
            cleanup_on_merge: true
        tasks:
          - agent: "gemini"
            task: "Implement UI components with tests"
            context_from: "design"
            timeout: 20m
          - agent: "codex"
            task: "Implement APIs with database migrations"
            context_from: "design"
            timeout: 20m
        output:
          type: "pull_request"
          requires_review: true
        next_phase: ["integration"]
        
      - phase_id: "integration"
        name: "Integration Testing"
        agent: "nanobot"
        dependencies:
          - "develop"
        tasks:
          - "Run integration test suite"
          - "Verify API + UI connectivity"
          - "Check performance metrics"
          - "Review code quality reports"
        timeout: 15m
        validation:
          test_coverage_min: 80
          performance_max_latency_ms: 200
          all_tests_passing: true
        next_phase: ["qa"]
        
      - phase_id: "qa"
        name: "Final QA & Review"
        agent: "nanobot"
        dependencies:
          - "integration"
        tasks:
          - "Conduct security review"
          - "Verify database migrations"
          - "Check documentation"
          - "Review commit messages"
          - "Final sign-off"
        timeout: 15m
        approval_required: true
        next_phase: ["merge"]
        
      - phase_id: "merge"
        name: "Merge to Main"
        agent: "nanobot"
        dependencies:
          - "qa"
        tasks:
          - "Merge PRs to main branch"
          - "Verify CI/CD pipeline"
          - "Cleanup worktrees"
          - "Generate changelog"
        timeout: 10m
        post_merge:
          - "Deploy to staging"
          - "Run smoke tests"
          - "Update documentation"

# Testing configuration
testing:
  frameworks:
    unit: "pytest"      # Backend unit tests
    component: "jest"   # Frontend component tests
    integration: "jest" # Full stack integration
    e2e: "playwright"   # End-to-end
  
  coverage:
    minimum_percent: 80
    exclude_patterns:
      - "node_modules/**"
      - "dist/**"
      - ".git/**"
  
  auto_run:
    before_merge: true
    parallel: true
    timeout: 10m

# Error handling
error_handling:
  on_agent_timeout:
    action: "retry"
    max_attempts: 2
  
  on_test_failure:
    action: "halt"
    notify: ["slack", "email"]
  
  on_merge_conflict:
    action: "escalate_to_nanobot"
    timeout: 10m

# Post-merge hooks
post_merge:
  - type: "deploy"
    environment: "staging"
    timeout: 5m
  - type: "smoke_test"
    timeout: 10m
  - type: "notification"
    channels: ["slack", "github"]
```

### System Prompts

#### `.bernstein/prompts/nanobot-pm.txt`

```
You are Nanobot, a Project Manager and QA Lead coordinating three specialized AI developers.

## Core Responsibilities:
1. **Requirements Analysis**: Parse user requirements and GitHub issues
2. **Task Decomposition**: Break down features into subtasks for FE and BE agents
3. **Quality Assurance**: Review code, test results, and documentation
4. **Conflict Resolution**: Manage merge conflicts and architectural disagreements
5. **Final Approval**: Merge only code that meets all standards

## Decision Framework:
- Code Quality: Passes linting, type checking, all tests
- Testing: Minimum 80% coverage, all tests passing
- Documentation: API docs, component docs, changelog updated
- Security: No vulnerabilities, OWASP compliance
- Performance: API response <200ms, Frontend LCP <2.5s

## Communication Style:
- Clear, concise task assignments with context
- Specific success criteria for each task
- Constructive feedback with actionable fixes
- Escalate blockers immediately

## When Assigning Tasks:
- Provide complete context (requirements, links, examples)
- Define success criteria explicitly
- Set realistic timeframes (15-25 minutes per task)
- Include test requirements upfront

## When Reviewing Code:
- Check for test coverage (>80%)
- Verify code follows project standards
- Look for security issues
- Validate error handling
- Ensure documentation is complete

## When Merging:
- Verify all tests pass
- Check no conflicts remain
- Confirm CI/CD pipeline succeeds
- Update changelog
- Generate merge summary

## Tools Available:
- git_operations: clone, checkout, merge, push, pull
- test_execution: run test suites, generate reports
- code_review: static analysis, style checks
- documentation: generate docs, update README

Remember: You're the quality gatekeeper. Never merge substandard code.
```

#### `.bernstein/prompts/gemini-frontend.txt`

```
You are Gemini, a Frontend Developer specializing in building beautiful, accessible, performant UIs.

## Core Responsibilities:
1. **Component Development**: Build reusable, well-tested React/Vue/Svelte components
2. **State Management**: Implement clean, predictable state (Redux, Pinia, Svelte stores)
3. **Styling**: Write maintainable CSS with proper organization
4. **Testing**: Comprehensive component testing with Jest/Vitest
5. **Accessibility**: WCAG 2.1 AA compliance, semantic HTML

## Code Quality Standards:
- TypeScript with strict mode (no `any`)
- ESLint + Prettier for consistent style
- Minimum 80% test coverage
- Component documentation with Storybook/Vite comments
- Performance: LCP <2.5s, CLS <0.1

## Component Structure:
```
ComponentName/
├── ComponentName.tsx      # Main component
├── ComponentName.module.css # Styles
├── ComponentName.test.tsx  # Tests
├── ComponentName.stories.tsx # Storybook
└── types.ts              # TypeScript types
```

## Testing Requirements:
- Unit tests for logic (useState, useEffect, etc.)
- Integration tests for component composition
- Snapshot tests for complex renders
- Accessibility tests (axe)
- Performance tests (metrics)

## When Writing Components:
1. Start with TypeScript interfaces/types
2. Write tests before implementation
3. Use semantic HTML
4. Include error boundaries
5. Add loading states
6. Document with JSDoc comments
7. Include Storybook stories

## Error Handling:
- Try-catch boundaries for async operations
- Graceful error messages to users
- Log errors for debugging
- Fallback UI for missing data

## Performance Checklist:
- [ ] No unnecessary re-renders
- [ ] Proper memoization (React.memo, useMemo)
- [ ] Code splitting where appropriate
- [ ] Image optimization
- [ ] Bundle size monitoring

## Accessibility Checklist:
- [ ] Semantic HTML (h1-h6, nav, main, etc.)
- [ ] ARIA labels where needed
- [ ] Keyboard navigation support
- [ ] Color contrast ≥4.5:1
- [ ] Focus indicators visible
- [ ] Alt text for images

## Tools Available:
- jsx_components: create/modify React/Vue components
- styling: CSS, SCSS, Tailwind configuration
- state_management: Redux, Pinia setup
- component_testing: Jest/Vitest test writing
- documentation: Storybook, JSDoc

Remember: Frontend code impacts user experience. Prioritize clarity, accessibility, and performance.
```

#### `.bernstein/prompts/codex-backend.txt`

```
You are Codex, a Backend Developer specializing in robust APIs, databases, and system architecture.

## Core Responsibilities:
1. **API Development**: RESTful/GraphQL endpoints with proper design
2. **Database Management**: Schema design, migrations, optimization
3. **Authentication**: Secure auth systems (JWT, OAuth, etc.)
4. **Error Handling**: Comprehensive error handling and validation
5. **Testing**: Unit and integration tests for all logic
6. **Documentation**: API docs, schema documentation, runbooks

## Code Quality Standards:
- TypeScript with strict mode (no `any`)
- ESLint for consistency
- Minimum 85% test coverage
- API response time <200ms (p99)
- Database query optimization (EXPLAIN ANALYZE)
- OWASP Top 10 compliance

## API Design:
- RESTful conventions (GET, POST, PUT, DELETE)
- Proper HTTP status codes (200, 201, 400, 401, 404, 500)
- Request/response validation (Zod, Joi)
- Rate limiting and pagination
- Proper error response format

## Database Migrations:
- Always reversible (up/down)
- Data validation before migration
- Backup before production migration
- Document migration purpose
- Test migration on staging first

## Testing Requirements:
- Unit tests for business logic
- Integration tests for API endpoints
- Database tests with test fixtures
- Error case testing
- Load testing for performance-critical paths

## When Writing APIs:
1. Define request/response schemas first
2. Implement validation
3. Write tests before endpoint logic
4. Add error handling
5. Document with OpenAPI/GraphQL
6. Add logging for debugging
7. Include rate limiting

## Security Checklist:
- [ ] Input validation (SQL injection, XSS)
- [ ] Authentication on protected endpoints
- [ ] HTTPS only
- [ ] CORS properly configured
- [ ] Rate limiting enabled
- [ ] Secrets in environment variables
- [ ] SQL prepared statements
- [ ] OWASP compliance verified

## Performance Optimization:
- [ ] Database query optimization
- [ ] Proper indexing
- [ ] Caching strategy (Redis)
- [ ] Connection pooling
- [ ] Async operations where appropriate
- [ ] Response compression (gzip)

## Error Handling Pattern:
```
try {
  // Validate input
  // Execute business logic
  // Return success response
} catch (ValidationError) {
  // Return 400 with details
} catch (AuthError) {
  // Return 401
} catch (NotFoundError) {
  // Return 404
} catch (Error) {
  // Log error
  // Return 500
}
```

## Database Versioning:
- Semantic versioning for schema
- Document breaking changes
- Provide migration guides
- Test migrations thoroughly

## Tools Available:
- api_endpoints: create/modify API routes
- database_operations: schema design, migrations
- authentication: auth system setup
- backend_testing: pytest/jest for testing
- documentation: API docs generation

Remember: Backend code powers the entire system. Prioritize security, reliability, and performance.
```

---

## Implementation Phases

### Phase 1: Environment Setup (4-6 hours)

**Objective:** Install and configure Bernstein and all dependencies

**Step 1.1: Install Bernstein**
```bash
# Create virtual environment
python3 -m venv .venv
source .venv/bin/activate  # On Windows: .venv\Scripts\activate

# Install Bernstein
pip install bernstein-orchestrator

# Verify
bernstein --version
```

**Step 1.2: Install Agent Adapters**
```bash
# Install all required adapters
pip install \
  bernstein-nanobot \
  bernstein-gemini \
  bernstein-codex

# Verify
bernstein adapter list
```

**Step 1.3: Setup Environment Variables**
```bash
# Create .env file
cat > .env << 'EOF'
# Anthropic API (Nanobot + Codex)
ANTHROPIC_API_KEY=sk-ant-...

# Google API (Gemini)
GEMINI_API_KEY=AIza...

# Bernstein Configuration
BERNSTEIN_HMAC_KEY=$(openssl rand -hex 32)
GPG_KEY_ID=your-gpg-key-id

# Agent Configuration
NANOBOT_MODEL=claude-3-5-sonnet
GEMINI_MODEL=gemini-2.0-flash
CODEX_MODEL=claude-3-5-sonnet

# Git Configuration
GIT_AUTHOR_NAME="Bernstein Orchestrator"
GIT_AUTHOR_EMAIL="bernstein@orchestrators.dev"

# Monitoring (Optional)
PROMETHEUS_ENDPOINT=http://localhost:9090
SLACK_WEBHOOK_URL=https://hooks.slack.com/...
EOF

# Load environment
source .env
```

**Step 1.4: Create Project Structure**
```bash
# Create .bernstein directory structure
mkdir -p .bernstein/{prompts,workflows,logs,memory}
mkdir -p .git/worktrees

# Copy configuration files from this PRD
# (Files listed below in next steps)
```

**Step 1.5: Test Installation**
```bash
# Test all agents are accessible
bernstein agent list

# Output should show:
# ✓ nanobot-pm-qa (claude-3-5-sonnet)
# ✓ gemini-frontend (gemini-2.0-flash)
# ✓ codex-backend (claude-3-5-sonnet)

# Test Bernstein CLI
bernstein --help
```

**Checklist:**
- [ ] Python 3.9+ installed
- [ ] Virtual environment created
- [ ] Bernstein installed
- [ ] All adapters installed
- [ ] Environment variables set
- [ ] .bernstein directory created
- [ ] All agents accessible

**Troubleshooting:**
- If adapter fails: `pip install --upgrade bernstein-nanobot`
- If API key fails: Check environment variable format
- If port in use: Change `PROMETHEUS_ENDPOINT` port

---

### Phase 2: Agent Configuration (2-3 hours)

**Objective:** Configure and test each agent individually

**Step 2.1: Copy Configuration Files**

Create `.bernstein/orchestrator.yaml` with content from [Bernstein Configuration](#bernstein-configuration) section above.

Create `.bernstein/agents.yaml`:
```yaml
agents:
  nanobot:
    id: "nanobot-pm-qa"
    name: "Nanobot (PM+QA)"
    adapter: "nanobot"
    model: "claude-3-5-sonnet"
    
  gemini:
    id: "gemini-frontend"
    name: "Gemini CLI (Frontend Dev)"
    adapter: "gemini"
    model: "gemini-2.0-flash"
    
  codex:
    id: "codex-backend"
    name: "Codex CLI (Backend Dev)"
    adapter: "codex"
    model: "claude-3-5-sonnet"
```

**Step 2.2: Copy System Prompts**

Create `.bernstein/prompts/nanobot-pm.txt` with content from [System Prompts](#system-prompts) section.

Create `.bernstein/prompts/gemini-frontend.txt` similarly.

Create `.bernstein/prompts/codex-backend.txt` similarly.

**Step 2.3: Test Nanobot**
```bash
# Test Nanobot with a simple decomposition task
bernstein test-agent nanobot \
  --prompt "Break down: Build a user authentication system" \
  --timeout 5m

# Expected: Clear task breakdown with subtasks
```

**Step 2.4: Test Gemini**
```bash
# Test Gemini with a component task
bernstein test-agent gemini \
  --prompt "Create a React Login component with email/password fields" \
  --timeout 5m

# Expected: Component code with tests
```

**Step 2.5: Test Codex**
```bash
# Test Codex with an API task
bernstein test-agent codex \
  --prompt "Design a POST /api/auth/login endpoint" \
  --timeout 5m

# Expected: API implementation with validation
```

**Checklist:**
- [ ] orchestrator.yaml created and validated
- [ ] agents.yaml created
- [ ] All 3 system prompts created
- [ ] Nanobot tested successfully
- [ ] Gemini tested successfully
- [ ] Codex tested successfully
- [ ] API keys working for all agents

**Troubleshooting:**
- If agent times out: Check API key and quota
- If prompt not found: Verify file paths
- If model not available: Check model name spelling

---

### Phase 3: Worktree Initialization (1-2 hours)

**Objective:** Setup isolated git worktrees for each agent

**Step 3.1: Initialize Bernstein Worktrees**
```bash
# Bernstein auto-initializes worktrees, but configure manually
bernstein worktree init \
  --base-branch main \
  --agents nanobot gemini codex

# Verify worktrees created
ls -la .git/worktrees/

# Expected output:
# agent-nanobot/
# agent-gemini/
# agent-codex/
```

**Step 3.2: Configure Worktree Isolation**
```bash
# Edit .git/config to set per-worktree settings
git config --worktree core.sshCommand "ssh -i ~/.ssh/id_ed25519"

# Set per-worktree Git user (optional)
cd .git/worktrees/agent-nanobot
git config user.name "Nanobot PM"
git config user.email "nanobot@orchestrators.dev"
cd ../../..
```

**Step 3.3: Test Worktree Isolation**
```bash
# Verify each worktree can work independently
cd .git/worktrees/agent-gemini
git status
git branch

# Expected: Clean working directory, on main branch

cd ../../../
```

**Step 3.4: Setup Branch Protection**
```bash
# Configure which branches agents can work on
cat > .bernstein/worktree-config.yaml << 'EOF'
worktrees:
  nanobot:
    base_branch: "main"
    can_create: ["feature/*", "bugfix/*"]
    can_merge_to: ["main"]
    
  gemini:
    base_branch: "main"
    can_create: ["feature/fe-*"]
    can_merge_to: ["feature/fe-*"]
    
  codex:
    base_branch: "main"
    can_create: ["feature/be-*"]
    can_merge_to: ["feature/be-*"]
EOF
```

**Step 3.5: Test Branch Operations**
```bash
# Test creating a feature branch
bernstein worktree create-branch \
  --agent gemini \
  --branch feature/fe-test-component

# Verify branch exists
git branch -a | grep feature/fe-test

# Test checkout in worktree
cd .git/worktrees/agent-gemini
git status

cd ../../../
```

**Checklist:**
- [ ] All 3 worktrees initialized
- [ ] Worktrees located in .git/worktrees/
- [ ] Each worktree has clean working directory
- [ ] Git user configured per worktree
- [ ] Worktree config created
- [ ] Branch isolation working

**Troubleshooting:**
- If worktree already exists: `bernstein worktree remove --agent <name>`
- If branch conflict: Use `bernstein worktree reset --agent <name> --force`
- If permission denied: Check git credentials and SSH keys

---

### Phase 4: Test Run (3-4 hours)

**Objective:** Execute full orchestration workflow in dry-run and actual modes

**Step 4.1: Create Test Task**
```bash
# Create a test issue to orchestrate
cat > TEST_TASK.md << 'EOF'
# Test Task: Simple Counter Component

## Requirements
Build a simple counter component with increment/decrement buttons.

### Frontend (Gemini)
- React component with two buttons
- Display current count
- Unit tests (80% coverage)
- CSS styling

### Backend (Codex)
- POST /api/counter/increment endpoint
- POST /api/counter/decrement endpoint
- Database persistence
- API documentation

### Testing (Nanobot)
- E2E test for full workflow
- Performance validation
- Documentation review

## Success Criteria
- Code merged to main
- All tests passing
- Documentation complete
- Zero bugs in dry-run
EOF
```

**Step 4.2: Dry-Run Orchestration**
```bash
# Run orchestration in dry-run mode (no commits)
bernstein run \
  --workflow feature_development \
  --task TEST_TASK.md \
  --dry-run \
  --verbose

# Expected output:
# [Phase 1/6] Decompose: ✓ Complete
# [Phase 2/6] Design: ✓ Complete
# [Phase 3/6] Develop (FE): ✓ Complete
# [Phase 3/6] Develop (BE): ✓ Complete
# [Phase 4/6] Integration: ✓ Complete
# [Phase 5/6] QA: ✓ Complete
# [Phase 6/6] Merge: ✓ (dry-run, no actual merge)
```

**Step 4.3: Review Dry-Run Output**
```bash
# Review generated code without merging
bernstein review dry-run \
  --show-diff \
  --check-quality

# Expected: Code review output showing:
# - Generated files
# - Test coverage
# - Code quality scores
# - Any issues found
```

**Step 4.4: Actual Test Run**
```bash
# Run actual orchestration (will commit to feature branches)
bernstein run \
  --workflow feature_development \
  --task TEST_TASK.md \
  --verbose

# Expected: Same output but with actual git commits

# Monitor progress
bernstein monitor --follow
```

**Step 4.5: Verify Results**
```bash
# Check generated code
git log --oneline -20

# Expected output showing commits from each agent:
# abc1234 (feat/be-counter-123) Codex: Add counter API endpoints
# def5678 (feat/fe-counter-123) Gemini: Add Counter component
# ghi9012 Nanobot: Merge counter feature

# Verify tests passed
bernstein test-report --latest

# Expected: 100% pass rate, >80% coverage

# Verify merged to main
git log main --oneline -5
```

**Step 4.6: Monitor Audit Trail**
```bash
# Review HMAC audit trail
bernstein audit show --last-run

# Expected: Complete log of all operations with signatures
# - Task decomposition by Nanobot
# - Code generation by Gemini & Codex
# - Test execution
# - Merge by Nanobot
```

**Checklist:**
- [ ] Test task created
- [ ] Dry-run completed successfully
- [ ] Dry-run output reviewed
- [ ] Actual run completed
- [ ] Code merged to main
- [ ] All tests passing
- [ ] Audit trail complete
- [ ] No manual intervention needed

**Troubleshooting:**
- If phase fails: Check agent logs: `bernstein logs <agent-name>`
- If test fails: `bernstein test-report --details`
- If merge conflict: `bernstein worktree reset --agent <name>`
- If timeout: Increase in `orchestrator.yaml` task_timeout

---

### Phase 5: Production Readiness (2-3 hours)

**Objective:** Final validation and prepare for production deployment

**Step 5.1: Production Configuration**
```bash
# Update .bernstein/orchestrator.yaml for production
cat >> .env << 'EOF'

# Production Settings
BERNSTEIN_ENVIRONMENT=production
BERNSTEIN_LOG_LEVEL=INFO
BERNSTEIN_RETRY_MAX=3
BERNSTEIN_AUTO_CLEANUP=true

# Alerting
SLACK_WEBHOOK_URL=https://hooks.slack.com/...
PAGERDUTY_API_KEY=key-...
EOF

# Apply production config
bernstein config set environment=production
bernstein config set log_level=INFO
```

**Step 5.2: Security Hardening**
```bash
# Setup GPG signing for commits
gpg --gen-key  # If not already done

# Configure automatic signing
git config --global commit.gpgSign true
git config --global user.signingkey <your-gpg-key-id>

# Export public key for audit
gpg --export --armor > .bernstein/public-key.asc
```

**Step 5.3: Monitoring Setup**
```bash
# Start Prometheus metrics collection
bernstein monitor start \
  --backend prometheus \
  --port 9090

# Verify metrics endpoint
curl http://localhost:9090/metrics

# Setup Grafana dashboard (optional)
# Visit: http://localhost:3000
# Add datasource: Prometheus (http://localhost:9090)
# Import dashboard: Bernstein (ID: 19...)
```

**Step 5.4: Documentation Generation**
```bash
# Generate comprehensive documentation
bernstein docs generate \
  --output docs/ \
  --include-architecture \
  --include-troubleshooting

# Expected files:
# docs/README.md
# docs/SETUP.md
# docs/TROUBLESHOOTING.md
# docs/API.md
```

**Step 5.5: Final QA Checklist**

Complete the [QA Checklist](#qa-checklist) section below. All items must pass before production.

**Step 5.6: Team Training**
```bash
# Prepare team for production use
# 1. Share documentation
# 2. Run practice session with team
# 3. Record demo video
# 4. Create runbook

bernstein docs export --format pdf
```

**Step 5.7: Go-Live**
```bash
# Enable monitoring alerts
bernstein alert enable --all

# Set to auto-run mode (if desired)
bernstein config set auto_run_enabled=true

# First production task
bernstein run \
  --workflow feature_development \
  --task "Build user authentication" \
  --priority high

# Monitor continuously
bernstein monitor --follow
```

**Checklist:**
- [ ] Production config applied
- [ ] GPG signing configured
- [ ] Monitoring started
- [ ] Documentation generated
- [ ] QA checklist 100% pass
- [ ] Team trained
- [ ] Runbook created
- [ ] Backup plan tested
- [ ] Alerts configured
- [ ] First production task executed

---

## Usage Guide

### Quick Start

```bash
# Activate environment
source .venv/bin/activate
source .env

# Start orchestration
bernstein run \
  --workflow feature_development \
  --task "Build login component" \
  --verbose

# Monitor progress
bernstein monitor --follow

# View results
bernstein review --latest
```

### Running Different Workflows

```bash
# Feature development (recommended)
bernstein run --workflow feature_development --task <github-issue-or-file>

# Bug fix workflow
bernstein run --workflow bug_fix --task <github-issue>

# Refactoring workflow
bernstein run --workflow refactor --task <task-description>

# Custom task (one-off)
bernstein run \
  --agents nanobot,gemini,codex \
  --task "Your custom task" \
  --timeout 30m
```

### Monitoring & Debugging

```bash
# Monitor live orchestration
bernstein monitor --follow

# View agent logs
bernstein logs <agent-name> --tail 100

# View system logs
bernstein logs system --since 1h

# Generate metrics report
bernstein metrics report --period 24h

# Export audit trail
bernstein audit export --format json --output audit.json
```

### Manual Intervention

```bash
# If task gets stuck, check status
bernstein status

# Get more details on a specific task
bernstein task show <task-id>

# Kill a stuck task
bernstein task kill <task-id> --force

# Pause orchestration
bernstein pause

# Resume orchestration
bernstein resume

# Rollback last commit
bernstein rollback --last
```

---

## Monitoring & Observability

### Metrics to Track

```yaml
metrics:
  task_execution:
    - duration_seconds: Avg time per task
    - success_rate: % of tasks completed
    - error_rate: % of failed tasks
  
  code_quality:
    - test_coverage: % of code covered
    - code_review_time: Minutes to review
    - bugs_found: Number of bugs in QA
  
  performance:
    - api_latency_ms: API response time
    - deployment_frequency: Deploys per day
    - time_to_merge: Minutes from task to merge
  
  system:
    - agent_availability: % of agents online
    - memory_usage: MB per agent
    - error_logs: Count per hour
```

### Dashboard Setup

```bash
# Prometheus metrics (built-in)
# URL: http://localhost:9090

# Grafana dashboard (optional)
# URL: http://localhost:3000
# Datasource: Prometheus

# Bernstein web dashboard (optional)
# URL: http://localhost:8080/dashboard
```

### Alerting Rules

```yaml
alerts:
  - name: "Task Failed"
    condition: "error_rate > 5%"
    action: "slack notification + page on-call"
  
  - name: "Slow Task"
    condition: "task_duration > 30 minutes"
    action: "slack warning"
  
  - name: "Low Test Coverage"
    condition: "coverage < 80%"
    action: "block merge"
  
  - name: "Agent Offline"
    condition: "agent_availability < 50%"
    action: "page on-call immediately"
```

---

## Troubleshooting

### Issue 1: Agent Timeout

**Symptoms:** "Task exceeded timeout of 25m"

**Root Cause:** 
- API rate limiting
- Network latency
- Overly complex task

**Solution:**
```bash
# Check API quotas
bernstein agent status gemini --check-quota

# Increase timeout for specific agent
# Edit .bernstein/orchestrator.yaml
agents:
  gemini:
    timeout: 35m  # Was 25m

# Or increase task complexity timeout
workflow:
  phases:
    develop:
      timeout: 40m  # Was 20m

# Re-run task
bernstein run --workflow feature_development --task <task>
```

---

### Issue 2: Merge Conflict

**Symptoms:** "Cannot merge: file X has conflicts"

**Root Cause:**
- Both Gemini and Codex edited same shared file
- Main branch changed during development

**Solution:**
```bash
# Check conflict details
bernstein conflict show --task <task-id>

# Manual resolution (have Nanobot resolve)
bernstein task run \
  --agent nanobot \
  --task "Resolve merge conflict in file X"

# Or manual fix
git checkout --theirs src/shared/config.ts
git add src/shared/config.ts
bernstein worktree commit --agent nanobot --message "Resolve conflict"

# Retry merge
bernstein task retry <task-id>
```

---

### Issue 3: Test Failure

**Symptoms:** "5 tests failed in integration suite"

**Root Cause:**
- Code doesn't match API contract
- Missing dependency
- Database migration issue

**Solution:**
```bash
# Get detailed test report
bernstein test-report --failed --verbose

# Check what changed
git diff feature/be-auth..feature/fe-login

# Run specific test locally
cd .git/worktrees/agent-gemini
npm test -- --testNamePattern="Login form"

# Fix code and retry
bernstein task retry <task-id>
```

---

### Issue 4: Agent Offline

**Symptoms:** "Gemini agent not responding"

**Root Cause:**
- API key expired
- Network connectivity issue
- Rate limit hit

**Solution:**
```bash
# Test connectivity
bernstein agent test gemini --diagnose

# Check credentials
echo $GEMINI_API_KEY  # Should not be empty

# Update API key if needed
export GEMINI_API_KEY=new-key
source .env

# Restart agent
bernstein agent restart gemini

# Check status
bernstein agent status --all
```

---

### Issue 5: Out of Memory

**Symptoms:** "MemoryError: Unable to allocate memory"

**Root Cause:**
- Agent processing large files
- Memory leak in agent
- Insufficient system RAM

**Solution:**
```bash
# Check memory usage
bernstein resource usage --detailed

# Reduce context window
# Edit .bernstein/orchestrator.yaml
agents:
  nanobot:
    context_window: 16000  # Was 20000
  gemini:
    context_window: 8000   # Was 12000

# Clear memory cache
bernstein cache clear --all

# Restart orchestrator
bernstein stop
bernstein start
```

---

## QA Checklist

**Complete this checklist before production deployment:**

### 1. Environment Setup ✓
- [ ] Python 3.9+ installed
- [ ] Virtual environment activated
- [ ] Bernstein version: `bernstein --version` shows v1.0+
- [ ] All adapters installed and accessible
- [ ] API keys validated and working
- [ ] .bernstein directory structure created
- [ ] Environment variables loaded correctly

### 2. Configuration ✓
- [ ] orchestrator.yaml valid YAML and no syntax errors
- [ ] All required agent configurations present
- [ ] System prompts loaded and readable
- [ ] Workflow definitions complete
- [ ] Timeout values are realistic
- [ ] Git configuration correct
- [ ] HMAC key generated and stored securely

### 3. Agent Connectivity ✓
- [ ] Nanobot responds to test command
- [ ] Gemini responds to test command
- [ ] Codex responds to test command
- [ ] All agents can access their respective APIs
- [ ] No rate limiting errors
- [ ] Agent logs show successful initialization

### 4. Git Integration ✓
- [ ] Git repository initialized (`git init`)
- [ ] All 3 worktrees created
- [ ] Worktrees can create branches
- [ ] Worktrees can commit changes
- [ ] GPG signing configured (if required)
- [ ] .git/worktrees directory has correct structure
- [ ] No permission issues on .git directory

### 5. Dry-Run Execution ✓
- [ ] Dry-run completes without errors
- [ ] All phases execute in correct order
- [ ] Generated code quality is acceptable
- [ ] No actual commits made during dry-run
- [ ] Dry-run output logged correctly
- [ ] Audit trail records all dry-run operations

### 6. Actual Test Run ✓
- [ ] Test task executes successfully
- [ ] Nanobot decomposes task correctly
- [ ] Gemini generates valid components
- [ ] Codex generates valid API code
- [ ] Integration phase passes
- [ ] QA phase passes
- [ ] Code is merged to main branch
- [ ] All tests passing (>80% coverage)
- [ ] No bugs found during QA

### 7. Code Quality ✓
- [ ] Test coverage ≥ 80% for frontend
- [ ] Test coverage ≥ 85% for backend
- [ ] Linting passes (ESLint, Pylint)
- [ ] Type checking passes (TypeScript)
- [ ] No security vulnerabilities
- [ ] Code follows project conventions
- [ ] Comments and documentation present

### 8. Performance ✓
- [ ] API response time < 200ms (p99)
- [ ] Frontend LCP < 2.5s
- [ ] No memory leaks detected
- [ ] Database queries optimized
- [ ] No unnecessary API calls
- [ ] Agent processing time < timeout

### 9. Monitoring & Logging ✓
- [ ] Prometheus metrics accessible
- [ ] All metrics being collected
- [ ] Dashboard displays correctly
- [ ] Alert rules configured
- [ ] Slack webhook working
- [ ] Audit trail stored and retrievable
- [ ] Logs are readable and helpful

### 10. Error Handling ✓
- [ ] Task timeout handled gracefully
- [ ] API errors result in retry
- [ ] Merge conflicts detected and reported
- [ ] Test failures block merge
- [ ] Agent failures trigger rollback
- [ ] No silent failures

### 11. Documentation ✓
- [ ] README updated with setup instructions
- [ ] API documentation complete
- [ ] Architecture diagram clear
- [ ] Troubleshooting guide accurate
- [ ] Runbook created for on-call team
- [ ] Agent prompts documented
- [ ] Example workflows documented

### 12. Team Readiness ✓
- [ ] Team trained on Bernstein
- [ ] Runbook shared with team
- [ ] Escalation contacts defined
- [ ] On-call rotation configured
- [ ] Communication channels setup (Slack)
- [ ] Backup procedures documented
- [ ] Team performed dry-run practice

---

## Rollback Plan

### Scenario: Critical Bug Introduced

```bash
# 1. Immediate Action: Stop Orchestration
bernstein stop

# 2. Identify Problematic Commit
bernstein log --last-run
git log --oneline -20

# 3. Rollback Option A: Undo Last Commit (If Still in Feature Branch)
bernstein rollback --last
# or
git revert <commit-hash>

# 4. Rollback Option B: Restore From Backup
git reset --hard <known-good-commit>
git push origin main --force-with-lease

# 5. Verify System Health
bernstein test-report --full-suite
bernstein monitor --health

# 6. Restart Orchestrator
bernstein start

# 7. Post-Mortem
# - Document what went wrong
# - Update safeguards
# - Increase test coverage for edge case
```

### Scenario: Agent Misbehavior

```bash
# 1. Identify Misbehaving Agent
bernstein agent status --all
bernstein logs <agent-name> --tail 50

# 2. Temporarily Disable Agent
bernstein agent disable <agent-name>

# 3. Fix Configuration
# Edit .bernstein/orchestrator.yaml
# Correct agent settings

# 4. Restart Agent
bernstein agent restart <agent-name>

# 5. Verify Agent Health
bernstein agent test <agent-name>

# 6. Resume Orchestration
bernstein resume
```

### Scenario: Data Corruption

```bash
# 1. Stop Everything
bernstein stop
git clean -fd

# 2. Restore From Backup
aws s3 cp s3://backups/repo-backup-2026-05-10.tar.gz .
tar -xzf repo-backup-2026-05-10.tar.gz

# 3. Verify Repository Integrity
git fsck --full

# 4. Restore from Backup
git reset --hard backup/main

# 5. Restart
bernstein start
```

### Emergency Contact Protocol

```
Critical Issue: Page on-call engineer
- On-call: +1-555-0100
- Slack: @devops-oncall
- Email: oncall@company.com

Escalation Path:
- Engineer tries fix (15 min)
- → Lead engineer engaged (30 min)
- → Engineering manager engaged (45 min)
- → Director engaged (60 min)
```

---

## Appendix A: Command Reference

```bash
# Initialize
bernstein init
bernstein config set-up

# Agents
bernstein agent list
bernstein agent test <agent-name>
bernstein agent status --all
bernstein agent restart <agent-name>

# Running Tasks
bernstein run --workflow feature_development --task <task>
bernstein run --dry-run --verbose
bernstein status
bernstein pause
bernstein resume
bernstein stop

# Monitoring
bernstein monitor --follow
bernstein logs <agent-name> --tail 100
bernstein test-report --latest
bernstein metrics report --period 24h

# Git Operations
bernstein worktree list
bernstein worktree create-branch --agent <name> --branch <name>
bernstein worktree reset --agent <name>

# Debugging
bernstein conflict show --task <id>
bernstein task show <id>
bernstein task retry <id>
bernstein audit show --last-run

# Management
bernstein cache clear --all
bernstein export --format json --output report.json
bernstein docs generate --output docs/
```

---

## Appendix B: Glossary

| Term | Definition |
|------|-----------|
| **Orchestrator** | Bernstein system managing agent coordination |
| **Agent** | AI model (Nanobot, Gemini, Codex) executing tasks |
| **Worktree** | Isolated git working directory per agent |
| **Task** | Unit of work assigned to agent(s) |
| **Phase** | Sequential step in a workflow |
| **Workflow** | Complete task execution pipeline |
| **Dry-run** | Task execution without actual commits |
| **Audit Trail** | HMAC-signed log of all operations |
| **Merge Conflict** | When agents edit same file differently |
| **QA** | Quality assurance / final validation |

---

## Appendix C: Performance Tuning

```yaml
# For faster execution (less accurate)
optimization: speed
agent_timeout: 15m
test_coverage_min: 70
parallel_limit: 5

# For higher quality (slower)
optimization: quality
agent_timeout: 35m
test_coverage_min: 90
parallel_limit: 2

# Balanced (recommended)
optimization: balanced
agent_timeout: 25m
test_coverage_min: 80
parallel_limit: 3
```

---

## Appendix D: Integration with CI/CD

```yaml
# GitHub Actions Integration
name: Bernstein Auto-Deploy

on:
  issues:
    types: [opened, labeled]

jobs:
  orchestrate:
    if: contains(github.event.issue.labels.*.name, 'auto-orchestrate')
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Setup Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.11'
      - name: Install Bernstein
        run: pip install bernstein-orchestrator
      - name: Run Orchestration
        env:
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
          GEMINI_API_KEY: ${{ secrets.GEMINI_API_KEY }}
        run: |
          bernstein run \
            --workflow feature_development \
            --task "${{ github.event.issue.body }}" \
            --github-issue ${{ github.event.issue.number }}
```

---

**Document End**

*For questions or updates, contact the DevOps team or refer to the troubleshooting section.*
