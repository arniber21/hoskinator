# Core/AI module split, composed into a single binary

## Module boundary

The engine is two Rust packages with a strict **one-way dependency, `ai` → `core`**:

- **`core`** — pure resume management: the Master Store, git (libgit2, ADR-0004), YAML editing, FTS5, JD storage. Exposes a Rust library API and the core JSON-RPC methods. Contains **no LLM code and reads no API key**; ships and runs entirely on its own.
- **`ai`** — depends on `core`, adds the Anthropic calls and the `score`/`gaps`/`generate` methods. It uses `core`'s public API as a proxy for every read and **every write**, so there is a single validated mutation path and `ai` cannot bypass `core`'s invariants. `core` never references `ai` (enforced at the `Cargo.toml` boundary).

The base product is fully functional with the AI absent; the Web UI feature-detects which methods exist.

## Composition

Everything is packed into **one self-contained binary** as much as possible:

- `ai` is included behind a **cargo feature flag** — built in (with a key present) or absent entirely; no separate AI process.
- The binary is simultaneously the **HTTP daemon**, the **CLI** (subcommands), and the **Web UI host** — the SPA is built separately in JS and its static output is **embedded at compile time** (`rust-embed`) and served from `/`.
- Other-language frontends remain possible over the JSON-RPC contract (ADR-0003), but the shipped unit is this one binary. **Docker is an optional wrapper, not required.**

## Consequences

- A two-stage build: build the web SPA, then embed its output into the Rust build.
- **Rendering/export is optional and feature-detected**, mirroring the AI addon: the engine shells out to external tools when present (rendercv for PDF; pandoc over rendercv's HTML/Markdown for DOCX, since rendercv has no native DOCX) and hides the export buttons when absent. The browser preview is a **self-contained approximation** (embedded web renderer), explicitly allowed to drift from rendercv's real output, which the export buttons produce. The binary stays self-contained; Docker is the batteries-included image that bundles these tools.
