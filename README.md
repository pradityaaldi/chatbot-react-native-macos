# chatbot-react-native-macos

A cross-platform AI chatbot built with React Native (macOS + Android) that talks to Claude and calls external tools through a custom **MCP (Model Context Protocol) server**.

## Overview

This project demonstrates an **agentic AI loop**: the app sends a conversation to Claude along with a list of available tools, Claude decides which tools to call, the app executes them via an MCP server, feeds the results back, and repeats until the model produces a final answer.

The included MCP server exposes a sample **recipes** tool, but the architecture works with any tool you register.

## Features

- **React Native app** running on macOS and Android (react-native-macos)
- **Claude integration** (Anthropic Messages API, `claude-3-7-sonnet`) with native tool use
- **Agentic tool-calling loop** — the model autonomously calls tools and iterates until done
- **Custom MCP server** — a standalone TypeScript server built on `@modelcontextprotocol/sdk` that exposes tools to the model
- **Typed end to end** — TypeScript across app and server, Zod-validated tool schemas

## Tech Stack

| Component | Technology |
|-----------|------------|
| App | React Native (macOS + Android), TypeScript |
| AI | Anthropic Claude API (tool use) |
| Tools | MCP server (`@modelcontextprotocol/sdk`) |
| Validation | Zod |

## Architecture

```
React Native app (chat UI)
        │
        ▼
ai.ts  ── getTools() ──▶  MCP client  ──▶  MCP server (recipes tool)
        │                                        │
        └── fetch ──▶ Anthropic Claude API        │
                 (sends tools, gets tool calls) ◀─┘
        │
        ▼
agentic loop: call AI → run tool calls → feed results back → repeat until final answer
```

## How It Works

1. App fetches the tool list from the MCP server (`getTools()`).
2. App sends the conversation + tools to Claude.
3. If Claude requests a tool, the app runs it via `callTool()` and appends the result.
4. The loop continues until Claude returns a final, tool-free response.

## Getting Started

### MCP server

```bash
cd mcp-server
pnpm install
pnpm build
```

### React Native app

```bash
cd reactnativemacos
pnpm install
# add your Anthropic API key in api-key.ts (gitignored)
# macOS
pnpm macos
# Android
pnpm android
```

## Key Implementation Details

- **Tool-calling loop** (`reactnativemacos/ai.ts`): iterates `call AI → execute tools → resubmit` so the model can chain multiple tool calls before answering.
- **MCP server** (`mcp-server/src/index.ts`, `recipes.ts`): registers tools with Zod input schemas using the official MCP SDK — swap in any tool (APIs, DB queries, file ops).

## License

MIT
