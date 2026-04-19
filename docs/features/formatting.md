# Formatting Utilities

`apcore-toolkit` includes powerful tools for converting complex data structures into formatted, human-readable Markdown. This is especially useful for creating "AI-perceivable" documentation or for logging results in a readable format.

## `to_markdown()`

The `to_markdown()` function converts an arbitrary dictionary or list into a structured Markdown string.

### Features
- **Depth Control**: Specify how many levels deep the conversion should go.
- **Table Heuristics**: Automatically detects when data can be better represented as a Markdown table.
- **Recursive Processing**: Handles nested dictionaries and lists gracefully.

### Example

=== "Python"

    ```python
    from apcore_toolkit import to_markdown

    user_data = {
        "name": "Alice",
        "role": "admin",
        "preferences": {
            "theme": "dark",
            "notifications": True
        },
        "recent_activity": [
            {"action": "login", "timestamp": "2024-03-07T12:00:00Z"},
            {"action": "upload", "timestamp": "2024-03-07T12:05:00Z"}
        ]
    }

    # Convert to Markdown with a title
    md = to_markdown(user_data, title="User Profile")
    print(md)
    ```

=== "TypeScript"

    ```typescript
    import { toMarkdown } from "apcore-toolkit";

    const userData = {
      name: "Alice",
      role: "admin",
      preferences: {
        theme: "dark",
        notifications: true,
      },
      recentActivity: [
        { action: "login", timestamp: "2024-03-07T12:00:00Z" },
        { action: "upload", timestamp: "2024-03-07T12:05:00Z" },
      ],
    };

    // Convert to Markdown with a title
    const md = toMarkdown(userData, { title: "User Profile" });
    console.log(md);
    ```

## Schema Enrichment

The `enrich_schema_descriptions()` utility helps bridge the gap when a JSON Schema lacks parameter descriptions but they are available in a function's docstring.

### Features
- **Description Merging**: Merges descriptions from a dictionary into the `properties` of a JSON Schema.
- **Safe by Default**: Won't overwrite existing descriptions unless explicitly requested.
- **Scanned Integration**: Used by concrete scanners to supplement schemas extracted from source code or OpenAPI with docstring-level documentation.

=== "Python"

    ```python
    from apcore_toolkit import enrich_schema_descriptions

    raw_schema = {
        "type": "object",
        "properties": {
            "user_id": {"type": "integer"}
        }
    }

    param_descriptions = {
        "user_id": "The ID of the user to retrieve."
    }

    # Enrich the schema with parameter descriptions
    enriched = enrich_schema_descriptions(raw_schema, param_descriptions)
    ```

=== "TypeScript"

    ```typescript
    import { enrichSchemaDescriptions } from "apcore-toolkit";

    const rawSchema = {
      type: "object",
      properties: {
        user_id: { type: "integer" },
      },
    };

    const paramDescriptions = {
      user_id: "The ID of the user to retrieve.",
    };

    // Enrich the schema with parameter descriptions
    const enriched = enrichSchemaDescriptions(rawSchema, paramDescriptions);
    ```

## Contract: to_markdown

### Inputs
- `data`: dict or list, required — arbitrary nested data structure to convert
- `title`: string, optional — if provided, prepended as an H1 or H2 header
- `depth`: int, optional, default=1 — heading level to start at (1 = `#`, 2 = `##`, etc.)

### Errors
- None raised — non-serializable values are converted via `str()` / `String()` as a fallback

### Returns
- On success: string — Markdown-formatted representation of the input data

### Properties
- async: false
- pure: true
- thread_safe: true

---

## Contract: enrich_schema_descriptions

### Inputs
- `schema`: dict / `Record<string, unknown>`, required — a JSON Schema object with a `"properties"` key; mutated in place
- `descriptions`: dict[str, str] / `Record<string, string>`, required — mapping of property name → description to inject

### Errors
- None raised — silently skips properties not found in `schema.properties`

### Returns
- Python/Rust: `None` — schema is mutated in place
- TypeScript: `void` — schema is mutated in place

### Properties
- async: false
- pure: false (mutates the schema dict in place)
- overwrite_safe: true — existing `"description"` fields are NOT overwritten (only missing descriptions are filled in)
- thread_safe: true (assuming no concurrent mutation of the same schema dict)

---

## Use Case: AI Documentation

By converting complex internal states to Markdown tables or sections, you provide an LLM with a highly structured and easy-to-parse context. This improves the agent's ability to reason about the system's current state and available actions.
