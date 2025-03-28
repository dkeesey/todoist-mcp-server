# MCP Todoist

A Model Context Protocol (MCP) server that enables Claude to interact with your Todoist account.

## Features

- Manage tasks: create, update, complete, and delete tasks
- Organize tasks in projects and with labels
- Search and filter tasks based on various criteria
- Seamless integration with Claude Desktop

## Prerequisites

- Python 3.11+
- [Astral UV](https://docs.astral.sh/uv/installation/)
- [Todoist account](https://todoist.com/) and [API token](https://app.todoist.com/app/settings/integrations/developer)
- Claude Desktop (for using the MCP server)

## Quick Start

### 1. Installation

```bash
# Install using UV
uvx mcp-todoist
```

### 2. Configuration

1. Get your Todoist API token from [Todoist Integrations settings](https://app.todoist.com/app/settings/integrations/developer)

2. Configure the environment variable:
   ```bash
   # Add to your .env file or environment
   TODOIST_API_TOKEN=your_api_token_here
   ```

3. Configure Claude Desktop:
   ```json
   // ~/.config/claude/claude_desktop_config.json or equivalent
   {
     "mcpServers": {
       "mcp-todoist": {
         "command": "uvx",
         "args": ["mcp-todoist"]
       }
     }
   }
   ```

### 3. Using with Claude

Once configured, you can ask Claude to interact with your Todoist account:

- "Show me my tasks due today"
- "Create a new task to buy groceries tomorrow"
- "Mark my 'send email' task as complete"
- "Create a new project called 'Home Renovation'"
- "Show me all tasks in my Work project"

## Available Tools

### Task Management

- `list-tasks` - Retrieve and filter tasks
- `create-task` - Create a new task
- `update-task` - Update an existing task
- `complete-task` - Mark a task as completed
- `delete-task` - Delete a task

### Project Management

- `list-projects` - Get all projects
- `create-project` - Create a new project
- `update-project` - Update a project
- `delete-project` - Delete a project

### Label Management

- `list-labels` - Get all labels
- `create-label` - Create a new label
- `update-label` - Update a label
- `delete-label` - Delete a label

### Utilities

- `search` - Search across tasks with complex filtering

## Running the MCP Server

There are multiple ways to run the Todoist MCP server:

### Method 1: Direct Command Line

Run the server in a terminal window:

```bash
# Set your API token
export TODOIST_API_TOKEN=your_api_token_here

# Run the server using UV
uvx mcp-todoist

# Alternative: Run from source
cd /path/to/mcp-todoist
uv run python -m mcp_todoist
```

Keep this terminal window open while using Claude Desktop.

### Method 2: Using a Startup Script (Recommended)

Create a startup script that Claude Desktop can use to automatically start the server:

1. Create a file named `start-todoist-mcp.sh` with the following content:

```bash
#!/bin/bash

# Set environment variables
export MCP_SERVER_NAME="mcp-todoist"
export MCP_LOG_LEVEL="INFO"
export MCP_DEBUG="true"
export TODOIST_API_TOKEN="your_todoist_api_token_here"

# Path to your Todoist MCP server
MCP_PATH="/path/to/mcp-todoist"

# Log file for debugging
LOG_FILE="${MCP_PATH}/todoist-mcp.log"

# Create log file or clear existing one
echo "Starting Todoist MCP server at $(date)" > "${LOG_FILE}"

# Navigate to the project directory
cd "${MCP_PATH}"

# Start the MCP server
echo "Starting MCP server from ${MCP_PATH}" >> "${LOG_FILE}"
uv run python -m mcp_todoist >> "${LOG_FILE}" 2>&1
```

2. Make the script executable:

```bash
chmod +x start-todoist-mcp.sh
```

3. Update your Claude Desktop configuration to use this script:

```json
{
  "mcpServers": {
    "mcp-todoist": {
      "command": "/absolute/path/to/start-todoist-mcp.sh",
      "args": []
    }
  }
}
```

This approach offers several advantages:
- The server starts automatically with Claude Desktop
- All logs are captured in a file for easier debugging
- Environment variables are set consistently

## Debugging

If you encounter issues with the MCP server, here are some debugging strategies:

### 1. Check the Logs

If using the startup script, check the log file:

```bash
cat /path/to/mcp-todoist/todoist-mcp.log
```

### 2. Enable Debug Mode

Set the `MCP_DEBUG` environment variable to `true` for more verbose logging:

```bash
export MCP_DEBUG=true
uvx mcp-todoist
```

### 3. Verify API Token

Ensure your Todoist API token is correct and still valid:

```bash
# Test the token with a simple curl request
curl -X GET \
  https://api.todoist.com/rest/v2/projects \
  -H "Authorization: Bearer $TODOIST_API_TOKEN"
```

### 4. Use the MCP Inspector

The MCP Inspector is a powerful tool for debugging MCP servers:

```bash
npx @modelcontextprotocol/inspector uvx mcp-todoist
```

This will open a web interface showing all communications between Claude and the MCP server.

### 5. Common Issues and Solutions

- **"MCP Server not available" error**: Ensure the server is running in a separate terminal or via a startup script.
- **Authentication errors**: Check that your Todoist API token is correctly set in your environment.
- **"Command not found" errors**: Make sure Astral UV is installed and in your PATH.
- **Timeout errors**: If your MCP server is slow to respond, try increasing the timeout in Claude Desktop settings.

## Development

### Setup

```bash
# Clone the repository
git clone https://github.com/yourusername/mcp-todoist.git
cd mcp-todoist

# Install dependencies
uv sync
```

### Testing

```bash
# Run tests
uv run pytest
```

### Local Development

For local development, you can create a `.env` file with your Todoist API token:

```
TODOIST_API_TOKEN=your_api_token_here
```

Then run the server:

```bash
uv run python -m mcp_todoist
```

## License

MIT License - see LICENSE file for details.
