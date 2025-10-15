# Documentation Wiki Delegation Example

Complete implementation of the Deli-Gator pattern for Confluence-like documentation systems.

## What This Example Demonstrates

- REST API delegation pattern for documentation/wiki systems
- Secure credential storage (environment variables, 1Password, Keybase)
- Shell wrapper pattern with guard rails
- Sub-agent delegation for 50-70% cost savings
- Complete working example from production use at ExampleJobInc

## Prerequisites

- Claude Code with superpowers framework
- Confluence (or compatible) account with API access
- Bash shell (macOS/Linux)
- jq (JSON processor)
- Optional: 1Password CLI or Keybase

## Installation

### 1. Set Up Credentials

**Option A: Environment Variables** (Quick start)
```bash
export CONFLUENCE_API_TOKEN="your-token"
```

**Option B: 1Password** (Recommended)
```bash
op item create \
  --category=Login \
  --title="Confluence API" \
  --vault=Private \
  credential="your-token"
```

**Option C: Keybase**
```bash
echo "your-token" | keybase fs write /keybase/private/you/confluence-token
```

### 2. Install Shell Wrappers

```bash
# Copy to ~/bin/
cp bin/* ~/bin/
chmod +x ~/bin/wiki-*

# Verify installation
wiki-read --help
```

### 3. Install Superpowers Skill

```bash
# Copy to superpowers skills directory
cp -r skills/delegating-to-wiki-agent \
  ~/.config/superpowers/skills/skills/company/
```

### 4. Install Startup Doc

```bash
# Copy to Claude config
cp setup/startup-doc.md \
  ~/.claude-config/startup-docs/04-wiki-delegation.md
```

## Usage

Start a new Claude session and try:

```
User: "Show me the wiki page about S3 buckets"
```

Expected behavior:
1. ✅ Claude recognizes keyword and delegates
2. ✅ Sub-agent uses wiki-search wrapper
3. ✅ Results returned cleanly
4. ✅ Main context stays < 2KB

## Cost Savings

**Before:** 5-10KB per query (expensive model)
**After:** < 1KB main context, 5-10KB sub-agent (cheap model)
**Savings:** 50-70% per query

## Files Explained

- `bin/` - Shell wrappers with secure credential loading
- `skills/` - Superpowers skill for delegation
- `setup/` - Startup documentation for Claude

## License

MIT License - Use freely, adapt for your needs
