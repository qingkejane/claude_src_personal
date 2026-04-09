# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is an **educational archive** of Claude Code v2.1.88 source code (unofficial, not affiliated with Anthropic). The purpose is structural analysis and source code reading — not building or running.

## Repository Structure

- `source/package/` — Pre-built distribution (bundled `cli.js`, `source/package.json`)
- `source/unpack/src/` — Extracted TypeScript/TSX source code (~1,900 .ts files, ~550 .tsx files)

No build system, no test framework, no CI/CD. The source is extracted from a production bundle.

## Source Code Architecture (`source/unpack/src/`)

### Entry Points
- `main.tsx` — Application bootstrap and main loop
- `entrypoints/cli.tsx` — CLI entry with fast-path optimization
- `entrypoints/init.ts` — Initialization and config setup

### Core Systems
- **QueryEngine.ts** — Central query processing and orchestration
- **Tool.ts** — Tool definition framework; all operations (file edit, bash, grep, etc.) are tools
- **commands.ts** — Slash command registry (100+ commands like `/commit`, `/review`, `/doctor`)
- **state/AppState.tsx** — React-based application state store
- **context.ts** — Context generation for API requests

### Tool System (`services/tools/`, `tools/`)
30+ tools including: `FileReadTool`, `FileWriteTool`, `FileEditTool`, `BashTool`, `GrepTool`, `GlobTool`, `AgentTool`, `WebSearchTool`, `MCPTool`, `LSPTool`. Each tool follows a consistent definition pattern in `Tool.ts`.

### Key Directories
- `components/` — React (Ink) terminal UI components (permissions, agents, MCP, tasks, UI primitives)
- `services/api/` — Anthropic API integration
- `services/mcp/` — Model Context Protocol client
- `services/lsp/` — Language Server Protocol integration
- `utils/bash/` — Bash command parsing and semantic analysis
- `utils/git/` — Git operations
- `utils/permissions/` — Permission tier management
- `utils/skills/` — Skill/slash-command system
- `hooks/` — ~1,200 React hooks
- `constants/prompts.ts` — System prompt definitions

### Patterns
- **React + Ink** for terminal rendering
- **Tool-based architecture**: every user-facing operation is a tool
- **Feature flags** via `feature()` function for A/B testing and experimental features
- **Agent system**: built-in and custom agents with isolation (worktree support)
- **Plugin/skill system**: extensible via `/skills` and MCP servers
- **Zod** for runtime schema validation throughout
