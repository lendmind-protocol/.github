<div align="center">
  <img src="/public/assets/banner.png" alt="Lending-Mind Protocol banner" width="1024" />
</div>

<div align="center">
  <p><strong>Make AI-built software respect the architecture.</strong></p>
  <p>A local-first Rust protocol for turning engineering principles into inspectable, repeatable validation.</p>

  <p>
    <a href="#how-it-works">How It Works</a> ·
    <a href="#getting-started">Getting Started</a> ·
    <a href="#contributing">Contributing</a>
  </p>
</div>

---

## Overview

Lending-Mind Protocol (LMP) sits between an AI coding agent and its workspace.
It loads an engineering profile, watches source changes, audits Rust syntax and
code shape, and returns actionable feedback before architectural drift becomes
a merge.

LMP is designed for developers, maintainers, and agent-tool builders who want
engineering preferences to be explicit data instead of undocumented taste.

> [!WARNING]
> LMP is pre-1.0 and local-first. The current synchronization boundary copies
> validated local profiles; OCI/IPFS distribution is not implemented yet. Use
> the current checkout and test results as the source of truth.

**LMP** is a stateful, autonomous protocol engineered to solve the core critique of contemporary AI code generation: syntactic fluency without architectural wisdom. While standard LLMs generate internet-average code that compiles but introduces structural regressions, Lending-Mind establishes a dynamic registry of **Semantic Synchrony Skills**. It moves beyond generic, static system markdown prompts by treating elite human engineering styles as version-controlled, executable blueprints.

The system derives its name from its core capability: it **lends the technical mindset of industry giants** who have spent decades navigating hardware constraints, software lifecycles, and scaling trade-offs. Rather than attempting to prompt an agent with basic style guides, Lending-Mind aggregates an individual creator’s or enterprise team’s complete digital footprint. It ingests their software philosophy, technical methodologies, core architectural beliefs, historical trade-off calculations, code implementations, blogs, and public post-mortems.

By restructuring this multi-dimensional footprint into content-addressed configurations, the protocol enables **generative mimicry** across advanced AI agent networks. Whether inheriting the defensive C-maximalism of the Linux Kernel team or the edge-first, framework-agnostic minimalism of Vercel’s principal engineers, the agent's internal monologue is forced to evaluate code options through that master's technical framework. It explicitly mimics how they handle memory boundaries, optimize routing pathways, or restrict data isolation layers.

Crucially, the Lending-Mind Protocol operates as an evolving, closed-loop engine. When an AI developer agent outputs a patch, a local daemon processes the file through static AST parsing rules and physical sandbox virtualization layers to compute hardware performance metrics. If the code adheres to the selected mind profile, the system creates a signed telemetry attestation. These learned artifacts are dynamically synchronized and pushed back to a decentralized, remote network protocol layer (OCI registries and IPFS). Over time, this pipeline continuously trains and optimizes the mind's behavioral weights. The platform ultimately bridges the gap between AI code generation and human craftsmanship, ensuring autonomous agents deliver production-grade code that reflects real-world engineering wisdom.

## How It Works

LMP follows a small validation loop:

```text
Engineering profile
        │
        ▼
Profile loader ──────────────┐
        │                    │
        ▼                    ▼
   lmpd watcher          lmp-sync copy
        │
        ▼
  Changed Rust file
        │
        ▼
 lmp-core / syn parser
        │
        ▼
  Violation feedback
```

1. Resolve a profile directly or with `--mind-select <alias>`.
2. Load it into the shared `MindSchema`; optional fields receive safe defaults.
3. Receive a source change from `notify`, or an audit request through MCP.
4. Parse the Rust file with `syn`.
5. Check function statement density, async naming, lock/blocking patterns, and
   configured forbidden AST nodes.
6. Return file-specific validation feedback.

## Getting Started

### Prerequisites

- Rust stable toolchain and Cargo
- Python 3.11 or newer for orchestration scripts
- Node.js for the consumer bootstrapper
- An MCP-compatible client for the JSON-RPC adapter

### Installation

Clone the repository and build the Rust workspace:

```bash
git clone https://github.com/lendmind-protocol/LMP.git
cd LMP
cargo build --workspace
```

### Start the workspace daemon

Select a profile from `registry/definitions/` and watch a target directory:

```bash
cargo run --bin lmpd -- \
  --mind-select tj-ponytail \
  --workspace ./crates
```

The daemon stays active and audits modified or newly created `.rs` files.

### Sync a profile locally

```bash
cargo run --bin lmp-sync -- \
  --mind-id tj-ponytail \
  --output-dir ./.lmp_telemetry/minds
```

The profile is loaded through `lmp-core` before it is copied.

### Core components

| Component | Responsibility |
| --- | --- |
| `lmp-core` | Shared profile model, AST audit, skill audit, and signature verification. |
| `lmpd` | Long-running local filesystem watcher. |
| `lmp-mcp` | JSON-RPC stdio adapter using the same core audit path. |
| `lmp-sync` | Local validated profile copy boundary. |
| `mind-signer` | Ed25519 profile signing utility. |
| `orchestrator` | Benchmarks, metrics plots, integration scripts, and AST docs. |

## Profiles

Profiles live in `registry/definitions/`:

| Profile | Purpose |
| --- | --- |
| `tj-ponytail.json` | Minimal dependency and strict TypeScript-oriented skill rules. |
| `tj-holowaychuk-minimalism.json` | Lightweight capability and manifest mutation profile. |
| `style.schema.json` | Naming and formatting preference schema. |

Create a signed profile with:

```bash
cargo run --bin mind-signer -- \
  --input registry/definitions/tj-ponytail.json \
  --output-dir ./.lmp_telemetry/crypto
```

## MCP Integration

Build the adapter as a stdio server:

```bash
cargo build --release --bin lmp-mcp
```

The `enforce_architectural_axioms` tool accepts:

```json
{
  "workspace_path": "/path/to/workspace",
  "mind_alias": "tj-ponytail"
}
```

It recursively audits Rust files and returns the number checked plus every
violation. MCP and `lmpd` call the same `lmp-core::ast::audit_source` function.

### Current verification note

Formatting, workspace metadata, Python/Node syntax, README asset checks, and the
benchmark harness pass in the working environment. Full Cargo compilation may
require a current stable Rust toolchain because Cargo 1.75 cannot parse the
cached `base64ct` release that requires Edition 2024 support.

## Roadmap

- [x] Rust workspace with shared profile and AST validation
- [x] Long-running local workspace daemon
- [x] MCP JSON-RPC audit adapter
- [x] Local profile synchronization command
- [x] Ed25519 profile signing and verification primitives
- [x] Python benchmark and telemetry workflow
- [ ] OCI registry transport with digest verification
- [ ] IPFS distribution adapter
- [ ] Richer language parsers beyond Rust
- [ ] Release artifacts built and verified on a current stable toolchain

## Contributing

Contributions are welcome.

1. Read [CONTRIBUTING.md](https://github.com/lendmind-protocol/LMP/CONTRIBUTING.md) and [CODE_OF_CONDUCT.md](https://github.com/lendmind-protocol/LMP/CODE_OF_CONDUCT.md) before participating.
2. Make the smallest focused change that solves the problem.
3. Keep validation logic in `lmp-core`; do not duplicate it in adapters.
4. Add or update a focused regression test for non-trivial behavior.
5. Run formatting, metadata, targeted tests, and the benchmark harness.
6. Update architecture documentation when a boundary changes.
7. Open a pull request with clear scope and verification evidence.

## License

Lending-Mind Protocol is licensed under the [MIT License](https://github.com/lendmind-protocol/LMP/LICENSE).

See also: [Notices](https://github.com/lendmind-protocol/LMP/NOTICE.md) · [Security Policy](https://github.com/lendmind-protocol/LMP/SECURITY.md) ·

---

<div align="center">
  <sub>Built for developers who want AI assistance without architectural drift.</sub>
</div>
