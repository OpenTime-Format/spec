# OpenTime Specification v0.2

A human-readable, plain-text format for representing goals, tasks, habits, reminders, events, appointments, and projects.

Format version: `opentime_version: "0.2"`

## 1. Introduction

OpenTime is a plain-text file format designed to do for time what Markdown did for notes. An OpenTime file (with extension `.ot`) describes a collection of items such as goals, tasks, habits, reminders, events, appointments, and projects in a way that is both human-readable and machine-parseable.

This document describes the normative specification for the OpenTime format v0.2. Implementations SHOULD validate against the JSON Schema and MUST NOT remove unknown fields when reading and writing `.ot` files.

## 2. Document Structure

An OpenTime file is a single YAML or JSON document with the following top-level structure:

```yaml
opentime_version: "0.2"        # required string, semantic-style version
default_timezone: "Asia/Tokyo"  # optional IANA timezone name
generated_by: "Elysium 0.3"    # optional free-form string
created_at: "2025-12-02T12:00:00Z"  # optional ISO8601 timestamp
items:
  - type: task
    id: task-1
    title: "Example task"
    status: todo
```

### Document Root Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `opentime_version` | string | Yes | Semantic-style version (e.g., `"0.2"`, `"0.2.1"`) |
| `items` | Item[] | Yes | List of schedule items |
| `default_timezone` | string | No | IANA timezone name for items without explicit timezone |
| `generated_by` | string | No | Application name and version (e.g., `"Elysium 0.3"`) |
| `created_at` | datetime | No | ISO8601 UTC timestamp when file was created |

## 3. Data Types

OpenTime uses a small set of common scalar types across item definitions:

| Type | Format | Example |
|------|--------|---------|
| date | `YYYY-MM-DD` | `"2025-12-03"` |
| datetime | ISO8601 with timezone | `"2025-12-03T09:00:00+09:00"` or `"2025-12-03T00:00:00Z"` |
| time | `HH:MM` (24-hour) | `"09:00"`, `"14:30"` |
| progress | Number 0.0–1.0 | `0.5` (50% complete) |

## 4. Link Object

The Link object is used across all item types for the `links` array field.

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `kind` | string | Yes | `"url"` for external URLs, `"ref"` for internal item references |
| `value` | string | Yes | The URL (if kind is `"url"`) or item ID (if kind is `"ref"`) |

```yaml
links:
  - kind: url
    value: "https://example.com/resource"
  - kind: ref
    value: "task-123"
```

## 5. Item Types

Every entry in `items` is an object with at minimum the fields `type`, `id`, and `title`. The `type` field acts as a discriminator and determines the required and optional fields for that item.

Supported item types: `goal`, `task`, `habit`, `reminder`, `event`, `appointment`, `project`

### 5.1 Goal

Represents a long-term outcome with optional progress tracking.

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `type` | `"goal"` | Yes | Item type discriminator |
| `id` | string | Yes | Unique identifier within the file |
| `title` | string | Yes | Human-readable title |
| `kind` | `"goal"` | Yes | Kind discriminator |
| `target_date` | date | No | Target completion date |
| `progress` | number | No | Progress value from 0.0 to 1.0 |
| `project_id` | string | No | ID of the project this goal belongs to |
| `tags` | string[] | No | Free-form tags for categorization |
| `notes` | string | No | Free-form notes (supports multiline) |
| `links` | Link[] | No | Links to URLs or other items |

### 5.2 Task

Represents an actionable item with status tracking.

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `type` | `"task"` | Yes | Item type discriminator |
| `id` | string | Yes | Unique identifier within the file |
| `title` | string | Yes | Human-readable title |
| `status` | string | Yes | One of: `"todo"`, `"in_progress"`, `"done"`, `"cancelled"` |
| `due` | date | No | Due date (`YYYY-MM-DD`) |
| `scheduled_start` | datetime | No | Scheduled start datetime |
| `estimate_minutes` | integer | No | Estimated time to complete in minutes |
| `actual_minutes` | integer | No | Actual time spent in minutes |
| `priority` | number | No | Priority level (e.g., 1–10) |
| `goal_id` | string | No | ID of the goal this task supports |
| `project_id` | string | No | ID of the project this task belongs to |
| `tags` | string[] | No | Free-form tags for categorization |
| `notes` | string | No | Free-form notes |
| `links` | Link[] | No | Links to URLs or other items |

### 5.3 Habit

Represents a recurring behavior with optional streaks.

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `type` | `"habit"` | Yes | Item type discriminator |
| `id` | string | Yes | Unique identifier within the file |
| `title` | string | Yes | Human-readable title |
| `pattern` | HabitPattern | No | Frequency pattern for the habit |
| `window` | HabitWindow | No | Time window for completing the habit |
| `streak` | HabitStreak | No | Current and longest streak counts |
| `goal_id` | string | No | ID of the goal this habit supports |
| `project_id` | string | No | ID of the project this habit belongs to |
| `tags` | string[] | No | Free-form tags |
| `notes` | string | No | Free-form notes |
| `links` | Link[] | No | Links to URLs or other items |

#### HabitPattern

| Field | Type | Description |
|-------|------|-------------|
| `freq` | string | One of: `"daily"`, `"weekly"`, `"custom"` |
| `days_of_week` | string[] | Days for weekly/custom: `"mon"`, `"tue"`, `"wed"`, `"thu"`, `"fri"`, `"sat"`, `"sun"` |

#### HabitWindow

| Field | Type | Description |
|-------|------|-------------|
| `start_time` | time | Start time in `HH:MM` format |
| `end_time` | time | End time in `HH:MM` format |

#### HabitStreak

| Field | Type | Description |
|-------|------|-------------|
| `current` | integer | Current streak count |
| `longest` | integer | Longest streak ever achieved |

### 5.4 Reminder

Represents a time-triggered notification.

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `type` | `"reminder"` | Yes | Item type discriminator |
| `id` | string | Yes | Unique identifier within the file |
| `title` | string | Yes | Human-readable title |
| `time` | datetime | Yes | When to fire (ISO8601 with timezone) |
| `repeat` | string | No | RRULE-style recurrence rule (e.g., `"FREQ=DAILY;COUNT=5"`) |
| `link` | string | No | ID of related task/event/habit/goal (single reference) |
| `tags` | string[] | No | Free-form tags |
| `notes` | string | No | Free-form notes |
| `links` | Link[] | No | Links to URLs or other items |

> **Note:** Reminder has both a singular `link` field (simple string reference to a related item) and a `links` array (for general URL/ref links).

### 5.5 Event

Represents a time-bound occurrence (meetings, deadlines).

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `type` | `"event"` | Yes | Item type discriminator |
| `id` | string | Yes | Unique identifier within the file |
| `title` | string | Yes | Human-readable title |
| `start` | datetime | Yes | Start datetime |
| `end` | datetime | Yes | End datetime |
| `all_day` | boolean | No | Whether this is an all-day event |
| `timezone` | string | No | IANA timezone name; falls back to `default_timezone` |
| `location` | string | No | Location description |
| `recurrence` | string | No | RRULE-style recurrence rule |
| `goal_id` | string | No | ID of the goal this event supports |
| `project_id` | string | No | ID of the project this event belongs to |
| `tags` | string[] | No | Free-form tags |
| `notes` | string | No | Free-form notes |
| `links` | Link[] | No | Links to URLs or other items |

### 5.6 Appointment

Represents a scheduled commitment with location/attendees.

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `type` | `"appointment"` | Yes | Item type discriminator |
| `id` | string | Yes | Unique identifier within the file |
| `title` | string | Yes | Human-readable title |
| `start` | datetime | Yes | Start datetime |
| `end` | datetime | Yes | End datetime |
| `attendees` | string[] | Yes | Names or emails of attendees (minimum 1) |
| `location` | string | No | Location description |
| `provider` | string | No | Service provider (e.g., `"dentist"`, `"therapist"`, `"Zoom"`) |
| `goal_id` | string | No | ID of the goal this appointment supports |
| `project_id` | string | No | ID of the project this appointment belongs to |
| `tags` | string[] | No | Free-form tags |
| `notes` | string | No | Free-form notes |
| `links` | Link[] | No | Links to URLs or other items |

### 5.7 Project

Represents a container for related items with phases.

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `type` | `"project"` | Yes | Item type discriminator |
| `id` | string | Yes | Unique identifier within the file |
| `title` | string | Yes | Human-readable title |
| `kind` | `"project"` | Yes | Kind discriminator |
| `children` | string[] | No | List of item IDs that belong to this project |
| `progress` | number | No | Progress value from 0.0 to 1.0 |
| `target_date` | date | No | Target completion date |
| `tags` | string[] | No | Free-form tags |
| `notes` | string | No | Free-form notes |
| `links` | Link[] | No | Links to URLs or other items |

## 6. Complete Example

```yaml
opentime_version: "0.2"
default_timezone: "America/New_York"
generated_by: "Elysium 0.3"
created_at: "2025-12-02T12:00:00Z"
items:
  - type: project
    id: proj-1
    title: "Q1 Product Launch"
    kind: project
    children:
      - goal-1
      - task-1
    progress: 0.25
    target_date: "2026-03-31"
    tags:
      - work
      - priority

  - type: goal
    id: goal-1
    title: "Complete MVP"
    kind: goal
    target_date: "2026-02-15"
    progress: 0.5
    project_id: proj-1
    links:
      - kind: url
        value: "https://docs.example.com/mvp-spec"

  - type: task
    id: task-1
    title: "Write API documentation"
    status: in_progress
    due: "2026-01-15"
    estimate_minutes: 120
    goal_id: goal-1
    project_id: proj-1
    priority: 8
    tags:
      - documentation

  - type: habit
    id: habit-1
    title: "Morning meditation"
    pattern:
      freq: daily
    window:
      start_time: "06:00"
      end_time: "08:00"
    streak:
      current: 15
      longest: 30
    goal_id: goal-wellness
    tags:
      - health
      - mindfulness

  - type: event
    id: event-1
    title: "Team standup"
    start: "2026-01-10T09:00:00-05:00"
    end: "2026-01-10T09:30:00-05:00"
    recurrence: "FREQ=WEEKLY;BYDAY=MO,WE,FR"
    location: "Conference Room A"
    project_id: proj-1

  - type: appointment
    id: appt-1
    title: "Dentist checkup"
    start: "2026-01-20T14:00:00-05:00"
    end: "2026-01-20T15:00:00-05:00"
    attendees:
      - "Dr. Smith"
    provider: dentist
    location: "123 Medical Plaza"

  - type: reminder
    id: rem-1
    title: "Review task progress"
    time: "2026-01-10T17:00:00-05:00"
    link: task-1
    repeat: "FREQ=DAILY;COUNT=5"
```

## 7. Extensions and `x_*` Fields

OpenTime is designed to be extensible. Item definitions allow additional properties, and applications are encouraged to place app-specific extensions under a namespaced key starting with `x_` (e.g., `x_elysium`, `x_other_app`).

Implementations MUST NOT discard unknown fields when reading and writing `.ot` files. This allows multiple applications to interoperate on the same file without clobbering each other's data.

```yaml
- type: event
  id: ev_extended
  title: "Event with extensions"
  start: "2025-12-20T10:00:00+09:00"
  end: "2025-12-20T11:00:00+09:00"
  x_elysium:
    focus_mode: "deep"
    color: "#3B82F6"
  x_other_app:
    custom_field: "preserved on round-trip"
```

## 8. Validation and Conformance

The canonical definition of OpenTime v0.2 is provided as a JSON Schema. Implementations SHOULD validate files against this schema in development and may choose to do so in production environments depending on performance constraints.

At minimum, conforming implementations MUST:

- Require `opentime_version` and `items` at the document root
- Require `type`, `id`, and `title` for all items, plus any additional type-specific required fields
- Preserve unknown fields, including `x_*` extension fields, when reading and writing files
- Treat date, datetime, and time fields as opaque strings unless they need to manipulate them semantically; when they do, they SHOULD follow ISO8601 rules

## 9. Resources

- [Quick Reference](./reference.md) — At-a-glance lookup for all fields and values
- [JSON Schema](./schema.json) — Validate your `.ot` files programmatically
- [Examples](../examples/) — Browse example `.ot` files
