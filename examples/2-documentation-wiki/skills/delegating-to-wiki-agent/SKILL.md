# Skill: Delegating to Confluence Agent

## Purpose

This skill teaches you how to delegate Acme Confluence wiki operations to a specialized sub-agent, keeping your context clean and focused.

## When to Use This Skill

**Trigger Keywords:** confluence, wiki, documentation, page, article, docs (in Acme context)

**IF user mentions ANY of these:**
- "confluence"
- "wiki page"
- "documentation page"
- "check the wiki"
- "update the docs"
- "create a confluence page"
- "search confluence"
- "OPS space" (Confluence space)

**THEN you MUST delegate. DO NOT handle directly.**

## Why Delegate?

**Cost:** Handling Confluence directly pollutes your context with:
- API authentication patterns (base64, tokens)
- REST API endpoint structures
- Confluence Storage Format (HTML macros)
- Error handling logic
- JSON parsing patterns

**Result:** 5-10KB per query in expensive Sonnet 4.5 tokens

**Solution:** Delegate to sub-agent using cheaper model with specialized knowledge

## How to Delegate (3 Steps)

### Step 1: Read Agent Instructions

```
Read("~/.config/superpowers/skills/skills/examplejobinc/delegating-to-wiki-agent/AGENT-INSTRUCTIONS.md")
```

### Step 2: Invoke Task Tool

```
Task(
  subagent_type: "general-purpose",
  description: "Query Acme Confluence wiki",
  prompt: "<paste AGENT-INSTRUCTIONS.md content here>

USER REQUEST: <user's exact request>

Return clean formatted results."
)
```

### Step 3: Present Results

Show the sub-agent's response to the user without exposing delegation mechanics.

## Example Interactions

### Example 1: User Wants to Read Page

**User says:** "show me the devops overview page"

**You do:**
1. Recognize keyword: "devops" + implied Confluence
2. Read AGENT-INSTRUCTIONS.md
3. Delegate via Task tool
4. Sub-agent executes: `wiki-read 123456789`
5. Present: "Here's the DevOPS Home page: [formatted content]"

**Context cost:** < 1KB (vs 5-10KB if you did it directly)

---

### Example 2: User Wants to Search

**User says:** "find confluence pages about S3"

**You do:**
1. Recognize keywords: "confluence" + "find"
2. Delegate to sub-agent
3. Sub-agent executes: `wiki-search "S3" OPS`
4. Present: "Found 3 pages: [list with URLs]"

---

### Example 3: User Wants to Update

**User says:** "add a note to the OPS overview that we're updating the runbooks"

**You do:**
1. Recognize: "OPS overview" (page 123456789)
2. Delegate to sub-agent
3. Sub-agent:
   - Reads current content
   - Updates with note
   - Returns new version
4. Present: "Added note. Page updated to version 16."

---

### Example 4: User Wants to Create

**User says:** "create a confluence page about our new deployment process"

**You do:**
1. Recognize: "create a confluence page"
2. Delegate to sub-agent
3. Sub-agent may ask: "Where should this page be created? Under DevOPS Home?"
4. Present sub-agent's question to user
5. User answers, you relay to sub-agent
6. Present: "Page created: [URL]"

## Common Mistakes to Avoid

### ❌ Mistake 1: Handling It Yourself

```
User: "update the confluence page about S3"
You: [Tries to run curl commands directly]
```

**Why wrong:** Pollutes your context with API details

**Correct:** Delegate immediately to sub-agent

---

### ❌ Mistake 2: Exposing Implementation Details

```
User: "find pages about Fastly"
You: "I'll use the wiki-search wrapper with the OPS space key..."
```

**Why wrong:** User doesn't care about implementation

**Correct:** Just present results cleanly

---

### ❌ Mistake 3: Not Reading Instructions

```
You: [Invokes Task tool without reading AGENT-INSTRUCTIONS.md]
```

**Why wrong:** Sub-agent won't know what to do

**Correct:** Always read and paste full instructions

---

### ❌ Mistake 4: Batching Queries Wrong

```
User: "read page 123 and page 456"
You: [Single delegation asking for both]
```

**Why right:** Single delegation is fine for related operations

**Also fine:** Separate delegations for unrelated operations

## Edge Cases

### Case 1: User Provides Confluence URL

```
User: "update https://example.atlassian.net/wiki/spaces/OPS/pages/123456789/"
```

**You do:**
- Extract page ID: 123456789
- Delegate with: "Update page 123456789: [user's changes]"

### Case 2: User Doesn't Know Page ID

```
User: "update the S3 documentation"
```

**You do:**
- Delegate with: "Find and update S3 documentation: [changes]"
- Sub-agent will search first, then update

### Case 3: Complex Multi-Page Operation

```
User: "create 5 new pages for our new service documentation"
```

**You do:**
- Single delegation describing all 5 pages
- Sub-agent can iterate through creates
- Present: "Created 5 pages: [URLs]"

## Shell Wrappers Available (for your knowledge only)

**Do NOT execute these yourself. Sub-agent uses them.**

- `wiki-read` - Read page content
- `wiki-update` - Update page
- `wiki-create` - Create page
- `wiki-search` - Search pages
- `wiki-list-space` - List pages in space

## Benefits Recap

✅ Your context stays clean (< 1KB vs 5-10KB)
✅ Sub-agent uses cheaper model
✅ Faster responses
✅ Scalable pattern (reuse for other services)
✅ Sub-agent can self-heal through iteration

## Testing the Delegation

**Test prompt:** "Show me the DevOPS overview page"

**Expected behavior:**
1. You recognize "DevOPS overview" = Confluence
2. You read AGENT-INSTRUCTIONS.md
3. You invoke Task tool with full instructions
4. Sub-agent returns formatted content
5. You present it cleanly

**Success criteria:**
- ✅ No API details in your response
- ✅ Clean, formatted output
- ✅ User gets what they asked for
- ✅ Your context < 2KB

## Summary

**Always delegate Confluence operations. Never handle them directly.**

This keeps your context clean so you can focus on what matters: helping the user with their actual work, not fighting with wiki APIs.
