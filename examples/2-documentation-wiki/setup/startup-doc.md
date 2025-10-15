# ⛔ CRITICAL: Confluence Query Delegation Pattern ⛔

## MANDATORY FOR ALL CLAUDE ASSISTANTS

**IF user mentions ANY of these keywords:**
- confluence
- wiki page
- wiki
- documentation page
- docs (in Acme context)
- OPS space
- DevOPS home

**THEN you MUST delegate to a sub-agent. DO NOT handle directly.**

## Why This Matters

Handling Confluence queries directly pollutes your expensive context with:
- API authentication patterns (Basic Auth, base64 encoding)
- REST API endpoint structures
- Confluence Storage Format (XML-like HTML with macros)
- JSON parsing and manipulation logic
- Error handling patterns

**Cost:** 5-10KB per query in Sonnet 4.5 tokens

**Solution:** Delegate to cheaper sub-agent with specialized knowledge.

## How To Delegate (3 Steps)

### Step 1: Read Agent Instructions
```
Read("~/.config/superpowers/skills/skills/examplejobinc/delegating-to-wiki-agent/AGENT-INSTRUCTIONS.md")
```

### Step 2: Invoke Task Tool
```
Task(
  subagent_type: "general-purpose",
  description: "Query Acme Confluence wiki",
  prompt: "<paste agent instructions here>

USER REQUEST: <user's exact request>

Return clean formatted results."
)
```

### Step 3: Present Results
Show the sub-agent's response to the user without exposing delegation mechanics.

## Example

**User says:** "show me the devops overview page"

**You do:**
1. Read AGENT-INSTRUCTIONS.md
2. Task tool with those instructions + user request
3. Present: "Here's the DevOPS Home page: [formatted content]"

## ⛔ What You Should NEVER Do

❌ Run `~/bin/wiki-*` commands yourself
❌ Load the old `managing-confluence` skill
❌ Construct curl commands
❌ Try to authenticate with Atlassian API
❌ Parse Confluence Storage Format manually
❌ Read AGENT-INSTRUCTIONS.md for your own use (it's for the SUB-AGENT!)

**Those resources are for the SUB-AGENT, not for you!**

## Benefits

✅ Your context stays clean (< 1KB vs 5-10KB)
✅ Sub-agent uses cheaper model
✅ Faster responses
✅ Scalable pattern (reuse for other services)

---

**Location of full documentation:**
`~/.config/superpowers/skills/skills/examplejobinc/delegating-to-wiki-agent/`

**Available Operations:**
- Read pages
- Update pages
- Create pages
- Search pages
- List space contents

**Common Page IDs:**
- DevOPS Home: 123456789
- S3 Bucket Creation Process: 987654321
- Serving S3 Content via Fastly CDN: 555555555
