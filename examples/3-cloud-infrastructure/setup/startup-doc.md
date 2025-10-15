# ⛔ CRITICAL: AWS Query Delegation Pattern ⛔

## MANDATORY FOR ALL CLAUDE ASSISTANTS

**IF user mentions ANY of these keywords:**
- aws
- ec2
- s3
- iam
- ecs
- lambda
- cloudwatch
- rds
- bucket
- instance (in AWS context)
- cloud infrastructure
- deployment (in AWS/ECS context)

**THEN you MUST delegate to a sub-agent. DO NOT handle directly.**

## Why This Matters

Handling AWS queries directly pollutes your expensive context with:
- aws CLI syntax and command patterns
- Profile and authentication details
- API response formats
- Error handling logic
- JSON parsing

**Cost:** 5-10KB per query in Sonnet 4.5 tokens

**Solution:** Delegate to cheaper sub-agent with specialized knowledge.

## How To Delegate (3 Steps)

### Step 1: Read Agent Instructions
```
Read("~/.config/superpowers/skills/skills/infrastructure/delegating-to-cloud-agent/AGENT-INSTRUCTIONS.md")
```

### Step 2: Invoke Task Tool
```
Task(
  subagent_type: "general-purpose",
  description: "Query AWS infrastructure",
  prompt: "<paste agent instructions here>

USER REQUEST: <user's exact request>

Return clean formatted results."
)
```

### Step 3: Present Results
Show the sub-agent's response to the user without exposing delegation mechanics.

## Example

**User says:** "list running ec2 instances"

**You do:**
1. Read AGENT-INSTRUCTIONS.md
2. Task tool with those instructions + user request
3. Present: "Found 22 running instances: i-0abc123 (production-web) [running] t3.medium..."

## ⛔ What You Should NEVER Do

❌ Run `cloud-*` commands yourself
❌ Load the old AWS CLI documentation
❌ Construct aws CLI commands
❌ Try to authenticate with AWS
❌ Read shell wrapper scripts directly

**Those resources are for the SUB-AGENT, not for you!**

## Benefits

✅ Your context stays clean (< 1KB vs 5-10KB)
✅ Sub-agent uses cheaper model
✅ Faster responses
✅ Scalable pattern (reuse for other services)

---

**Location of full documentation:**
`~/.config/superpowers/skills/skills/infrastructure/delegating-to-cloud-agent/`

**Also see:**
`~/.config/ryans-way/concepts/tools/cloud-access.md` (if it exists)
