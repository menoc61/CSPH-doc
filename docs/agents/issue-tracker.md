# Issue Tracker Configuration

## Tracker Type

**Local markdown files** under `.scratch/` directory.

## Structure

```
.scratch/
├── features/          # Feature requests
├── bugs/              # Bug reports
├── improvements/      # Code improvements
├── research/          # Technical research
└── iot/               # IoT-specific issues
```

## File Naming Convention

```
.scratch/{category}/{short-id}-{slug}.md
```

Example: `.scratch/features/001-add-rfid-validation.md`

## Issue Template

```markdown
---
id: {auto-generated}
title: {Issue title}
type: feature|bug|improvement|research|iot
status: needs-triage
priority: high|medium|low
labels: []
created: {ISO 8601 date}
---

# {Title}

## Description

{Detailed description of the issue}

## Acceptance Criteria

- [ ] Criterion 1
- [ ] Criterion 2
- [ ] Criterion 3

## Technical Notes

{Any technical context, hardware requirements, or constraints}

## Related

- {Links to related issues or documentation}
```

## Workflow

1. **Create:** Add new `.md` file in appropriate category
2. **Triage:** Apply labels, set priority, assign status
3. **Work:** Agent picks up `ready-for-agent` issues
4. **Review:** Human reviews completed work
5. **Close:** Move to archive or delete

## Integration with Skills

- `to-issues` creates new issue files
- `triage` moves issues through states
- `diagnose` reads issues for context
- `improve-codebase-architecture` finds related issues
