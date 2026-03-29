# VS Code fork with MCP Sampling + Tool Calling (SEP-1577)

> **Disclaimer:** This is not an official or production-ready implementation. It was hacked together quickly for a demo video to illustrate how SEP-1577 works in practice. Use at your own risk.

This is a fork of [VS Code](https://github.com/microsoft/vscode) that adds support for [SEP-1577: Sampling with Tools](https://modelcontextprotocol.io/seps/1577--sampling-with-tools).

## What is this?

MCP Sampling lets an MCP Server request LLM completions from the MCP Client. SEP-1577 extends this by allowing the server to include **tools** in those sampling requests — enabling MCP Servers to run their own agentic tool-calling loops.

VS Code claims support for the 2025-11-25 MCP spec (which includes SEP-1577), but the sampling implementation ignores the `tools` and `toolChoice` parameters. This fork adds that missing piece.

## What was changed

**SEP-1577 support** (3 files):
- `mcpServerRequestHandler.ts` — Advertise `sampling: { tools: {} }` capability
- `mcpSamplingService.ts` — Pass tools to the LM, handle `tool_use` responses, return them with `stopReason: "toolUse"`
- `mcpTypes.ts` — Add `progress` to `ISamplingOptions`

**Sampling visualization** (3 files):
- `mcpServer.ts` — Thread `ToolProgress` from tool calls to the sampling service
- `mcpSamplingService.ts` — Report detailed progress (messages, tools, LM responses) as an accumulating bullet list in the chat window
- `mcpTypes.ts` — Type changes for progress threading

VS Code acts as a **pass-through** — it forwards tools to the LM and returns `ToolUseContent` back to the MCP Server. The server executes the tools and sends follow-up sampling requests. VS Code never executes the tools itself.

## Building

Follow the standard [VS Code build instructions](https://github.com/microsoft/vscode/wiki/How-to-Contribute). Requires Node 22, Python, and VS 2022 Build Tools with Spectre-mitigated libs on Windows.

```
npm install
npm run watch
.\scripts\code.bat
```

## Links

- [SEP-1577: Sampling with Tools](https://modelcontextprotocol.io/seps/1577--sampling-with-tools)
- [MCP Specification (2025-11-25)](https://modelcontextprotocol.io/specification/2025-11-25)
