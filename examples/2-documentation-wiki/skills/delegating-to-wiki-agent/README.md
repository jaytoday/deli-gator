# Confluence API Delegation Skill

Complete implementation of the Deli-Gator pattern for Acme Confluence wiki operations.

## Overview

This skill enables the main Claude assistant to delegate Confluence wiki operations to a specialized sub-agent, keeping the expensive main context clean while using a cheaper model for API operations.

**Cost Savings:** 50-70% reduction per Confluence query

## Architecture Layers

1. **Recognition** - Keywords trigger delegation (startup doc)
2. **Shell Wrappers** - Guard-railed, tested scripts in `~/bin/`
3. **Agent Instructions** - Complete domain knowledge for sub-agent
4. **Delegation Skill** - How main assistant invokes sub-agent
5. **Working Examples** - Real commands that work now

## Files in This Skill

### For Main Assistant

**SKILL.md**
- When to delegate
- How to delegate (3-step pattern)
- Common mistakes to avoid
- Examples of good delegation

**Loaded automatically:**
`~/devel/.claude-config/startup-docs/04-wiki-delegation.md`
- Forceful ⛔ warnings
- Keywords that trigger delegation
- Quick reference

### For Sub-Agent

**AGENT-INSTRUCTIONS.md**
- Complete Confluence API knowledge
- Decision tree for operations
- Shell wrapper documentation
- Confluence Storage Format examples
- Error handling
- Safety rules

**WORKING-EXAMPLES.md**
- Real working commands
- Workflow examples
- Output formats
- Common patterns
- Error examples

### Shell Wrappers (in `~/bin/`)

**wiki-read** - Read page content
```bash
wiki-read PAGE_ID [--with-version]
```

**wiki-update** - Update page
```bash
wiki-update PAGE_ID "Content HTML" ["Title"]
```

**wiki-create** - Create new page
```bash
wiki-create "Title" "Content" PARENT_ID [SPACE]
```

**wiki-search** - Search pages
```bash
wiki-search "query" [SPACE_KEY]
```

**wiki-list-space** - List pages in space
```bash
wiki-list-space SPACE_KEY [--limit N]
```

## How It Works

### User Request Flow

```
User: "Show me the DevOPS overview page"
  ↓
Main Assistant recognizes: "DevOPS" + "overview page" = Confluence
  ↓
Main Assistant reads: AGENT-INSTRUCTIONS.md
  ↓
Main Assistant invokes: Task tool with instructions + user request
  ↓
Sub-Agent (cheaper model):
  - Parses request
  - Decides: need to read page 123456789
  - Executes: wiki-read 123456789
  - Formats output
  - Returns to main assistant
  ↓
Main Assistant presents: Formatted page content to user
```

**Main assistant context cost:** < 1KB
**Sub-agent context cost:** 5-10KB (on cheaper model)

## Keywords That Trigger Delegation

- confluence
- wiki
- wiki page
- documentation page
- docs (in Acme context)
- OPS space
- DevOPS home

## Common Operations

### Read Page
```
User: "show me page 123456789"
Main → Sub-agent → wiki-read 123456789
```

### Search
```
User: "find confluence pages about S3"
Main → Sub-agent → wiki-search "S3" OPS
```

### Update Page
```
User: "add a note to the ops overview"
Main → Sub-agent →
  wiki-read 123456789 --with-version
  wiki-update 123456789 "updated content"
```

### Create Page
```
User: "create a page about docker deployment"
Main → Sub-agent →
  wiki-create "Docker Deployment" "<content>" 123456789 OPS
```

## Testing the Skill

### Test 1: Simple Read
```
User: "Show me the DevOPS home page"
Expected: Page content displayed, < 1KB main context
```

### Test 2: Search
```
User: "Find pages about Fastly CDN"
Expected: List of matching pages with URLs
```

### Test 3: Create
```
User: "Create a test page under DevOPS home"
Expected: Page created, URL returned
```

### Success Criteria
- ✅ Delegation happens automatically (no user prompting)
- ✅ Results are accurate and well-formatted
- ✅ Main context stays < 2KB
- ✅ Works first try, every time

## Common Page IDs

**OPS Space:**
- DevOPS Home: 123456789
- S3 Bucket Creation Process: 987654321
- Serving S3 Content via Fastly CDN: 555555555

## Configuration

**Confluence Instance:** https://example.atlassian.net/wiki

**Authentication:** Embedded in shell wrappers (API token)

**Default Space:** OPS (DevOPS)

## Troubleshooting

### Issue: Main assistant handles Confluence directly
**Solution:** Check startup doc is loaded, add more forceful ⛔ warnings

### Issue: Sub-agent doesn't know what to do
**Solution:** Verify AGENT-INSTRUCTIONS.md is being read and passed to Task tool

### Issue: Shell wrappers fail
**Solution:**
- Check wrappers are executable: `chmod +x ~/bin/wiki-*`
- Test manually: `~/bin/wiki-read 123456789`
- Verify API token is current

### Issue: Malformed content
**Solution:** Follow Confluence Storage Format in AGENT-INSTRUCTIONS.md

## Benefits

### Cost Savings
- **50-70% reduction** in expensive model token usage
- Main context stays < 1KB per query
- Sub-agents use cheaper models

### Scalability
- Add operations without bloating main context
- Each operation is independent
- Test in isolation

### Reliability
- Guard rails prevent dangerous operations
- Tested patterns (wrappers work before automation)
- Sub-agents can self-heal through iteration

### Knowledge Preservation
- Main assistant doesn't forget important context
- Specialized knowledge stays in sub-agents
- Clean separation of concerns

## Time Investment

**Initial Setup:** ~3 hours (already done!)
- 1 hour: Shell wrappers
- 1 hour: Agent instructions
- 30 min: Delegation skill
- 30 min: Startup doc

**ROI:** 50-70% cost savings on all Confluence queries forever

## Integration with Other Skills

This Confluence delegation skill follows the same pattern as:
- **Jira:** `~/.config/superpowers/skills/skills/examplejobinc/delegating-to-jira-agent/`
- **AWS:** `~/.config/superpowers/skills/skills/infrastructure/delegating-to-aws-agent/`
- **New Relic:** `~/.config/superpowers/skills/skills/monitoring/delegating-to-newrelic-agent/`

All use the same Deli-Gator architecture pattern.

## Next Steps

### To Use This Skill
1. Skill is ready to use immediately
2. Just mention Confluence in a user query
3. Main assistant will delegate automatically

### To Extend This Skill
1. Add new operation: Create shell wrapper in `~/bin/wiki-*`
2. Document in AGENT-INSTRUCTIONS.md
3. Add example to WORKING-EXAMPLES.md
4. Test with sub-agent

### To Create Similar Skill for Another Service
1. Copy this directory structure
2. Replace "confluence" with your service name
3. Create appropriate shell wrappers
4. Update agent instructions
5. Add startup doc

## Documentation

**Full Deli-Gator pattern:**
`/tmp/claude/deli-gator/`

**Blog post:**
`/tmp/claude/deli-gator/docs/architecture-blog-post.md`

**Implementation guide:**
`/tmp/claude/deli-gator/docs/implementation-guide.md`

## Credits

Created: October 2025
Pattern: Deli-Gator (Delegation + Agents)
Author: Ryan Nelson with Claude Sonnet 4.5

Based on successful patterns for:
- Jira API delegation
- AWS CLI delegation
- New Relic NerdGraph delegation

---

**Remember:** The best architecture is the one that scales. This pattern lets you add infinite services without forgetting what matters.

🐊 Happy delegating!
