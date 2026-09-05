---
description: "Pipeline-oriented index of apcore-toolkit capabilities: discovery, schema and metadata refinement, artifacts, runtime registration, presentation, and cross-SDK conformance."
---

# Features Overview

`apcore-toolkit` is a cross-language metadata pipeline for turning framework routes, convention-based functions, or OpenAPI documents into portable `ScannedModule` values, then refining, presenting, exporting, or registering them. Available for **Python**, **TypeScript**, and **Rust**.

## The Toolkit Pipeline

```text
source / OpenAPI → ScannedModule → schema & metadata refinement
                  → presentation or artifact → Registry / HTTP execution
```

Every feature below either produces, transforms, persists, presents, or
executes a `ScannedModule`. Framework-specific route discovery remains owned
by each adapter.

## Core Capabilities

### Discovery

| Feature | Description |
|---------|-------------|
| **[Smart Scanning](scanning.md)** | Shared `ScannedModule` model and `BaseScanner` utilities for framework adapters, including filtering, deduplication, documentation extraction, and behavioral inference. |
| **[Convention Scanning](convention-scanning.md)** | Python-only convention scanner for discovering public functions in a `commands/` directory without decorators or apcore imports. |
| **[OpenAPI Scanner](openapi-scanner.md)** | Turn a complete OpenAPI 3.0/3.1 document into one `ScannedModule` per operation with a byte-identical module-ID algorithm across SDKs. |

### Schema & Metadata

| Feature | Description |
|---------|-------------|
| **[OpenAPI Schema Extraction](openapi.md)** | Operation-level parameter/response extraction and bounded nested `$ref` resolution. This is the reusable primitive consumed by the OpenAPI Scanner. |
| **[Schema Utilities](pydantic.md)** | Model flattening, target resolution, and schema refinement for language-native integrations. |
| **[Display Overlay](display-overlay.md)** | Resolve surface-facing alias, description, guidance, and tags into `metadata["display"]`. |
| **[AI Enhancement](../ai-enhancement.md)** | Pluggable metadata enrichment with a built-in local-SLM enhancer and `apcore-refinery` integration. |

### Artifacts & Runtime

| Feature | Description |
|---------|-------------|
| **[Output Writers](output-writers.md)** | Generate YAML bindings or language-native stubs, register modules directly, or expose remote operations through HTTP proxies; includes output verification contracts. |
| **[Binding Loader](binding-loader.md)** | Read `.binding.yaml` back into `ScannedModule` values, supporting strict/loose modes and writer round trips. |

### Presentation & Interchange

| Feature | Description |
|---------|-------------|
| **[Surface Formatting](formatting.md)** | Render modules and schemas as Markdown, Skill, JSON, table rows, CSV, or JSONL. |
| **[TUI View Model](tui-view-model.md)** | Build a byte-equivalent module-list view model with shared columns, rows, filtering, sorting, grouping, and tag-tone semantics. It is a view model, not a terminal renderer. |

The `format_module(..., style="table-row")` contract and the `TuiViewModel`
wire format serve different consumers and should not be conflated.

## Proposed Capabilities

These are design proposals with no shipping implementation. They are versioned
in this repository so the contract can be reviewed before any SDK writes code.

For navigation purposes, proposals are intentionally separated from shipped
features. The current proposal is [Device Authorization Flow](device-auth.md).

| Proposal | Tracking issue | Summary |
|---|---|---|
| **[Device Authorization Flow](device-auth.md)** | [#17](https://github.com/aiperceivable/apcore-toolkit/issues/17) | RFC 8628 polling state machine, token lifecycle, and portable storage. Protocol only; terminal UI stays in `apcore-cli`. |

## Design Philosophy

- **Framework Agnostic**: The core logic has no dependency on specific web frameworks (Django, Flask, FastAPI).
- **Separation of Concerns**: Scanning (extraction), Schema Utilities (refinement), and Writers (export) are kept distinct.
- **Developer First**: Focuses on automating the tedious tasks of writing `apcore.yaml` or `@module` decorators.
- **AI-Native**: Built with the assumption that the ultimate consumer of this metadata is a Large Language Model (LLM) or AI agent.
- **Cross-Language Parity**: Every core feature has matching implementations in Python, TypeScript, and Rust with idiomatic-per-language APIs and wire-format compatibility.

## SDK Parity

Every core feature listed above ships in all three SDKs (Python, TypeScript, Rust) with idiomatic per-language APIs and wire-format compatibility. A small number of surfaces are intentionally single-language because they solve ecosystem-specific problems that have no direct counterpart elsewhere:

| Surface | SDK | Why it is single-language |
|---|---|---|
| `flatten_pydantic_params` | Python | Unwraps Pydantic models into flat kwargs. TypeScript object arguments are already flat; Rust uses compile-time proc macros. See [`pydantic.md`](pydantic.md). |
| `ConventionScanner` | Python | Uses Python's `importlib` to discover plain `.py` files in `commands/`. No `importlib` analogue exists in TypeScript/Rust. See [`convention-scanning.md`](convention-scanning.md). |
| `_type_mapping` (internal) | Python | Internal helper supporting `flatten_pydantic_params`. Not public. |
| Code-generation writers | `PythonWriter` (Python), `TypeScriptWriter` (TypeScript), `RustWriter` (Rust) | Each generates a language-native stub wired to the scanned metadata. `RustWriter` generates a `.rs` file per module with a `#[module(...)]`-annotated handler stub whose body is `todo!(...)` — a deliberate starting point for the caller to fill in, not a ready-to-run implementation, unlike `PythonWriter`/`TypeScriptWriter` which wrap an existing target function. See [`output-writers.md`](output-writers.md#rustwriter). |
| `safe-keys` (prototype-pollution guard) | TypeScript | JavaScript's prototype-chain semantics create this vulnerability class; Python and Rust are unaffected. |

`HTTPProxyRegistryWriter` ships in all three SDKs (Rust via the `http-proxy` Cargo feature, enabled by default — opt out with `default-features = false` for lean builds) and is documented in the main feature table above; it is not a single-language surface.

## Scope

For a detailed definition of what the toolkit does and does not do, see the [Scope & Boundaries](../scope.md) document.
