---
name: collaborative-progress
description: Team progress management with development planning, risk control, and progress tracking. Use when managing multi-person projects, tracking task ownership, or coordinating branch workflows.
allowed-tools: Read, Grep, Glob, Edit, Write, Bash, TodoWrite, AskUserQuestion
---

# Collaborative Progress Management

Multi-person project coordination using structured markdown files.

## Core Files

Maintain three core files in project root:

```
project/
├── PLAN.md       # Development plan
├── RISKS.md      # Risk control
└── PROGRESS.md   # Progress tracking
```

---

## PLAN.md - Development Plan

Records overall project planning and task breakdown.

### Template

```markdown
# Development Plan

## Project Overview

**Project**: [Project name]
**Goal**: [One-sentence goal]
**Start Date**: YYYY-MM-DD
**Target Date**: YYYY-MM-DD

## Milestones

### M1: [Milestone name] - Target: YYYY-MM-DD

| Task ID | Task | Priority | Estimate | Dependencies |
|---------|------|----------|----------|--------------|
| T001 | [Task description] | P0 | 2d | - |
| T002 | [Task description] | P1 | 1d | T001 |

### M2: [Milestone name] - Target: YYYY-MM-DD

| Task ID | Task | Priority | Estimate | Dependencies |
|---------|------|----------|----------|--------------|
| T003 | [Task description] | P0 | 3d | T001, T002 |

## Out of Scope

- [Items explicitly not doing]
- [Features deferred to next version]

## Technical Decisions

| Decision | Choice | Rationale | Date |
|----------|--------|-----------|------|
| [Decision point] | [Choice] | [Reason] | YYYY-MM-DD |
```

---

## RISKS.md - Risk Control

Identify, assess, and track project risks.

### Template

```markdown
# Risk Management

## Active Risks

| Risk ID | Description | Impact | Probability | Mitigation | Owner | Status |
|---------|-------------|--------|-------------|------------|-------|--------|
| R001 | [Risk description] | High/Med/Low | High/Med/Low | [Mitigation plan] | @name | Open |
| R002 | [Risk description] | Med | Low | [Mitigation plan] | @name | Monitoring |

## Risk Matrix

```
Impact ↑
       │ Low Prob  │ Med Prob  │ High Prob
───────┼───────────┼───────────┼───────────
High   │ Monitor   │ Mitigate  │ CRITICAL
Med    │ Accept    │ Monitor   │ Mitigate
Low    │ Accept    │ Accept    │ Monitor
```

## Resolved Risks

| Risk ID | Description | Resolution | Resolved Date |
|---------|-------------|------------|---------------|
| R000 | [Resolved risk] | [How resolved] | YYYY-MM-DD |

## Blockers

Current blockers (need immediate attention):

- [ ] [Blocker description] - Owner: @name - Since: YYYY-MM-DD
```

---

## PROGRESS.md - Progress Tracking

Real-time task status and team collaboration records.

### Template

```markdown
# Progress Tracking

## Current Sprint: [Sprint Name] (YYYY-MM-DD ~ YYYY-MM-DD)

### Task Board

#### 🔴 Blocked
| Task ID | Task | Owner | Blocker | Since |
|---------|------|-------|---------|-------|

#### 🟡 In Progress
| Task ID | Task | Owner | Branch | Started | ETA | Progress |
|---------|------|-------|--------|---------|-----|----------|
| T001 | [Task desc] | @alice | feat/xxx | 01-20 | 01-22 | 60% - core logic done |
| T002 | [Task desc] | @bob | fix/yyy | 01-21 | 01-21 | 80% - pending test |

#### 🟢 Ready for Review
| Task ID | Task | Owner | PR | Reviewer |
|---------|------|-------|----|---------|
| T003 | [Task desc] | @alice | #123 | @bob |

#### ✅ Done (This Sprint)
| Task ID | Task | Owner | Completed | PR |
|---------|------|-------|-----------|-----|
| T000 | [Task desc] | @bob | 01-19 | #120 |

### Daily Updates

#### YYYY-MM-DD

**@alice**
- T001: Completed API design, started core implementation
- Found R003 risk, logged in RISKS.md

**@bob**
- T002: Fixed edge case bug, preparing PR
- Starting T004 tomorrow

---

## Task Claiming Rules

1. **Claim task**: Add row to In Progress table with Owner and Branch
2. **Update progress**: Update Progress column and Daily Updates daily
3. **Complete task**: Move to Ready for Review or Done
```

---

## Independent Workspace Setup (Multi-Agent)

When multiple agents work on the same codebase simultaneously, each agent MUST create an isolated workspace to prevent conflicts.

### Why Worktree?

Git worktree allows multiple working directories from a single repository. Each agent works in its own directory with its own branch, enabling true parallel development without stepping on each other's toes.

### Naming Convention

Use poetic, evocative names that give each agent a distinct identity:

**Worktree directories** (in parent folder):
```
../worktree_of_wandering_cloud/
../worktree_of_silent_river/
../worktree_of_autumn_leaf/
../worktree_of_morning_dew/
../worktree_of_distant_thunder/
```

**Branch names** (matching the identity):
```
agent/wandering-cloud/feat-xxx
agent/silent-river/fix-yyy
agent/autumn-leaf/refactor-zzz
```

### Setup Before Work

```bash
# 1. Generate a poetic identity (or choose one)
AGENT_NAME="wandering-cloud"  # Be creative: moonlit-path, frozen-pine, etc.

# 2. Create worktree in parent directory
git worktree add "../worktree_of_${AGENT_NAME}" -b "agent/${AGENT_NAME}/workspace"

# 3. Enter your isolated workspace
cd "../worktree_of_${AGENT_NAME}"

# 4. Create feature branch from your workspace branch
git checkout -b "agent/${AGENT_NAME}/feat-your-task"
```

### Workspace Structure

```
parent_directory/
├── main_repo/                      # Original repository
├── worktree_of_wandering_cloud/    # Agent 1's workspace
├── worktree_of_silent_river/       # Agent 2's workspace
└── worktree_of_autumn_leaf/        # Agent 3's workspace
```

### Cleanup After Work

```bash
# After PR is merged, clean up your worktree
cd ../main_repo
git worktree remove "../worktree_of_${AGENT_NAME}"
git branch -d "agent/${AGENT_NAME}/workspace"
```

### Benefits

1. **No file conflicts**: Each agent edits files in a separate directory
2. **Independent staging**: Commits don't interfere with each other
3. **Clean separation**: Easy to track which agent did what
4. **Parallel PRs**: Multiple PRs can be prepared simultaneously

---

## Git Workflow Integration

### Before Starting (Before Creating Branch)

```bash
# 1. Switch to main and pull latest
git checkout main && git pull

# 2. Update PROGRESS.md - claim the task
# Add to "In Progress" table:
# | T001 | Task desc | @yourname | feat/xxx | MM-DD | MM-DD | 0% - starting |

# 3. Commit progress update
git add PROGRESS.md
git commit -m "progress: claim T001 - [brief description]"
git push

# 4. Create feature branch
git checkout -b feat/xxx
```

### During Work

```bash
# Update progress regularly (daily recommended)
git checkout main && git pull
# Update Progress column and Daily Updates in PROGRESS.md
git add PROGRESS.md
git commit -m "progress: T001 update - [brief status]"
git push
git checkout feat/xxx
```

### Before Merge

```bash
# 1. Update PROGRESS.md
# - Move task from "In Progress" to "Ready for Review"
# - Add PR link

# 2. Commit and create PR
git checkout main && git pull
git add PROGRESS.md
git commit -m "progress: T001 ready for review"
git push
git checkout feat/xxx
git push -u origin feat/xxx
gh pr create
```

### After Merge

```bash
# 1. Update PROGRESS.md
# - Move task from "Ready for Review" to "Done"
# - Record completion date

# 2. Commit
git checkout main && git pull
# Update PROGRESS.md
git add PROGRESS.md
git commit -m "progress: T001 completed"
git push
```

---

## Commit Message Convention

Progress-related commits use `progress:` prefix:

```
progress: claim T001 - user auth module
progress: T001 update - 60% complete, API design done
progress: T001 ready for review
progress: T001 completed
progress: add R002 risk - third-party API unstable
progress: resolve R001 - switched to backup solution
```

---

## Quick Actions

### Initialize Project Progress Management

```bash
# Create three core files
touch PLAN.md RISKS.md PROGRESS.md

# Initialize with templates (Claude assisted)
```

### Daily Standup Checklist

1. [ ] Check Blocked tasks in PROGRESS.md
2. [ ] Update In Progress task progress
3. [ ] Review Active Risks in RISKS.md
4. [ ] Sync Daily Updates

### Weekly Review Checklist

1. [ ] Archive this week's Done tasks
2. [ ] Evaluate risk status in RISKS.md
3. [ ] Update Milestones progress in PLAN.md
4. [ ] Plan next week's tasks

---

## Best Practices

1. **Atomic updates**: Update one task status at a time
2. **Timely sync**: Commit to main immediately after status change
3. **Clear descriptions**: Progress column should state what's done, not just percentage
4. **Front-load risks**: Log risks immediately when discovered
5. **Reduce conflicts**: Keep progress commits small and frequent

---

## Integration with First-Principles

Use together with `first-principles-pm`:

1. **Question**: Does each task in PLAN.md really need to exist?
2. **Delete**: Remove unnecessary tasks from PLAN.md
3. **Simplify**: Merge similar tasks, reduce coordination overhead
4. **Accelerate**: Use PROGRESS.md to quickly identify blockers
5. **Automate**: Consider automating progress updates (CI/CD integration)
