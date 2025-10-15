# Monitoring System Delegation Example

Complete implementation of the Deli-Gator pattern for New Relic-like monitoring and observability platforms.

## What This Example Demonstrates

- GraphQL API delegation pattern for monitoring systems
- NRQL (custom query language) support
- Secure credential storage (environment variables, 1Password, Keybase)
- Shell wrapper pattern with guard rails
- Sub-agent delegation for 50-70% cost savings
- Complete working example from production use at ExampleJobInc

## Prerequisites

- Claude Code with superpowers framework
- New Relic (or compatible) account with API access
- New Relic CLI installed (`newrelic`)
- Bash shell (macOS/Linux)
- jq (JSON processor)
- Optional: 1Password CLI or Keybase

## Installation

### 1. Set Up Credentials

**Option A: Environment Variables** (Quick start)
```bash
export NEWRELIC_API_KEY="your-api-key"
export NEWRELIC_ACCOUNT_ID="1234567"
```

**Option B: New Relic CLI Profile**
```bash
newrelic profile add --name company --apiKey YOUR_KEY --accountId 1234567
```

**Option C: 1Password** (Recommended)
```bash
op item create \
  --category=Login \
  --title="New Relic API" \
  --vault=Private \
  credential="your-api-key"
```

### 2. Install Shell Wrappers

```bash
# Copy to ~/bin/
cp bin/* ~/bin/
chmod +x ~/bin/monitoring-*

# Verify installation
monitoring-query --help
```

### 3. Install Superpowers Skill

```bash
# Copy to superpowers skills directory
cp -r skills/delegating-to-monitoring-agent \
  ~/.config/superpowers/skills/skills/company/
```

## Usage

Start a new Claude session and try:

```
User: "Query newrelic for error logs"
User: "Show active alerts"
User: "List monitored applications"
User: "Search logs for 500 errors"
```

Expected behavior:
1. ✅ Claude recognizes keywords (newrelic, monitoring, alerts, logs) and delegates
2. ✅ Sub-agent uses monitoring-* wrappers
3. ✅ Results returned cleanly formatted
4. ✅ Main context stays < 2KB

## Available Operations

- `monitoring-query "NRQL"` - Execute NRQL query
- `monitoring-alerts` - List active alerts
- `monitoring-apps` - List monitored applications
- `monitoring-logs "search"` - Search logs
- `monitoring-patterns` - Analyze log patterns for anomalies

## NRQL Queries

Example queries:
```bash
monitoring-query "FROM Log SELECT count(*) WHERE message LIKE '%error%' SINCE 1 hour ago"
monitoring-query "FROM Transaction SELECT average(duration) FACET appName SINCE 24 hours ago"
```

## Cost Savings

**Before:** 5-10KB per query (expensive model)
**After:** < 1KB main context, 5-10KB sub-agent (cheap model)
**Savings:** 50-70% per query

## Customization

Update account ID in wrappers or set environment:
```bash
export NEWRELIC_ACCOUNT_ID=your-account-id
```

See [SANITIZATION-MAP.md](./SANITIZATION-MAP.md) for full adaptation guide.

## Security

This example demonstrates secure credential storage. **Never hard-code API keys in scripts.**

All shell wrappers support:
- Environment variables
- New Relic CLI profiles
- 1Password CLI
- Keybase secure storage

## License

MIT License - Use freely, adapt for your needs
