# Getting Started with apcore-toolkit

This guide walks you through installing **apcore-toolkit** and using its core modules to extract metadata from your code and generate AI-perceivable outputs.

## Prerequisites

- **Python**: 3.11+
- **apcore**: 0.9.0+

---

## Installation

```bash
pip install apcore-toolkit
```

---

## Step 1: Create a Custom Scanner

The `BaseScanner` provides the foundation for extracting metadata from any framework.

```python
from apcore_toolkit import BaseScanner, ScannedModule

class MyScanner(BaseScanner):
    def scan(self, **kwargs):
        # Scan your framework endpoints and return ScannedModule instances
        return [
            ScannedModule(
                module_id="users.get_user",
                description="Get a user by ID",
                input_schema={"type": "object", "properties": {"id": {"type": "integer"}}, "required": ["id"]},
                output_schema={"type": "object", "properties": {"name": {"type": "string"}}},
                tags=["users"],
                target="myapp.views:get_user",
            )
        ]

    def get_source_name(self):
        return "my-framework"

scanner = MyScanner()
modules = scanner.scan()
```

---

## Step 2: Filter and Deduplicate

Use built-in utilities to refine your scanned modules.

```python
# Filter modules by ID using regex
modules = scanner.filter_modules(modules, include=r"^users\.")

# Ensure all module IDs are unique
modules = scanner.deduplicate_ids(modules)
```

---

## Step 3: Generate Output

Choose the output format that fits your workflow.

### Option A: YAML Bindings

Generates `.binding.yaml` files for `apcore.BindingLoader`.

```python
from apcore_toolkit import YAMLWriter

writer = YAMLWriter()
writer.write(modules, output_dir="./bindings")
```

### Option B: Python Wrappers

Generates `@module`-decorated Python wrapper files.

```python
from apcore_toolkit import PythonWriter

writer = PythonWriter()
writer.write(modules, output_dir="./generated")
```

### Option C: Direct Registration

Registers modules directly into an active `apcore.Registry`.

```python
from apcore import Registry
from apcore_toolkit import RegistryWriter

registry = Registry()
writer = RegistryWriter()
writer.write(modules, registry)
```

---

## Step 4: Use Schema Utilities

`apcore-toolkit` includes powerful utilities for working with Pydantic and OpenAPI schemas.

### Pydantic Model Flattening

Flatten complex Pydantic models into scalar keyword arguments, perfect for MCP (Model Context Protocol) tools.

```python
from apcore_toolkit import flatten_pydantic_params, resolve_target

# Resolve a target string to a callable
func = resolve_target("myapp.views:create_task")

# Wrap the function to accept flat kwargs
wrapped = flatten_pydantic_params(func)
```

### OpenAPI Extraction

Extract JSON Schemas directly from OpenAPI operation objects.

```python
from apcore_toolkit.openapi import extract_input_schema, extract_output_schema

input_schema = extract_input_schema(operation, openapi_doc)
output_schema = extract_output_schema(operation, openapi_doc)
```

---

## Step 5: Enable AI Enhancement (Optional)

Enhance your metadata using local Small Language Models (SLMs).

1. **Install Ollama** and pull a model (e.g., `qwen:0.6b`).
2. **Configure Environment**:
   ```bash
   export APCORE_AI_ENABLED=true
   export APCORE_AI_MODEL="qwen:0.6b"
   ```
3. **Run your scanner**: Missing descriptions and documentation will be automatically inferred.

See the [AI Enhancement Guide](AI_ENHANCEMENT.md) for more details.

---

## Next Steps

- **[Features Overview](features/overview.md)** — Deep dive into all toolkit capabilities.
- **[AI Enhancement Guide](ai-enhancement.md)** — Strategy for metadata enrichment using SLMs.
- **[Changelog](changelog.md)** — See what's new in the latest release.
- **[apcore Documentation](https://aipartnerup.github.io/apcore/)** — Learn more about the core framework.
