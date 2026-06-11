---
name: kimi-code-playwright-mcp-installation
description: Step-by-step guide to install Playwright MCP server, set up browsers, and configure it in Kimi Code for browser automation.
---

# Playwright MCP Setup Guide

Complete guide to install and configure the Playwright MCP server for Kimi Code browser automation.

## Prerequisites

- Node.js 18+ and npm installed
- Kimi Code CLI installed (`kimi` command available)

## 1. Install Playwright MCP Server

Install the MCP server globally using npm:

```bash
npm install -g @executeautomation/playwright-mcp-server
```

Verify the installation:

```bash
playwright-mcp-server --help
```

Expected output:

```
Playwright MCP Server

USAGE:
  playwright-mcp-server [OPTIONS]

OPTIONS:
  --port <number>    Run in HTTP mode on the specified port
  --help, -h         Show this help message
```

## 2. Install Playwright Browsers

The MCP server needs browser binaries. Install them with:

```bash
# Install all browsers (Chromium, Firefox, WebKit)
npx playwright install

# Or install specific browsers only
npx playwright install chromium
npx playwright install firefox
npx playwright install webkit
```

Browsers are cached at:
- **Linux/macOS**: `~/.cache/ms-playwright`
- **Windows**: `%USERPROFILE%\AppData\Local\ms-playwright`

Verify browser installation:

```bash
ls ~/.cache/ms-playwright/
```

You should see directories like `chromium-XXXX`, `firefox-XXXX`, `webkit-XXXX`.

## 3. Configure Kimi Code MCP

Create or edit the user-global MCP configuration file at `~/.kimi-code/mcp.json`:

```bash
mkdir -p ~/.kimi-code
```

Add the Playwright MCP server entry:

```json
{
  "mcpServers": {
    "playwright": {
      "command": "npx",
      "args": ["-y", "@executeautomation/playwright-mcp-server"]
    }
  }
}
```

Save the file. Kimi Code loads MCP servers at **session start**, so you must start a **new session** for changes to take effect.

### Alternative: Project-Local Config

For project-specific setup, create `.kimi-code/mcp.json` inside your project directory instead:

```json
{
  "mcpServers": {
    "playwright": {
      "command": "npx",
      "args": ["-y", "@executeautomation/playwright-mcp-server"]
    }
  }
}
```

### Alternative: HTTP Mode

If you prefer running the server as a background HTTP service:

```bash
# Start the server on port 8931
npx @executeautomation/playwright-mcp-server --port 8931
```

Then configure Kimi Code with:

```json
{
  "mcpServers": {
    "playwright": {
      "url": "http://localhost:8931/mcp",
      "type": "http"
    }
  }
}
```

## 4. Verify Configuration

Start a new Kimi Code session:

```bash
kimi
```

Inside the session, list configured MCP servers:

```
/mcp-config
```

You should see `playwright` listed as an active MCP server.

## 5. Available Tools

Once connected, the Playwright MCP server exposes these tools:

| Tool | Description |
|------|-------------|
| `playwright_navigate` | Open a URL in the browser |
| `playwright_screenshot` | Capture a screenshot of the page |
| `playwright_click` | Click an element by selector |
| `playwright_fill` | Fill a form input field |
| `playwright_evaluate` | Execute JavaScript in the browser context |
| `playwright_get_visible_text` | Extract visible text from the page |
| `playwright_resize` | Resize viewport or emulate a device (143 presets) |
| `playwright_generate_test` | Generate Playwright test code from actions |

## 6. Usage Examples

### Open a website and take a screenshot

> Navigate to https://example.com and take a screenshot

### Extract page content

> Go to https://news.ycombinator.com and summarize the top 5 headlines

### Fill a form

> Open https://example.com/login, fill in username "admin" and password "secret", then click the login button

### Emulate a mobile device

> Open https://example.com in iPhone 13 viewport and take a screenshot

### Generate a test

> Navigate to https://example.com, click the "Get Started" button, fill in the signup form, then generate a Playwright test for this flow

## 7. Troubleshooting

### Browser not found error

If you see browser-related errors, reinstall browsers:

```bash
npx playwright install --force
```

### MCP server not appearing in Kimi Code

- Ensure `~/.kimi-code/mcp.json` is valid JSON
- Start a **new** Kimi Code session (MCP servers load at session start)
- Check server status with `/mcp-config`

### stdio mode hangs or disconnects

Switch to HTTP mode for better stability in long sessions:

```bash
npx @executeautomation/playwright-mcp-server --port 8931
```

Then update `mcp.json` to use `"url"` and `"type": "http"`.

### Permission errors on Linux

Install system dependencies if needed:

```bash
npx playwright install-deps chromium
```

## References

- [Playwright MCP Server Docs](https://executeautomation.github.io/mcp-playwright/)
- [MCP Protocol Specification](https://modelcontextprotocol.io/)
- [Playwright Documentation](https://playwright.dev/)
