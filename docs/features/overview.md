---
description: "Index of apcore-toolkit features (scanning, OpenAPI, writers, binding loader, formatting, enhancement, display overlay) with the per-SDK parity table."
---

# Features Overview

`apcore-toolkit` is a collection of framework-agnostic utilities designed to help you extract, refine, and export metadata from your existing codebase, making it "AI-Perceivable". Available for **Python**, **TypeScript**, and **Rust**.

## Core Capabilities

| Feature | Description |
|---------|-------------|
| **[Smart Scanning](scanning.md)** | Abstract base classes and utilities for framework-specific scanners, with a 5-phase ability extraction methodology. |
| **[OpenAPI Integration](openapi.md)** | Extract JSON Schemas directly from OpenAPI operation objects. |
| **[Schema Utilities](pydantic.md)** | Flatten complex models (Pydantic / Zod) for easier AI interaction. |
| **[Output Writers](output-writers.md)** | Export metadata to YAML bindings, source code wrappers, or direct Registry registration — with optional output verification. |
| **[Binding Loader](binding-loader.md)** | Parse `.binding.yaml` files back into `ScannedModule` objects — the read-path counterpart to `YAMLWriter`. Supports strict and loose modes for verification, merging, and round-trip workflows. |
| **[Formatting](formatting.md)** | Convert data structures into Markdown, enrich JSON Schema descriptions from docstrings, and render `ScannedModule` for specific consumer surfaces (`format_module` / `format_schema` / `format_modules` — markdown / skill / table-row / json styles). |
| **[AI Enhancement](../ai-enhancement.md)** | Pluggable `Enhancer` protocol with built-in `AIEnhancer` for local SLMs; [apcore-refinery](https://github.com/aiperceivable/apcore-refinery) recommended for production. |
| **[Display Overlay](display-overlay.md)** | Sparse `binding.yaml` overlay that resolves surface-facing alias, description, guidance, and tags into `metadata["display"]` for CLI, MCP, and A2A surfaces. |
| **[Convention Scanning](convention-scanning.md)** | Scan a `commands/` directory of plain Python files for public functions, inferring schemas from type annotations -- zero decorators, zero imports. |
| **[TUI View Model](tui-view-model.md)** | Tier-1 byte-equivalent `TuiViewModel` lifting module-list shape (columns, rows, filter/sort/color-by-tag semantics) into the toolkit. |
| **[OpenAPI Scanner](openapi-scanner.md)** | Turn a whole OpenAPI 3.x document into a `ScannedModule` list — one module per operation — with a byte-identical `module_id` derivation. |

## Proposed Capabilities

These are design proposals with no shipping implementation. They are versioned
in this repository so the contract can be reviewed before any SDK writes code.

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
