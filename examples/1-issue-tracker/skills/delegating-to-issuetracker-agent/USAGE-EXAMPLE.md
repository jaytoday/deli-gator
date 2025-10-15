# How to Use the Jira Delegation Pattern

This file shows the EXACT pattern the main assistant should use to delegate Jira queries.

## Step 1: Recognize Jira Query

Keywords that trigger delegation:
- jira, issue, ticket
- PROJ-*, AcmeED-*
- "show me", "search for", "create", "comment"

## Step 2: Invoke Task Tool

Read the agent instructions file and embed it in the prompt:

```python
Task(
  subagent_type="general-purpose",
  description="Query Acme Jira",
  prompt=f"""
{contents_of_AGENT_INSTRUCTIONS_md}

USER REQUEST: {user's actual request}

Return clean, formatted results ready to show the user.
"""
)
```

## Step 3: Present Results

When agent returns results, present them directly to user without exposing mechanics.

## Complete Example

**User says:** "show me my jira issues for production-main"

**Main assistant does:**

```python
# Read agent instructions
agent_instructions = Read("~/.config/superpowers/skills/skills/examplejobinc/delegating-to-issuetracker-agent/AGENT-INSTRUCTIONS.md")

# Invoke sub-agent
Task(
  subagent_type="general-purpose",
  description="Query Jira for production-main issues",
  prompt=f"""
{agent_instructions}

USER REQUEST: Show me my jira issues for production-main

Execute the appropriate issuetracker-* shell wrapper and return formatted results.
"""
)
```

**Agent returns:**
```
Found PROJ-1638: production-main
Status: In Development
Updated: 2025-10-07
URL: https://example.atlassian.net/browse/PROJ-1638
```

**Main assistant tells user:**
```
I found your Jira issue for production-main:

PROJ-1638: production-main
Status: In Development
Updated: 2025-10-07
URL: https://example.atlassian.net/browse/PROJ-1638
```

## Why This Works

- Main assistant: Lightweight recognition + delegation (< 1KB context)
- Sub-agent: Full Jira knowledge (< 5KB context, cheaper model)
- User: Clean results, no technical details
- Cost: Much lower than loading full knowledge in main session

## Testing

Test in tmux trialarena:
1. Start fresh Claude session
2. Ask: "show me my jira issues for production-main"
3. Verify main Claude delegates without trying curl directly
4. Verify results are clean and accurate
