# Triage Labels

## Canonical Labels

These labels map to the five triage states. Use them exactly as written.

| State | Label | Description |
|-------|-------|-------------|
| **Needs Triage** | `needs-triage` | Newly created, awaiting evaluation |
| **Needs Info** | `needs-info` | Waiting on reporter for clarification |
| **Ready for Agent** | `ready-for-agent` | Fully specified, agent can work autonomously |
| **Ready for Human** | `ready-for-human` | Requires human implementation or decision |
| **Won't Fix** | `wontfix` | Will not be actioned |

## Priority Labels

| Priority | Label | Description |
|----------|-------|-------------|
| **High** | `priority:high` | Critical path, blocks other work |
| **Medium** | `priority:medium` | Important but not blocking |
| **Low** | `priority:low` | Nice to have, no urgency |

## Type Labels

| Type | Label | Description |
|------|-------|-------------|
| Feature | `type:feature` | New functionality |
| Bug | `type:bug` | Something broken |
| Improvement | `type:improvement` | Enhancement to existing |
| Research | `type:research` | Investigation needed |
| IoT | `type:iot` | Hardware/embedded specific |

## Domain Labels

| Domain | Label | Description |
|--------|-------|-------------|
| Hardware | `domain:hardware` | Physical components |
| Firmware | `domain:firmware` | Embedded software |
| API | `domain:api` | Backend endpoints |
| Dashboard | `domain:dashboard` | Frontend UI |
| RFID | `domain:rfid` | RFID/PDA subsystem |
| Safety | `domain:safety` | ATEX/ADR compliance |

## Application Rules

1. Every issue must have exactly one triage state label
2. Issues can have multiple type/domain labels
3. Priority is optional but recommended
4. When triaging, check `needs-info` issues weekly for response
5. Close `wontfix` issues after 30 days of inactivity
