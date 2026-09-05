<!--
  NOTE: this file is overwritten by `.github/workflows/deploy-docs.yml`
  ("Copy README as index page") on every documentation-site deploy. Edits
  made here are NOT what readers of the published site see — edit
  README.md instead if you want the site homepage to change. This file
  is kept only so `mkdocs serve` has something to render locally without
  running the CI prep step first.
-->

# apcore Toolkit

The **apcore Toolkit** is a multi-language SDK for scanning, transforming, and registering AI-callable modules in the [apcore](https://github.com/aiperceivable/apcore) ecosystem.

It provides:
- **Module scanning** — discover and extract callable modules from your codebase
- **Output writers** — serialize modules to YAML, Python/TypeScript code, or register directly into an apcore registry
- **Binding loader** — load pre-built `.binding.yaml` files
- **Display overlay** — attach display metadata for richer module presentations
- **AI enhancement** — enrich module metadata using a local SLM

## Language SDKs

| Language | Package | Install |
|---|---|---|
| Python | `apcore-toolkit` | `pip install apcore-toolkit` |
| TypeScript | `apcore-toolkit` | `npm install apcore-toolkit` |
| Rust | `apcore-toolkit` | `cargo add apcore-toolkit` |

## Quick Start

See the [Getting Started](getting-started.md) guide.

## Features

- [Scanning](features/scanning.md)
- [OpenAPI Integration](features/openapi.md)
- [OpenAPI Scanner](features/openapi-scanner.md)
- [Schema Utilities](features/pydantic.md)
- [Output Writers](features/output-writers.md)
- [Binding Loader](features/binding-loader.md)
- [Formatting](features/formatting.md)
- [AI Enhancement](ai-enhancement.md)
- [Display Overlay](features/display-overlay.md)
- [Convention Scanning](features/convention-scanning.md)
- [TUI View Model](features/tui-view-model.md)
