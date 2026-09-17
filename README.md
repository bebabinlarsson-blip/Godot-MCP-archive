<p align="center">
  <img src="docs/hero.png" alt="Godot MCP — Universal AI Control for Godot" width="720">
</p>

# Godot MCP (Godot Omni)

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Godot Engine](https://img.shields.io/badge/Godot-4.1%20to%204.8+%20(dev)-478CBF?logo=godotengine&logoColor=white)](https://godotengine.org)
[![MCP Protocol](https://img.shields.io/badge/MCP-Model%20Context%20Protocol-8A2BE2)](https://modelcontextprotocol.io)
[![Operations](https://img.shields.io/badge/Operations-1%2C776%20Canonical-brightgreen)](reports/tool-coverage.md)
[![Domains](https://img.shields.io/badge/Domains-59%20Engine%20Domains-success)](reports/tool-coverage.md)
[![Python](https://img.shields.io/badge/Python-3.11%20|%203.12%20|%203.13%20|%203.14-blue?logo=python&logoColor=white)](pyproject.toml)
[![AI Clients](https://img.shields.io/badge/AI%20Clients-12%20Supported-orange)](reports/client-compatibility.md)

**Godot MCP** is the next-generation Model Context Protocol (MCP) server and official editor plugin for the [Godot Engine](https://godotengine.org). It empowers AI coding agents (including Google Antigravity, Claude Code, Cursor, Windsurf, VS Code, Cline, Roo Code, and OpenAI Codex) with complete, native, in-editor omnipotence over Godot.

Where legacy implementations offer ~40 basic operations, Godot MCP provides **1,776 canonical operations** across **59 engine domains**, arbitrary live GDScript evaluation (`godot_eval`), stateful object reflection (`obj://session/id`), semantic UI control tree automation, and adaptive tool exposure.

---

## ⚡ Key Highlights

* 🎮 **1,776 Canonical Engine Operations**: Complete coverage spanning 2D/3D physics, rendering, shaders, materials, skeletal animations, particles, audio buses, tilemaps, gridmaps, CSG modeling, navigation meshes, UI controls, project settings, scene composition, Trail3D, texture streaming, and dynamic ClassDB discovery.
* 🧠 **Adaptive Tool Exposure Engine**: Seamlessly overcomes LLM context bloat and hard tool caps (such as Antigravity's 100-tool limit) using 5 distinct exposure strategies:
  * `AUTO`: Automatically detects connected client capabilities and selects optimal density.
  * `FULL`: Exposes all 1,776 granular tools for agents supporting unbounded tool catalogs.
  * `DOMAIN`: Groups operations into 61 high-level domain rollups (fits comfortably within 100-tool limits).
  * `LAZY`: Provides 33 core operational tools and discovers remaining tools on demand.
  * `ROUTER`: Minimal 4-tool interface (`omni_execute`, `omni_search`, `omni_describe`, `omni_stats`) with sub-millisecond p50 dispatch.
* ⚡ **Arbitrary GDScript Omnipotence (`godot_eval` / `omni_eval`)**: Execute ephemeral `@tool` GDScript directly inside the running Godot editor with full access to `EditorInterface`, `ProjectSettings`, singletons, and ClassDB without leaving residual files.
* 🔍 **Universal Object Reflection (`obj://session/id`)**: Stateful handle management with generational safety counters, cycle detection, and bidirectional serialization across all 38+ Godot Variant types.
* 🖱 **Semantic UI Automation**: Autonomous editor control tree inspection, widget traversal, button clicking, text input, and keyboard shortcut dispatch.
* 🔄 **Broad Version Compatibility**: Zero-regression support from Godot 4.1 through Godot 4.8+ (including active Godot 4.8 development snapshots).
* 🤖 **12 Supported AI Clients**: One-command automatic configuration for Antigravity, Claude Code, Claude Desktop, Cursor, Windsurf, VS Code, Cline, Roo Code, OpenAI Codex, OpenCode, Zed, and Gemini CLI.

---

## 🚀 Quick Start

### 1. Requirements
* **Godot Engine**: 4.1, 4.2, 4.3, 4.4, 4.5, 4.6, 4.7, or 4.8+ (including 4.8 dev snapshots)
* **Python**: 3.11 – 3.14
* **Astral uv** (recommended) or `pip`

---

### 2. Install the Godot Plugin

#### Option A: From Godot Asset Library (Recommended)
1. Open your Godot project.
2. Click the **AssetLib** tab at the top of the editor.
3. Search for **Godot Omni** (or **Godot MCP**) and click **Download** → **Install**.

#### Option B: Manual Installation
Copy the `addons/godot_omni` and `addons/godot_ai` directories from this repository into your Godot project's `res://addons/` directory:

```text
your-godot-project/
└── addons/
    ├── godot_omni/
    │   ├── plugin.cfg
    │   ├── plugin.gd
    │   ├── omni_dock.gd
    │   ├── omni_reflection.gd
    │   └── omni_ui_tree.gd
    └── godot_ai/
        ├── plugin.cfg
        ├── plugin.gd
        └── handlers/
            └── omni_handler.gd
```

#### Option C: Enable the Plugin
In Godot, navigate to:
**Project → Project Settings → Plugins** and enable **Godot Omni** (or **Godot AI**).

The plugin will start the loopback WebSocket server and open the **Godot AI / Omni** dock in the bottom editor panel.

---

### 3. Install the Python MCP Server

```bash
# Clone the repository
git clone https://github.com/<your-org-or-username>/godot-mcp.git
cd "godot-mcp"

# Install with dev dependencies using uv
uv sync --extra dev

# Or install editable via pip
pip install -e .
```

Verify your installation:
```bash
godot-omni self-test
godot-omni tools stats
```

---

### 4. Connect Your AI Client

You can automatically configure your AI client with a single CLI command:

```bash
# Configure all detected clients on your system
godot-omni clients configure all

# Or configure a specific client:
godot-omni clients configure antigravity
godot-omni clients configure claude-code
godot-omni clients configure claude-desktop
godot-omni clients configure cursor
godot-omni clients configure windsurf
godot-omni clients configure vscode
```

#### Manual Client Configuration (JSON)
If you prefer manual configuration, add the following to your client's MCP configuration file (e.g., `claude_desktop_config.json`, `.cursor/mcp.json`, or Antigravity MCP settings):

```json
{
  "mcpServers": {
    "godot-omni": {
      "command": "godot-omni",
      "args": ["attach"]
    }
  }
}
```

---

## 🏛 System Architecture

```text
┌─────────────────────────────────────────────────────────────┐
│                       AI Client                             │
│     (Antigravity / Claude Code / Cursor / Windsurf / ...)   │
└──────────────────────────────┬──────────────────────────────┘
                               │  stdio (FastMCP Protocol)
                               ▼
┌─────────────────────────────────────────────────────────────┐
│                 Godot MCP Python Server                     │
│  ┌───────────────────────────────────────────────────────┐  │
│  │   Adaptive Exposure Engine (AUTO/FULL/DOMAIN/LAZY)    │  │
│  ├───────────────────────────────────────────────────────┤  │
│  │   Canonical Registry: 1,763 Operations / 58 Domains   │  │
│  ├───────────────────────────────────────────────────────┤  │
│  │   Universal Reflection Handle Manager (obj://...)     │  │
│  └───────────────────────────┬───────────────────────────┘  │
└──────────────────────────────┼──────────────────────────────┘
                               │  Loopback WebSocket (Port 9500)
                               │  (Rotating Token Authentication)
                               ▼
┌─────────────────────────────────────────────────────────────┐
│               Godot Editor Plugin (addons/)                 │
│  ┌───────────────────────────────────────────────────────┐  │
│  │   omni_handler.gd (WebSocket In-Editor Dispatch)      │  │
│  ├───────────────────────────────────────────────────────┤  │
│  │   godot_eval (Ephemeral @tool GDScript Execution)     │  │
│  ├───────────────────────────────────────────────────────┤  │
│  │   omni_reflection.gd (Variant ↔ JSON Serialization)   │  │
│  ├───────────────────────────────────────────────────────┤  │
│  │   omni_ui_tree.gd (Semantic Control Automation)       │  │
│  ├───────────────────────────────────────────────────────┤  │
│  │   Godot Engine APIs: ClassDB, EditorInterface, Nodes  │  │
│  └───────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────┘
```

---

## 🛠 Unified CLI Reference

Godot MCP includes a powerful unified CLI (`godot-omni` or `godot-mcp`):

```bash
# 1. Tools & Registry
godot-omni tools stats                       # Summary of 1,763 operations across 58 domains
godot-omni tools search "camera"             # Search operations across all domains
godot-omni tools describe node_create        # Detailed schema, parameters, and Godot class
godot-omni tools list --domain physics_3d    # List all tools in a specific domain
godot-omni tools export schema.json          # Export complete JSON catalog

# 2. Client Management
godot-omni clients detect                    # Detect installed AI clients on host
godot-omni clients status                    # Check MCP registration status across 12 clients
godot-omni clients configure <name>          # Auto-configure specific client
godot-omni clients configure all             # Auto-configure all detected clients
godot-omni clients doctor                    # Validate client configs and report syntax errors

# 3. Performance & Benchmarks
godot-omni benchmark all                     # Run end-to-end performance benchmarks
godot-omni benchmark tools                   # Measure tool lookup & execution latency
godot-omni benchmark memory                  # Monitor memory consumption and handle footprint

# 4. Diagnostics & Health
godot-omni doctor                            # Full diagnostics: Python, Godot, network, registry
godot-omni self-test                         # 8-point in-process integration test
godot-omni versions status                   # Godot 4.1 – 4.8+ compatibility breakdown
```

---

## 📊 Operations Breakdown

Godot MCP classifies its 1,763 operations into 5 architectural layers:

| Layer | Operations | Description |
| :--- | :--- | :--- |
| **Curated Core** | 262 | High-level operations for nodes, scenes, scripts, resources, project settings, files. |
| **ClassDB Generated** | 1,370 | Automatically generated wrappers for Godot engine classes and methods. |
| **UI Automation** | 54 | Full traversal, widget inspection, click, input, and shortcut dispatch for the editor GUI. |
| **Runtime Execution** | 66 | Play-in-editor monitoring, game process lifecycle, runtime capture, test suites. |
| **Universal Reflection** | 11 | Handle resolution, property read/write, method invocation, and variant serialization. |
| **Total** | **1,763** | **58 unique engine domains** |

*For full breakdown and domain catalog, see [reports/tool-coverage.md](reports/tool-coverage.md).*

---

## 🔒 Security & Privacy

* **Local Loopback Isolation**: All network communication occurs strictly over `127.0.0.1` on local loopback sockets. External network access is never opened.
* **Rotating Cryptographic Capabilities**: WebSocket communication between the Python MCP server and the Godot editor uses per-session cryptographic tokens stored with strict `0700` user permissions.
* **Ephemeral GDScript Execution**: Dynamic code evaluated via `godot_eval` runs in memory as transient `@tool` scripts and leaves no untracked residual files.
* **Zero Telemetry Exfiltration**: Code, scene hierarchies, script contents, and file paths are never transmitted. Telemetry can be completely disabled with `GODOT_AI_DISABLE_TELEMETRY=true`.

See [SECURITY.md](SECURITY.md) for vulnerability reporting guidelines.

---

## 🤝 Contributing

Contributions are welcome! Please see [CONTRIBUTING.md](CONTRIBUTING.md) for development environment setup, test guidelines, and PR procedures.

---

## 📄 License

This project is licensed under the [MIT License](LICENSE).
