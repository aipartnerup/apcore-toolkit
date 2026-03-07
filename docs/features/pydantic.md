# Pydantic Utilities

Modern Python web frameworks (like FastAPI and Django Ninja) use Pydantic models for request and response validation. `apcore-toolkit` provides utilities to bridge the gap between structured Pydantic models and the flat interface often required by AI tools.

## Model Flattening

The `flatten_pydantic_params()` function wraps a Python function, converting its Pydantic model parameters into flat, scalar keyword arguments.

### Why Flatten?

AI agents and protocols like MCP (Model Context Protocol) interact best with flat schemas. A function like `create_task(body: TaskCreate)` is difficult for an AI to call because it needs to understand the `TaskCreate` structure. By flattening, the AI sees `create_task(title: str, description: str, ...)` directly.

### Example

```python
from pydantic import BaseModel
from apcore_toolkit import flatten_pydantic_params

class UserCreate(BaseModel):
    username: str
    email: str

def create_user(body: UserCreate):
    return f"Created {body.username}"

# Wrap the function
flat_create_user = flatten_pydantic_params(create_user)

# Now it can be called with flat kwargs
result = flat_create_user(username="jdoe", email="joe@example.com")
```

## Metadata Preservation

When flattening, `apcore-toolkit` preserves:
- **Type Hints**: Original field types are maintained in the wrapper's signature.
- **Field Metadata**: Descriptions, examples, and `json_schema_extra` from the Pydantic model are converted into `Annotated` metadata on the wrapper's parameters.

## Target Resolution

The `resolve_target()` function resolves a string like `"myapp.views:create_task"` into the actual Python callable. This is essential for dynamically loading view functions referenced in metadata files.

```python
from apcore_toolkit import resolve_target

# Dynamically load a callable
view_func = resolve_target("myapp.api.v1.users:create_user")
# view_func is now the actual function from myapp
```
