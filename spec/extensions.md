# OpenTime Extensions

OpenTime supports app-specific metadata through `x_` prefixed fields. This allows implementations to store custom data without breaking interoperability.

## How Extensions Work

Any item can include additional fields prefixed with `x_` followed by an app or vendor name:

```yaml
- type: task
  id: task-1
  title: "Review notes"
  status: todo
  x_myapp:
    custom_field: "any value"
    nested:
      data: "also fine"
```

### Rules

1. **Prefix with `x_`** — All extension fields MUST start with `x_` followed by a namespace (e.g., `x_elysium`, `x_obsidian`).
2. **Preserve on round-trip** — Implementations MUST NOT discard `x_` fields they don't recognize. If an app reads a file with `x_other_app` data, it must write that data back unchanged.
3. **No conflicts** — Each app should only write to its own namespace. Never modify another app's `x_` fields.
4. **Optional by definition** — No `x_` field is ever required. Core functionality must not depend on extension data.

## Registered Prefixes

The following prefixes are in use. Registration is optional but helps avoid collisions.

| Prefix | App / Vendor | Description |
|--------|-------------|-------------|
| `x_elysium` | [Elysium](https://elysium.is) | Focus modes, colors, auto-scheduling, UI metadata |
| `x_obsidian` | [Obsidian](https://obsidian.md) integration | Vault name, source file, line number for round-trip sync |

### Registering a Prefix

If you're building on OpenTime and want to reserve a prefix, open a PR adding your entry to the table above. Include:

- Your `x_` prefix
- App or vendor name (with link)
- Brief description of what you store

## Examples

### Elysium Extensions

```yaml
- type: event
  id: ev-focus
  title: "Deep Work Session"
  start: "2025-12-03T09:00:00+09:00"
  end: "2025-12-03T11:00:00+09:00"
  x_elysium:
    focus_mode: "deep"
    color: "#3B82F6"
    auto_schedule: true
```

### Obsidian Integration

```yaml
- type: task
  id: task-from-vault
  title: "Review project notes"
  status: todo
  x_obsidian:
    vault_name: "My Vault"
    source_file: "Projects/Q1 Planning.md"
    line_number: 42
  x_elysium:
    synced_at: "2026-01-02T10:30:00Z"
```

### Multiple Apps on One File

```yaml
- type: event
  id: ev-shared
  title: "Team Planning"
  start: "2025-12-20T10:00:00+09:00"
  end: "2025-12-20T11:00:00+09:00"
  x_elysium:
    focus_mode: "deep"
    color: "#3B82F6"
  x_custom_app:
    custom_field: "preserved on round-trip"
  x_analytics:
    tracked: true
    category: "planning"
```

Each app reads its own namespace and ignores the rest. All data survives the round-trip.
