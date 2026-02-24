# OpenTime

**A human-readable, open format for scheduling and time management.**

Think "Markdown for your calendar." OpenTime is a YAML-based spec for goals, tasks, habits, reminders, events, and appointments — all in plain text files you can read, edit, version control, and own.

```yaml
opentime_version: "0.2"
items:
  - id: goal-1
    type: goal
    title: "Finish Novel Draft"
    target_date: 2026-06-30
    progress: 0.35
    tags: [writing, creative]

  - id: task-1
    type: task
    title: "Write chapter outline"
    status: in_progress
    due: "2026-01-15"
    goal_id: goal-1
```

## Why?

Every calendar and task app stores your schedule in a proprietary database you can't read, move, or trust to exist in five years. OpenTime fixes that.

- **Portable.** It's plain text. Move it anywhere.
- **Readable.** It's YAML. Open it in any editor.
- **Version-controllable.** Track changes with git like you do with code.
- **DRM-free.** Your schedule is yours. No lock-in. No export fees. No API required.

## Schedule Types

OpenTime supports six item types that compose into any workflow:

| Type | Description |
|------|-------------|
| **Goal** | Outcomes with deadlines and progress tracking |
| **Task** | Actions that need to get done |
| **Habit** | Recurring behaviors with streaks |
| **Reminder** | Time-triggered notifications |
| **Event** | Blocks of time on your calendar |
| **Appointment** | Events with attendees |

Items can be nested inside **projects** — containers that hold any combination of types. A client project. A product launch. A semester of study.

## Extensibility

OpenTime supports `x_` prefixed extension fields for app-specific metadata without polluting the core spec:

```yaml
- type: task
  id: task_from_obsidian
  title: "Review project notes"
  status: todo
  x_obsidian:
    vault_name: "My Vault"
    source_file: "Projects/Q1 Planning.md"
    line_number: 42
  x_myapp:
    custom_field: "anything you need"
```

Extensions are optional and ignored by implementations that don't recognize them. This keeps the format interoperable while allowing apps to build rich integrations.

## Spec

The full specification lives in [`spec/`](./spec/).

- [Core Spec](./spec/core.md) — Format definition, item types, and field reference
- [Extensions](./spec/extensions.md) — Extension field conventions and registered prefixes
- [Examples](./examples/) — Sample `.ot` files for common workflows

## Implementations

| App | Platforms | Notes |
|-----|-----------|-------|
| [Elysium](https://elysium.is) | macOS, iOS, iPadOS, watchOS | Reference implementation. Full read/write support with Obsidian integration. |

Building something with OpenTime? Open a PR to add it here.

## File Extension

OpenTime files use the `.ot` extension.

## Contributing

OpenTime is an open spec and contributions are welcome. Whether it's fixing a typo, proposing a new item type, or building a parser — check out [CONTRIBUTING.md](./CONTRIBUTING.md) to get started.

## License

The OpenTime specification is released under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). Build whatever you want with it.

---

*Created by the developer of [Elysium](https://elysium.is). Born out of frustration with proprietary calendars and the belief that your schedule should be as portable as your notes.*
