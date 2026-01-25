# Setting Up btw with Claude Code and Claude Desktop

This guide connects your R session to Claude Code and/or Claude Desktop so they can see your environment, read package documentation, and help you write better R code.

## Prerequisites

- R and RStudio installed
- Claude Code installed (`npm install -g @anthropic-ai/claude-code` or see [docs](https://docs.anthropic.com/en/docs/claude-code)) and/or Claude Desktop installed

## Step 1: Install R Packages

In R/RStudio:

```r
install.packages("btw")
```

## Step 2: Register the MCP Server

### For Claude Code

In your terminal (not R):

```bash
claude mcp add -s "user" r-btw -- Rscript -e "btw::btw_mcp_server()"
```

This registers btw globally so it's available in all projects.

Verify it worked:

```bash
claude mcp list
```

You should see `r-btw` with a checkmark.

### For Claude Desktop

Edit the config file at `~/Library/Application Support/Claude/claude_desktop_config.json` (macOS) and add the `r-btw` entry to the `mcpServers` object:

```json
{
  "mcpServers": {
    "r-btw": {
      "type": "stdio",
      "command": "Rscript",
      "args": [
        "-e",
        "btw::btw_mcp_server()"
      ],
      "env": {}
    }
  }
}
```

If you already have other MCP servers configured, just add the `r-btw` block alongside them.

Restart Claude Desktop after editing the config.

## Step 3: Connect Your R Session

In RStudio, run:

```r
btw::btw_mcp_session()
```

This lets Claude Code see objects in your current R session. Run this each time you start RStudio, or add it to your `.Rprofile` to run automatically:

```r
# Add to ~/.Rprofile
if (interactive()) btw::btw_mcp_session()
```

## Step 4: Test It

1. In RStudio, load some data:
   ```r
   data(mtcars)
   ```

2. In terminal, start Claude Code:
   ```bash
   claude
   ```

3. Ask Claude about your environment:
   ```
   what's in my R environment?
   ```

   Claude should describe `mtcars`.

4. Try asking for package docs:
   ```
   read the docs for dplyr::filter
   ```

## Useful Commands in Claude Code

| Command | Description |
|---------|-------------|
| `/mcp` | List available MCP tools |
| `claude mcp list` | Check MCP server status (run in terminal) |
| `claude mcp remove r-btw` | Remove the btw server |

## Optional: GitHub Integration

To enable btw's GitHub tools, authenticate with:

```r
gitcreds::gitcreds_set()
```

Then paste a personal access token from https://github.com/settings/tokens

## Troubleshooting

**"No sessions configured" or empty environment:**
Make sure you ran `btw::btw_mcp_session()` in your active RStudio session.

**Server not connecting:**
Check that `Rscript` is on your PATH. Run `which Rscript` in terminal to verify.

**Want to restart fresh:**
```bash
claude mcp remove r-btw
claude mcp add -s "user" r-btw -- Rscript -e "btw::btw_mcp_server()"
```
