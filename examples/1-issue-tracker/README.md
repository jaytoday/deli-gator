# Issue Tracker Delegation Example

Complete implementation of the Deli-Gator pattern for Jira-like issue tracking systems.

## What This Example Demonstrates

- REST API delegation pattern for issue tracking
- Secure credential storage (environment variables, 1Password, Keybase)
- Shell wrapper pattern with guard rails
- Sub-agent delegation for 50-70% cost savings
- Complete working example from production use at ExampleJobInc

## Prerequisites

- Claude Code with superpowers framework
- Jira (or compatible) account with API access
- Bash shell (macOS/Linux)
- jq (JSON processor)
- Optional: 1Password CLI or Keybase

## Installation

### 1. Set Up Credentials

**Option A: Environment Variables** (Quick start)
```bash
export JIRA_API_TOKEN="your-token"
export JIRA_EMAIL="you@example.com"
```

**Option B: 1Password** (Recommended)
```bash
op item create \
  --category=Login \
  --title="Jira API" \
  --vault=Private \
  username="you@example.com" \
  credential="your-token"
```

**Option C: Keybase**
```bash
echo "your-token" | keybase fs write /keybase/private/you/jira-token
echo "you@example.com" | keybase fs write /keybase/private/you/jira-email
```

### 2. Install Shell Wrappers

```bash
# Copy to ~/bin/
cp bin/* ~/bin/
chmod +x ~/bin/issuetracker-*

# Verify installation
issuetracker-mine --help
```

### 3. Install Superpowers Skill

```bash
# Copy to superpowers skills directory
cp -r skills/delegating-to-issuetracker-agent \
  ~/.config/superpowers/skills/skills/company/
```

## Usage

Start a new Claude session and try:

```
User: "Show me my open issues"
User: "Search for issues about deployment"
User: "Show details for PROJ-1234"
```

Expected behavior:
1. ✅ Claude recognizes keywords (jira, issue, ticket) and delegates
2. ✅ Sub-agent uses issuetracker-* wrappers
3. ✅ Results returned cleanly formatted
4. ✅ Main context stays < 2KB

## Available Operations

- `issuetracker-mine` - List your assigned issues
- `issuetracker-search "query"` - Search issues by text
- `issuetracker-show ISSUE-123` - Show issue details
- `issuetracker-create` - Create new issue
- `issuetracker-comment ISSUE-123 "text"` - Add comment

## Cost Savings

**Before:** 5-10KB per query (expensive model)
**After:** < 1KB main context, 5-10KB sub-agent (cheap model)
**Savings:** 50-70% per query

## Customization

Update `BASE_URL` in shell wrappers:
```bash
BASE_URL="https://yourcompany.atlassian.net"
```

See [SANITIZATION-MAP.md](./SANITIZATION-MAP.md) for full adaptation guide.

## License

MIT License - Use freely, adapt for your needs
