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

### Testing & Development

> "Open localhost:3000 and verify the signup flow works"

> "Go to the settings page and figure out why the save button isn't working"

> "Test the checkout flow from adding items to payment confirmation"

> "Fill out the contact form and verify the validation messages"

> "Test the search functionality with different query terms"

> "Verify the pagination works correctly on the products page"

> "Check that the modal opens and closes properly"

### Debugging

> "Take a screenshot of the dashboard and tell me what's broken"

> "Navigate to the profile page and inspect why the avatar isn't loading"

> "Check the console for errors on the login page"

> "Inspect the network requests when submitting the form"

> "Find out why the dropdown menu isn't appearing"

> "Debug why the infinite scroll stops loading after a few pages"

### Form Automation

> "Fill out the multi-step registration form with test data"

> "Submit the survey form with random answers"

> "Test all the input validations on the signup form"

> "Upload a file to the document upload form"

> "Select options from all the dropdowns and submit"

### Data Extraction & Scraping

> "Scrape the product listings from this page and save them to a JSON file"

> "Extract all the article titles and links from the blog"

> "Capture the API responses when I scroll through the feed"

> "Get all the prices and product names from this e-commerce page"

> "Extract the table data and convert it to CSV"

> "Scrape all the job listings with their descriptions"

### Navigation & Exploration

> "Navigate through the main menu and list all available pages"

> "Click through the onboarding flow and document each step"

> "Explore the API documentation and find all endpoints"

> "Go through the help center and list all FAQ topics"

### Authenticated Sessions (Chrome Extension)

> "Connect to my Chrome and check my GitHub notifications"

> "Use my logged-in session to navigate to the admin panel"

> "Go to my Twitter profile and get my recent posts"

> "Access my account settings using my logged-in session"

> "Check my order history on the e-commerce site"

> "Navigate to my private dashboard"

### Visual Verification

> "Take a full-page screenshot of the landing page"

> "Compare how the page looks before and after my CSS changes"

> "Scroll through the page and capture screenshots at each section"

> "Take screenshots at different viewport sizes for responsive testing"

> "Capture the hover states of all the buttons"

> "Screenshot the page in both light and dark mode"

### End-to-End Workflows

> "Complete a full user journey from registration to first purchase"

> "Test the entire password reset flow from email to new password"

> "Go through the entire onboarding process as a new user"

> "Simulate a user booking an appointment from start to finish"

> "Test the complete order flow including cancellation"

### Accessibility & Content

> "Check what screen readers would see on the homepage"

> "List all the interactive elements on the page"

> "Verify all images have alt text"

> "Check the heading structure of the page"

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

## License

MIT

## Author

[Sawyer Hood](https://github.com/sawyerhood)
