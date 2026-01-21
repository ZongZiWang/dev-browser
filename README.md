<p align="center">
  <img src="assets/header.png" alt="Dev Browser - Browser automation for Claude Code" width="100%">
</p>

A browser automation plugin for [Claude Code](https://docs.anthropic.com/en/docs/claude-code) that lets Claude control your browser to test and verify your work as you develop.

**Key features:**

- **Persistent pages** - Navigate once, interact across multiple scripts
- **Flexible execution** - Full scripts when possible, step-by-step when exploring
- **LLM-friendly DOM snapshots** - Structured page inspection optimized for AI

## Prerequisites

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) CLI installed
- [Node.js](https://nodejs.org) (v18 or later) with npm

## Installation

### Claude Code

```
/plugin marketplace add sawyerhood/dev-browser
/plugin install dev-browser@sawyerhood/dev-browser
```

Restart Claude Code after installation.

### Amp / Codex

Copy the skill to your skills directory:

```bash
# For Amp: ~/.claude/skills | For Codex: ~/.codex/skills
SKILLS_DIR=~/.claude/skills  # or ~/.codex/skills

mkdir -p $SKILLS_DIR
git clone https://github.com/sawyerhood/dev-browser /tmp/dev-browser-skill
cp -r /tmp/dev-browser-skill/skills/dev-browser $SKILLS_DIR/dev-browser
rm -rf /tmp/dev-browser-skill
```

**Amp only:** Start the server manually before use:

```bash
cd ~/.claude/skills/dev-browser && npm install && npm run start-server
```

### Chrome Extension (Optional)

The Chrome extension allows Dev Browser to control your existing Chrome browser instead of launching a separate Chromium instance. This gives you access to your logged-in sessions, bookmarks, and extensions.

**Installation:**

1. Download `extension.zip` from the [latest release](https://github.com/sawyerhood/dev-browser/releases/latest)
2. Unzip the file to a permanent location (e.g., `~/.dev-browser-extension`)
3. Open Chrome and go to `chrome://extensions`
4. Enable "Developer mode" (toggle in top right)
5. Click "Load unpacked" and select the unzipped extension folder

**Using the extension:**

1. Click the Dev Browser extension icon in Chrome's toolbar
2. Toggle it to "Active" - this enables browser control
3. Ask Claude to connect to your browser (e.g., "connect to my Chrome" or "use the extension")

When active, Claude can control your existing Chrome tabs with all your logged-in sessions, cookies, and extensions intact.

## Permissions

To skip permission prompts, add to `~/.claude/settings.json`:

```json
{
  "permissions": {
    "allow": ["Skill(dev-browser:dev-browser)", "Bash(npx tsx:*)"]
  }
}
```

Or run with `claude --dangerously-skip-permissions` (skips all prompts).

## Usage

Just ask Claude to interact with your browser:

### Testing & Debugging

> "Open localhost:3000 and verify the signup flow works"

> "Go to the settings page and figure out why the save button isn't working"

> "Test the login form with invalid credentials and check the error messages"

> "Navigate through the checkout flow and make sure the total updates correctly"

### Form Automation

> "Fill out the contact form on the about page and submit it"

> "Log into my dev account and check the dashboard"

> "Complete the multi-step registration wizard"

### Visual Verification

> "Take a screenshot of the homepage on desktop and mobile viewports"

> "Check if the responsive menu works correctly on smaller screens"

> "Verify the dark mode toggle changes all the colors properly"

### Data Extraction

> "Scrape the product listings from the search results page"

> "Extract all the comments from this article"

> "Get the data from the table on this page and save it as JSON"

### Navigation & Exploration

> "Go to the admin panel and show me what options are available"

> "Find the user profile page and tell me what fields are editable"

> "Navigate to the API documentation and summarize the endpoints"

### Integration Testing

> "Test the OAuth login flow with Google"

> "Verify the webhook is triggered when I submit this form"

> "Check that the real-time notifications appear when events are fired"

## Benchmarks

| Method                  | Time    | Cost  | Turns | Success |
| ----------------------- | ------- | ----- | ----- | ------- |
| **Dev Browser**         | 3m 53s  | $0.88 | 29    | 100%    |
| Playwright MCP          | 4m 31s  | $1.45 | 51    | 100%    |
| Playwright Skill        | 8m 07s  | $1.45 | 38    | 67%     |
| Claude Chrome Extension | 12m 54s | $2.81 | 80    | 100%    |

_See [dev-browser-eval](https://github.com/SawyerHood/dev-browser-eval) for methodology._

### How It's Different

| Approach                                                         | How It Works                                      | Tradeoff                                               |
| ---------------------------------------------------------------- | ------------------------------------------------- | ------------------------------------------------------ |
| [Playwright MCP](https://github.com/microsoft/playwright-mcp)    | Observe-think-act loop with individual tool calls | Simple but slow; each action is a separate round-trip  |
| [Playwright Skill](https://github.com/lackeyjb/playwright-skill) | Full scripts that run end-to-end                  | Fast but fragile; scripts start fresh every time       |
| **Dev Browser**                                                  | Stateful server + agentic script execution        | Best of both: persistent state with flexible execution |

## How It Works

Dev Browser consists of three main components:

1. **Persistent Server** - Launches Chromium with `launchPersistentContext`, preserving cookies and localStorage across sessions. Pages are registered by name and persist until explicitly closed.

2. **Client Library** - Connects to the server via CDP (Chrome DevTools Protocol) and returns standard Playwright `Page` objects for automation.

3. **ARIA Snapshots** - Generates LLM-friendly accessibility trees for element discovery, making it easy for Claude to understand and interact with page elements.

```
┌─────────────────┐     HTTP/CDP     ┌─────────────────┐
│  Claude Code    │◄───────────────►│  Dev Browser    │
│  (runs scripts) │                  │  Server         │
└─────────────────┘                  └────────┬────────┘
                                              │
                                              ▼
                                     ┌─────────────────┐
                                     │  Chromium       │
                                     │  (persistent)   │
                                     └─────────────────┘
```

## Troubleshooting

### Server won't start

```bash
# Check if port 9222 is already in use
lsof -i :9222

# Kill existing process if needed
kill -9 $(lsof -t -i :9222)
```

### Browser not launching

Make sure Playwright browsers are installed:

```bash
cd skills/dev-browser && npx playwright install chromium
```

### Extension not connecting

1. Ensure the extension is toggled to "Active" in Chrome
2. Check that the relay server is running (`npm run start-extension`)
3. Look for "Extension connected" message in the console

### Scripts timing out

- Use `waitForPageLoad(page)` after navigation
- Use `page.waitForSelector()` for dynamic content
- Increase timeout: `page.waitForSelector('.element', { timeout: 10000 })`

### Permission errors

Add the required permissions to `~/.claude/settings.json` (see [Permissions](#permissions) section).

## FAQ

**Q: Can I use Dev Browser with sites that require login?**

A: Yes! Use the Chrome extension to control your existing browser with all your logged-in sessions intact. Or use standalone mode which preserves cookies across sessions.

**Q: Does it work with SPAs (Single Page Applications)?**

A: Yes. Dev Browser handles SPAs well. Use `waitForPageLoad()` after navigation and `page.waitForSelector()` for dynamically loaded content.

**Q: Can I run multiple browsers simultaneously?**

A: The server manages a single browser context, but you can have multiple named pages open at once. Use descriptive names like `"checkout"`, `"admin"`, `"profile"`.

**Q: How do I handle popups and new tabs?**

A: Use Playwright's standard popup handling:

```typescript
const [popup] = await Promise.all([
  page.waitForEvent('popup'),
  page.click('a[target="_blank"]')
]);
```

**Q: Is my data safe?**

A: Dev Browser runs locally on your machine. No data is sent to external servers. The Chrome extension only activates when you explicitly enable it.

## Contributing

Contributions are welcome! Here's how to get started:

```bash
# Clone the repository
git clone https://github.com/sawyerhood/dev-browser
cd dev-browser/skills/dev-browser

# Install dependencies
npm install

# Run in development mode
npm run dev

# Run tests
npm test

# Type check
npx tsc --noEmit
```

### Development Guidelines

- Use Node.js/npm (not Bun)
- Use `import type { ... }` for type-only imports
- Run `npm test` and `npx tsc --noEmit` before submitting PRs
- Keep scripts small and focused

## License

MIT

## Author

[Sawyer Hood](https://github.com/sawyerhood)
