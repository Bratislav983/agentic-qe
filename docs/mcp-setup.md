# AQE MCP Server Setup

## Installation Status ✅

- **AQE Version:** 3.0.0-alpha.1
- **MCP Server Version:** 3.0.0-alpha.42
- **MCP Tools Registered:** 29
- **Status:** Running successfully

## MCP Server Command

```bash
aqe-mcp
```

## Verification

The MCP server starts successfully and outputs:
```
[agentic-qe-v3] MCP server starting v3.0.0-alpha.42
[MCP] Initializing token tracking...
[MCP] Starting server...
[MCP] Registered 29 tools
[MCP] agentic-qe-v3 v3.0.0-alpha.42 started
[MCP] Ready
```

## Manual MCP Configuration for Claude Code

Since the `claude` CLI command is not available, you need to configure the MCP server manually.

### For Claude Desktop App

Add to `%APPDATA%\Claude\claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "agentic-qe": {
      "command": "aqe-mcp",
      "args": []
    }
  }
}
```

### For Claude Code (VS Code Extension)

The MCP server is available globally via `aqe-mcp`. Claude Code should automatically detect it if properly configured.

Alternatively, you can configure it in VS Code settings:
1. Open VS Code Settings (Ctrl+,)
2. Search for "Claude MCP"
3. Add the server configuration

## Testing the MCP Server

You can test if the MCP server is working:

```bash
# Start the server (it will listen for stdin/stdout communication)
aqe-mcp

# The server should output:
# [MCP] Registered 29 tools
# [MCP] Ready
```

## Available MCP Tools (29 total)

The server provides tools for:
- Test generation and execution
- Coverage analysis
- Quality assessment
- Code intelligence
- Security compliance
- And more QE-specific operations

## Next Steps

1. **Restart Claude Code/VS Code** to detect the new MCP server
2. **Verify MCP server is loaded** in Claude Code's MCP settings
3. **Test using MCP tools** in your conversations

## Troubleshooting

If the MCP server doesn't appear:
1. Ensure `aqe-mcp` is in your PATH
2. Verify installation: `aqe-mcp --version`
3. Check Claude Code logs for MCP-related errors
4. Restart the IDE/application
