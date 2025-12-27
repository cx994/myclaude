# /dev + /spec - Spec-Driven Development Workflow

## Overview

A two-phase development workflow integrating lean-spec for spec-driven development:

- **Phase A (`/spec`)**: Planning - requirements clarification, dependency analysis, multi-agent review
- **Phase B (`/dev`)**: Execution - parallel development with 90% coverage enforcement

## Complete Flow

```
/spec "feature description"
  ↓
Search existing specs (avoid duplicates)
  ↓
Requirements clarification (2-3 rounds)
  ↓
Dependency analysis (link to existing specs)
  ↓
Create lean-spec
  ↓
Codex + Gemini review
  ↓
[STOP] Wait for user approval
  ↓
User: "Approve" or /dev @specs/{name}
  ↓
/dev reads spec → parallel execution → 90% coverage
  ↓
Auto-update spec status to complete
```

## Quick Start

```bash
# Phase A: Create and review spec
/spec "实现用户登录功能"

# ... clarification, review, approval checkpoint ...

# Phase B: Execute development
/dev @specs/user-auth
```

## /spec - Phase A (Planning)

### What it does

1. **Discovery**: Search existing specs for duplicates/related work
2. **Clarification**: 2-3 rounds of requirement questions
3. **Dependency Analysis**: Identify upstream/downstream spec dependencies
4. **Create Spec**: Use lean-spec to create structured spec
5. **Multi-Agent Review**: Codex (technical) + Gemini (clarity) review
6. **Approval Checkpoint**: STOP and wait for user approval

### Usage

```bash
/spec "Add payment integration"
```

### Key Features

- **Duplicate detection**: Searches existing specs before creating
- **Dependency linking**: Auto-links `depends_on` relationships
- **Dual review**: Codex for technical soundness, Gemini for clarity
- **Pause & Wait**: Never proceeds to implementation without approval

## /dev - Phase B (Execution)

```
/dev trigger
  ↓
AskUserQuestion (backend selection)
  ↓
AskUserQuestion (requirements clarification)
  ↓
codeagent analysis (plan mode + task typing + UI auto-detection)
  ↓
dev-plan-generator (create dev doc)
  ↓
codeagent concurrent development (2–5 tasks, backend routing)
  ↓
codeagent testing & verification (≥90% coverage)
  ↓
Done (generate summary)
```

## Step 0 + The 6 Steps

### 0. Select Allowed Backends (FIRST ACTION)
- Use **AskUserQuestion** with multiSelect to ask which backends are allowed for this run
- Options (user can select multiple):
  - `codex` - Stable, high quality, best cost-performance (default for most tasks)
  - `claude` - Fast, lightweight (for quick fixes and config changes)
  - `gemini` - UI/UX specialist (for frontend styling and components)
- If user selects ONLY `codex`, ALL subsequent tasks must use `codex` (including UI/quick-fix)

### 1. Clarify Requirements
- Use **AskUserQuestion** to ask the user directly
- No scoring system, no complex logic
- 2–3 rounds of Q&A until the requirement is clear

### 2. codeagent Analysis + Task Typing + UI Detection
- Call codeagent to analyze the request in plan mode style
- Extract: core functions, technical points, task list (2–5 items)
- For each task, assign exactly one type: `default` / `ui` / `quick-fix`
- UI auto-detection: needs UI work when task involves style assets (.css, .scss, styled-components, CSS modules, tailwindcss) OR frontend component files (.tsx, .jsx, .vue); output yes/no plus evidence

### 3. Generate Dev Doc
- Call the **dev-plan-generator** agent
- Produce a single `dev-plan.md`
- Append a dedicated UI task when Step 2 marks `needs_ui: true`
- Include: task breakdown, `type`, file scope, dependencies, test commands

### 4. Concurrent Development
- Work from the task list in dev-plan.md
- Route backend per task type (with user constraints + fallback):
  - `default` → `codex`
  - `ui` → `gemini` (enforced when allowed)
  - `quick-fix` → `claude`
  - Missing `type` → treat as `default`
  - If the preferred backend is not allowed, fallback to an allowed backend by priority: `codex` → `claude` → `gemini`
- Independent tasks → run in parallel
- Conflicting tasks → run serially

### 5. Testing & Verification
- Each codeagent task:
  - Implements the feature
  - Writes tests
  - Runs coverage
  - Reports results (≥90%)

### 6. Complete
- Summarize task status
- Record coverage

## Usage

```bash
/dev "Implement user login with email + password"
```

No CLI flags required; workflow starts with an interactive backend selection.

## Spec Mode (lean-spec Integration)

When using lean-spec for spec-driven development, `/dev` can read requirements from an existing spec and sync status automatically.

### Triggering Spec Mode

```bash
# Option 1: Reference spec directly
/dev @specs/user-auth

# Option 2: Use --spec flag
/dev --spec user-auth

# Option 3: After discussing a spec, say "Approve" or "开始"
```

### Spec Mode Flow

```
lean-spec create user-auth
  ↓
Edit spec (fill in requirements)
  ↓
/dev @specs/user-auth
  ↓
Auto: lean-spec update user-auth --status in-progress
  ↓
Step 0: Confirm backends (skip if spec has allowed_backends)
  ↓
Step 1: Confirm requirements (no multi-round clarification)
  ↓
Step 2-5: Normal execution
  ↓
Step 6: lean-spec update user-auth --status complete
```

### Workflow Adjustments in Spec Mode

| Step | Normal Mode | Spec Mode |
|------|-------------|-----------|
| Step 0 | Ask backends | Use spec's `allowed_backends` or ask |
| Step 1 | 2-3 rounds clarification | Confirm only: "需求完整？" |
| Step 6 | Summary only | Summary + auto update spec status |

### Spec Template for /dev

Add `allowed_backends` to frontmatter for automatic backend selection:

```yaml
---
status: planned
priority: high
allowed_backends: [codex, gemini]  # Optional
---
```

### Output Structure in Spec Mode

```
specs/
├── 001-user-auth/
│   ├── SPEC.md         # lean-spec managed
│   └── dev-plan.md     # /dev generated
```

## Output Structure

```
.claude/specs/{feature_name}/
└──  dev-plan.md      # Dev document generated by agent
```

Only one file—minimal and clear.

## Core Components

### Tools
- **AskUserQuestion**: interactive requirement clarification
- **codeagent skill**: analysis, development, testing; supports `--backend` for `codex` / `claude` / `gemini`
- **dev-plan-generator agent**: generate dev doc (subagent via Task tool, saves context)

## Backend Selection & Routing
- **Step 0**: user selects allowed backends; if `仅 codex`, all tasks use codex
- **UI detection standard**: style files (.css, .scss, styled-components, CSS modules, tailwindcss) OR frontend component code (.tsx, .jsx, .vue) trigger `needs_ui: true`
- **Task type field**: each task in `dev-plan.md` must have `type: default|ui|quick-fix`
- **Routing**: `default`→codex, `ui`→gemini, `quick-fix`→claude; if disallowed, fallback to an allowed backend by priority: codex→claude→gemini

## Key Features

### ✅ Fresh Design
- No legacy project residue
- No complex scoring logic
- No extra abstraction layers

### ✅ Minimal Orchestration
- Orchestrator controls the flow directly
- Only three tools/components
- Steps are straightforward

### ✅ Concurrency
- Tasks split based on natural functional boundaries
- Auto-detect dependencies and conflicts
- codeagent executes independently with optimal backend

### ✅ Quality Assurance
- Enforces 90% coverage
- codeagent tests and verifies its own work
- Automatic retry on failure

## Example

```bash
# Trigger
/dev "Add user login feature"

# Step 0: Select backends
Q: Which backends are allowed? (multiSelect)
A: Selected: codex, claude

# Step 1: Clarify requirements
Q: What login methods are supported?
A: Email + password
Q: Should login be remembered?
A: Yes, use JWT token

# Step 2: codeagent analysis
Output:
- Core: email/password login + JWT auth
- Task 1: Backend API (type=default)
- Task 2: Password hashing (type=default)
- Task 3: Frontend form (type=ui)
UI detection: needs_ui = true (tailwindcss classes in frontend form)

# Step 3: Generate doc
dev-plan.md generated with typed tasks ✓

# Step 4-5: Concurrent development (routing + fallback)
[task-1] Backend API (codex) → tests → 92% ✓
[task-2] Password hashing (codex) → tests → 95% ✓
[task-3] Frontend form (fallback to codex; gemini not allowed) → tests → 91% ✓
```

## Directory Structure

```
dev-workflow/
├── README.md                          # This doc
├── commands/
│   ├── dev.md                         # /dev workflow (Phase B - execution)
│   └── spec.md                        # /spec workflow (Phase A - planning)
└── agents/
    └── dev-plan-generator.md          # Dev plan document generator agent
```

Four files total - minimal and clear.

## When to Use

### Use `/spec` when:
- Starting a new feature that needs planning
- Feature affects multiple parts of the system
- You want Codex/Gemini review before implementation
- You need to understand dependencies with existing work

### Use `/dev` directly when:
- Spec already exists (use `/dev @specs/{name}`)
- Simple, well-defined task with clear requirements
- Bug fix or small change that doesn't need spec

### Skip both when:
- Trivial changes (typo fix, config tweak)
- Single-line fixes

## Design Principles

1. **Two-Phase Separation**: Planning (/spec) and execution (/dev) are distinct
2. **Pause & Wait**: Never auto-proceed to implementation without approval
3. **Dependency Awareness**: Specs are linked, not isolated
4. **Multi-Agent Review**: Codex + Gemini catch different issues
5. **Quality First**: 90% coverage enforced
6. **KISS**: Minimal abstraction, direct workflows

---

**Philosophy**: Plan deliberately, execute fast. Spec-driven development with Linus-style pragmatism.
