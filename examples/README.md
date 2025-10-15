# Deli-Gator Examples

Complete, production-ready implementations of the delegation pattern for different types of services.

All examples are sanitized from real production use at ExampleJobInc and demonstrate secure, cost-effective patterns for AI agent delegation.

## Available Examples

### 1. Issue Tracker (Jira-like)
**Pattern:** REST API with token authentication  
**Operations:** Search, read, list, update issues  
**Best for:** Project management, bug tracking systems  
**[→ See full example](1-issue-tracker/)**

### 2. Documentation Wiki (Confluence-like)
**Pattern:** REST API with CRUD operations  
**Operations:** Create, read, update, search pages  
**Best for:** Internal wikis, knowledge bases  
**[→ See full example](2-documentation-wiki/)**

### 3. Cloud Infrastructure (AWS-like)
**Pattern:** CLI wrapper  
**Operations:** List resources, describe instances, manage storage  
**Best for:** Cloud providers, infrastructure APIs  
**[→ See full example](3-cloud-infrastructure/)**

### 4. Monitoring System (New Relic-like)
**Pattern:** GraphQL API with custom query language  
**Operations:** Query metrics, search logs, check alerts  
**Best for:** Observability platforms, APM tools  
**[→ See full example](4-monitoring-system/)**

## Quick Start

1. **Choose an example** that matches your service type
2. **Copy the structure** to your project
3. **Customize** for your service:
   - Replace service names
   - Update API endpoints
   - Adjust operations
4. **Set up credentials** (see each example's README)
5. **Test** the delegation pattern

## Structure of Each Example

```
example-name/
├── README.md                    # Complete guide
├── SANITIZATION-MAP.md          # What was changed
├── setup/
│   └── startup-doc.md           # Copy to ~/.claude-config/startup-docs/
├── skills/
│   └── delegating-to-X-agent/   # Copy to ~/.config/superpowers/skills/
│       ├── SKILL.md             # Main assistant delegation logic
│       ├── AGENT-INSTRUCTIONS.md # Sub-agent complete knowledge
│       ├── WORKING-EXAMPLES.md  # Real commands that work
│       └── README.md            # Skill documentation
└── bin/
    └── service-*                # Copy to ~/bin/ and make executable
```

## Security Note

All examples use **secure credential storage** (environment variables, 1Password, or Keybase).

**Never hard-code API tokens in scripts!**

Each example demonstrates three credential management approaches:
1. Environment variables (simplest)
2. 1Password CLI (recommended for teams)
3. Keybase (good for encrypted storage)

## The Delegation Pattern

### Why Delegate?

When your AI assistant needs to query external services (Jira, Confluence, AWS, etc.), loading API documentation into the main context is expensive:

**Before delegation:**
- Every query loads 5-10KB of API docs
- Uses expensive model (Sonnet 4)
- Repeated authentication patterns
- Context fills up quickly

**After delegation:**
- Main assistant: < 1KB (just delegation logic)
- Sub-agent: 5-10KB (runs on cheaper model)
- **Result: 50-70% cost savings**

### How It Works

```
User Query ("show me jira issue PROJ-123")
    ↓
Main Assistant (Expensive Model)
  - Recognizes "jira" keyword
  - Reads AGENT-INSTRUCTIONS.md
  - Invokes Task tool with sub-agent
    ↓
Sub-Agent (Cheap Model)
  - Has complete API knowledge
  - Executes ~/bin/issuetracker-show PROJ-123
  - Formats results
    ↓
Main Assistant
  - Presents clean results to user
  - Context stays clean (< 2KB)
```

### Key Components

1. **Shell Wrappers** (`bin/`)
   - Guard-railed scripts with secure credential loading
   - Handle API authentication
   - Parse and format output
   - Work standalone or via delegation

2. **Delegation Skill** (`skills/`)
   - **SKILL.md**: Teaches main assistant when/how to delegate
   - **AGENT-INSTRUCTIONS.md**: Complete API knowledge for sub-agent
   - **WORKING-EXAMPLES.md**: Real commands with expected output

3. **Startup Doc** (`setup/`)
   - Loaded every session
   - ⛔ Forceful warnings to delegate
   - Keyword triggers
   - Prevents main assistant from handling directly

## Choosing the Right Example

| Your Service | Use This Example | Key Pattern |
|--------------|------------------|-------------|
| Jira, Linear, Asana | Issue Tracker | REST API + Basic Auth |
| Confluence, Notion, Wiki | Documentation Wiki | REST API + CRUD |
| AWS, GCP, Azure | Cloud Infrastructure | CLI wrapper |
| New Relic, Datadog, Splunk | Monitoring System | GraphQL + Query Language |
| GitHub, GitLab | Issue Tracker | REST API + Token Auth |
| Slack, Discord | Issue Tracker | REST API + Webhooks |

## Installation Overview

For any example:

```bash
# 1. Set up credentials (see example README)
export SERVICE_API_TOKEN="your-token"

# 2. Install shell wrappers
cp examples/X-service/bin/* ~/bin/
chmod +x ~/bin/service-*

# 3. Install superpowers skill
cp -r examples/X-service/skills/* \
  ~/.config/superpowers/skills/skills/company/

# 4. Install startup doc
cp examples/X-service/setup/startup-doc.md \
  ~/.claude-config/startup-docs/XX-service-delegation.md

# 5. Test in new Claude session
# User: "show me my service items"
```

## Adapting for Your Service

Each example includes a `SANITIZATION-MAP.md` showing exactly what was changed from the original ExampleJobInc setup. Use this as a guide to adapt for your service.

Common customizations:
- Update API base URLs
- Change authentication method
- Adjust operations for your API
- Update keywords in startup doc
- Customize response formatting

## Cost Savings (Real Data)

From ExampleJobInc's production use:

| Service | Queries/Day | Before | After | Savings |
|---------|-------------|--------|-------|---------|
| Jira | 40 | $8.00 | $2.80 | 65% |
| Confluence | 30 | $6.00 | $2.10 | 65% |
| AWS | 25 | $5.00 | $1.75 | 65% |
| New Relic | 20 | $4.00 | $1.40 | 65% |
| **Total** | **115** | **$23.00** | **$8.05** | **65%** |

*Based on Sonnet 4 vs Haiku 4.5 pricing*

## Testing Your Delegation

### 1. Test Shell Wrappers Standalone

```bash
~/bin/service-list
~/bin/service-search "query"
```

### 2. Test Delegation Pattern

Start fresh Claude session and try natural queries:
- "Show me my issues"
- "Search wiki for S3"
- "List EC2 instances"

### 3. Verify Cost Savings

Check that:
- [ ] Main assistant delegates (doesn't handle directly)
- [ ] Sub-agent is invoked via Task tool
- [ ] Results are clean and formatted
- [ ] Main context stays < 2KB

## Contributing

Have you built a delegation pattern for a different service type? Share it!

Submit a PR with your example following the structure above. Include:
- Complete skill directory
- Shell wrappers with secure credential loading
- README and SANITIZATION-MAP
- Real working examples

## Troubleshooting

### Delegation Doesn't Trigger

- Check startup doc is in `~/.claude-config/startup-docs/`
- Verify file loads (check session startup messages)
- Try explicit keywords: "jira query", "wiki search"

### Credentials Not Found

- Check environment: `echo $SERVICE_API_TOKEN`
- Verify 1Password: `op item get "Service API"`
- Test wrapper: `~/bin/service-list`

### Sub-Agent Errors

- Verify AGENT-INSTRUCTIONS.md location
- Check wrappers are executable: `ls -l ~/bin/service-*`
- Test wrapper directly: `~/bin/service-list`

## Real-World Usage

These examples are based on ExampleJobInc's production setup, handling:
- 115+ delegated queries per day
- Multiple services (Jira, Confluence, AWS, New Relic)
- Read, update, create, and search operations
- 65% cost reduction vs direct API calls
- Used by engineering team for infrastructure management

## License

MIT License - Use freely, adapt for your needs

## Questions?

- Open an issue in the [deli-gator repository](https://github.com/yourusername/deli-gator)
- Check the [main documentation](../README.md)
- Review individual example READMEs for service-specific details

---

**These are complete, production-ready examples. Just add your credentials and start using!**
