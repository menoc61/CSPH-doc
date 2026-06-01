# Domain Documentation Layout

## Structure

This project uses a **single-context** layout.

```
workspace/
├── CONTEXT.md                    # Domain language & glossary
├── AGENTS.md                     # Agent configuration
├── docs/
│   ├── SystemDesignDoc.md        # Main technical document
│   ├── adr/                      # Architecture Decision Records
│   └── agents/                   # Agent configuration files
│       ├── issue-tracker.md
│       ├── triage-labels.md
│       └── domain.md
└── .scratch/                     # Issue tracker
```

## Reading Rules

### For Domain Understanding

1. Start with `CONTEXT.md` at root - contains glossary and business rules
2. Read `docs/SystemDesignDoc.md` for technical specifications
3. Check `docs/adr/` for past architectural decisions

### For Code Tasks

1. Read `AGENTS.md` for project conventions
2. Check `CONTEXT.md` for domain terminology
3. Review relevant sections of `SystemDesignDoc.md`

### For IoT/Hardware Tasks

1. Read `CONTEXT.md` hardware terms
2. Review section 3.x of `SystemDesignDoc.md`
3. Check ADRs for component decisions

## Writing Rules

- Use French for domain documentation (industry standard for Cameroon)
- Use English for code comments and API documentation
- Metric units throughout
- ISO 8601 timestamps
- UTC timezone for server operations

## ADR Template

Place new ADRs in `docs/adr/` with the naming convention:

```
docs/adr/{NNN}-{title-slug}.md
```

Example: `docs/adr/001-chose-adam-6717-gateway.md`
