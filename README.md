# Perplexity MCP Server

A [FastMCP](https://github.com/jlowin/fastmcp) server that integrates with Perplexity's API to provide web search and grounded AI answers.

## Features

### Three-Tier Research Workflow

1. **`search`** - Ground yourself first
   - Find relevant sources before asking questions
   - Returns URLs, titles, and snippets
   - Use this when you don't know about a topic

2. **`ask`** - Get AI answers (DEFAULT)
   - AI-synthesized answers with web grounding
   - Uses the `sonar` model (fast and cost-effective)
   - Includes citations and optional images/related questions

3. **`ask_more`** - Dig deeper
   - More comprehensive analysis for complex questions
   - Uses the `sonar-pro` model (more capable but pricier)
   - Use when `ask` doesn't provide sufficient depth

## Prerequisites

- Python 3.10 or higher
- A [Perplexity API key](https://www.perplexity.ai/settings/api)
- [uv](https://github.com/astral-sh/uv) (recommended) or pip

## Local Setup

### 1. Install Dependencies

Using uv (recommended):
```bash
uv pip install -e .
```

Or using pip:
```bash
pip install -e .
```

### 2. Configure API Key

Copy the example environment file:
```bash
cp .env.example .env
```

Edit `.env` and add your Perplexity API key:
```
PERPLEXITY_API_KEY=your_api_key_here
```

### 3. Run the Server

Test the server locally:
```bash
uv run fastmcp run server.py
```

Or with the `fastmcp` CLI:
```bash
fastmcp run server.py
```

### 4. Install in Claude Desktop

Install the server for use with Claude Desktop:
```bash
fastmcp install claude-code server.py
```

Or manually add to your Claude Desktop config (`~/Library/Application Support/Claude/claude_desktop_config.json` on macOS):

```json
{
  "mcpServers": {
    "perplexity": {
      "command": "uv",
      "args": ["run", "fastmcp", "run", "/absolute/path/to/server.py"],
      "env": {
        "PERPLEXITY_API_KEY": "your_api_key_here"
      }
    }
  }
}
```

## Cloud Deployment (FastMCP Cloud)

Deploy to [fastmcp.cloud](https://fastmcp.cloud) for easy hosting:

### 1. Push to GitHub

```bash
git init
git add .
git commit -m "Initial commit"
git remote add origin https://github.com/yourusername/perplexity-mcp.git
git push -u origin main
```

### 2. Deploy on FastMCP Cloud

1. Visit [fastmcp.cloud](https://fastmcp.cloud)
2. Sign in with GitHub
3. Create a new project and connect your repo
4. Configure:
   - **Entrypoint**: `server.py`
   - **Environment Variables**: Add `PERPLEXITY_API_KEY`
5. Deploy!

Your server will be available at `https://your-project-name.fastmcp.app/mcp`

FastMCP Cloud automatically:
- ✅ Detects dependencies from `pyproject.toml`
- ✅ Deploys on every push to `main`
- ✅ Creates preview deployments for PRs
- ✅ Handles HTTP transport and authentication

## Tool Usage Guide

### Research Workflow Example

```
1. Don't know about a topic? → Use search()
   search("latest AI research papers on transformers")

2. Found sources? → Use ask() to understand
   ask("What are the key innovations in transformer models?")

3. Need more depth? → Use ask_more()
   ask_more("Explain the mathematical foundations of attention mechanisms in transformers")
```

### Tool Parameters

#### `search(query, max_results=10, recency=None, domain_filter=None)`

- `query`: Search query string
- `max_results`: Number of results (default: 10)
- `recency`: Filter by time - `"day"`, `"week"`, `"month"`, or `"year"`
- `domain_filter`: Include/exclude domains
  - Include: `["wikipedia.org", "github.com"]`
  - Exclude: `["-reddit.com", "-pinterest.com"]`

#### `ask(query, reasoning_effort="medium", ...)`

- `query`: Question to ask
- `reasoning_effort`: `"low"`, `"medium"` (default), or `"high"`
- `search_mode`: `"web"` (default), `"academic"`, or `"sec"`
- `recency`: Time filter
- `domain_filter`: Domain filter
- `return_images`: Include images (default: False)
- `return_related_questions`: Include follow-up questions (default: False)

#### `ask_more(query, reasoning_effort="medium", ...)`

Same parameters as `ask()`, but uses the more powerful `sonar-pro` model.

## Cost Optimization

- **Start with `search`**: Free/cheap way to find sources
- **Default to `ask`**: Uses `sonar` (cost-effective)
- **Escalate to `ask_more`**: Only when needed (more expensive)

## Development

### Project Structure

```
perplexity-mcp/
├── server.py           # Main FastMCP server
├── pyproject.toml      # Dependencies
├── .env.example        # Environment template
└── README.md          # This file
```

### Inspect the Server

See what FastMCP Cloud will see:
```bash
fastmcp inspect server.py
```

## API Reference

This server uses two Perplexity API endpoints:

- **Search API** (`/search`) - Returns ranked search results
- **Chat Completions API** (`/chat/completions`) - Returns AI-generated answers

Supported models:
- `sonar` - Fast, cost-effective
- `sonar-pro` - More comprehensive

## Troubleshooting

### API Key Issues

If you get authentication errors:
1. Verify your API key at https://www.perplexity.ai/settings/api
2. Check that `PERPLEXITY_API_KEY` is set correctly
3. Make sure there are no extra spaces or quotes

### Timeout Errors

If requests timeout:
- The default timeout is 30s for search, 60s for chat
- Complex questions may take longer
- Consider using `reasoning_effort="low"` for faster responses

### Local Testing

Test individual tools:
```bash
uv run fastmcp dev server.py
```

This opens an interactive development interface.

## License

MIT

## Contributing

Contributions welcome! Please open an issue or PR.

## Links

- [Perplexity API Docs](https://docs.perplexity.ai)
- [FastMCP Docs](https://gofastmcp.com)
- [FastMCP Cloud](https://fastmcp.cloud)