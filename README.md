# MCP To LangChain Tools Conversion Utility [![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://github.com/hideya/langchain-mcp-tools-ts/blob/main/LICENSE) [![npm version](https://img.shields.io/npm/v/@h1deya/langchain-mcp-tools.svg)](https://www.npmjs.com/package/@h1deya/langchain-mcp-tools)

This package is intended to simplify the use of MCP server tools within LangChain.

It contains a utility function `convertMcpToLangchainTools()`
that initializes specified MCP servers,
and returns [LangChain Tools](https://js.langchain.com/docs/how_to/tool_calling/)
that wrap all the tools found in given MCP servers.

## Installation

```bash
npm i @h1deya/langchain-mcp-tools
```

## Quick Start

`convertMcpToLangchainTools()` utility function accepts MCP server configuration
that follows the same structure as
[Claude for Desktop](https://modelcontextprotocol.io/quickstart/user),
but only the contents of the `mcpServers` property,
and is expressed as a JS Object, e.g.:

```ts
const mcpServers: McpServersConfig = {
  filesystem: {
    command: 'npx',
    args: [
      '-y',
      '@modelcontextprotocol/server-filesystem',
      '/Users/username/Desktop'  // path to a directory to allow access to
    ]
  },
  fetch: {
    command: 'uvx',
    args: [
      'mcp-server-fetch'
    ]
  }
};

const { tools, cleanup } = await convertMcpToLangchainTools(mcpServers);
```

The utility function initializes all specified MCP servers in parallel,
and returns LangChain Tools (`tools: DynamicStructuredTool[]`)
by gathering all the available MCP server tools,
and by wrapping them into [LangChain Tools](https://js.langchain.com/docs/how_to/tool_calling/).
It also returns `cleanup` callback function
which is used to close all the connections to the MCP servers when finished.

The returned tools can be used with LangChain, e.g.:

```ts
// import { ChatAnthropic } from '@langchain/anthropic';
const llm = new ChatAnthropic({ model: 'claude-3-5-haiku-latest' });

// import { createReactAgent } from '@langchain/langgraph/prebuilt';
const agent = createReactAgent({
  llm,
  tools
});
```
A simple and experimentable usage example can be found
[here](https://github.com/hideya/langchain-mcp-tools-ts-usage/blob/main/src/index.ts)

A more realistic usage example can be found
[here](https://github.com/hideya/langchain-mcp-client-ts)

## Limitations

Currently, only text results of tool calls are supported.
