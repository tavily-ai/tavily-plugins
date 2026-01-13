# Tavily Claude Code Plugins

Build AI applications with real-time web data using Tavily's search, extract, crawl, and research APIs.

### Step 1: Add Your API Key

**Tavily API Key Required** — Get your key at https://tavily.com

Add to your Claude Code settings (`~/.claude/settings.json`):

```json
{
  "env": {
    "TAVILY_API_KEY": "tvly-your-api-key-here"
  }
}
``` 

### Step 2: Installation

Run these commands inside Claude Code:

```
/plugin marketplace add tavily-ai/tavily-plugins
/plugin install tavily@tavily-plugins
```

### Step 3: Restart Claude Code

Clear your session and restart to load the plugin:

```
/clear
```

Then press `Ctrl+C` to restart.

### Step 4: Usage

Use the skills by explicitly invoking via slash command, or let Claude decide when to use them:

```
/research
/crawl-url
/tavily-api
```

## Skills

The plugin includes three skills that Claude can use automatically:

| Skill | Description |
|-------|-------------|
| **tavily-api** | Reference documentation for Tavily's search, extract, crawl, and research APIs. Claude uses this automatically when you ask it to integrate Tavily into your code. |
| **research** | CLI tool for AI-synthesized research with citations. Supports structured JSON output. |
| **crawl-url** | Website crawler that saves pages as local markdown files for offline analysis. |

## Usage Examples

Once installed, Claude automatically uses these skills when relevant. Here are some example prompts:

### Web Search Integration
```
"Add Tavily search to my agent so it can look up current information"
"Help me implement web search using the Tavily API"
```

### Research
```
"Research the latest developments in quantum computing"
"Run the research script on AI agent frameworks and save to report.json"
```

### Crawling
```
"Crawl the Stripe API docs and save them locally"
"Download the React documentation for offline reference"
```

## Links

- [Tavily Documentation](https://docs.tavily.com)
- [Tavily Plugins Repository](https://github.com/tavily-ai/tavily-plugins)
- [Tavily Cookbook](https://github.com/tavily-ai/tavily-cookbook)
- [Get API Key](https://tavily.com)

## License

MIT
