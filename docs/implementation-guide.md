# Implementation Guide: Delegation Agent Pattern

## What Is This?

A **delegation agent pattern** that:
- Keeps your main AI assistant's context clean (< 1KB)
- Delegates queries to specialized sub-agents (cheaper model)
- Sub-agents have full domain knowledge and use shell wrappers
- Reduces cost per query from 5-10KB to < 1KB in main session

## What We Learned

### Success Pattern:

1. **Shell Wrappers** (`~/bin/{service}-*`)
   - Encapsulate authentication
   - Provide guard rails
   - Handle common operations
   - Return clean formatted output

2. **Agent Instructions** (`AGENT-INSTRUCTIONS.md`)
   - Complete domain knowledge (200-400 lines)
   - Decision tree for choosing tools
   - Output format specifications
   - Error handling patterns

3. **Main Skill** (`SKILL.md`)
   - Lightweight delegation logic
   - When to delegate (keywords)
   - How to invoke Task tool
   - What to do with results

4. **Forceful Documentation**
   - Startup doc loaded every session (⛔ warnings)
   - Concept file with STOP warnings
   - Impossible to miss

5. **Testing**
   - Validate in fresh session
   - Sub-agent successfully chose correct tool
   - Main assistant delegated without prompting

## Example Services to Implement

### 1. Cloud Infrastructure CLI Delegation

**Priority:** HIGH (frequent use, complex commands)

**Shell Wrappers Needed:**
```bash
~/bin/cloud-instances-list    # List compute instances with filters
~/bin/cloud-storage-list      # List storage buckets/objects
~/bin/cloud-iam-keys          # List IAM access keys
~/bin/cloud-deploy            # Deploy services
~/bin/cloud-profile           # Show/switch profiles
```

**Agent Instructions Should Cover:**
- Profile management
- Common queries (compute, storage, IAM, container services)
- Output formatting (clean, formatted)
- Safety checks (deployments require confirmation)
- Region awareness
- Decision tree with CLI tool fallbacks

**Keywords to Trigger:** cloud, compute, storage, iam, container, function, database, bucket, instance

### 2. CDN/Cache Management Delegation

**Priority:** MEDIUM (less frequent, but API-heavy)

**Shell Wrappers Needed:**
```bash
~/bin/cdn-purge         # Purge cache by URL/key
~/bin/cdn-service-list  # List services
~/bin/cdn-stats         # Get service stats
~/bin/cdn-config        # Configuration management
```

**Agent Instructions Should Cover:**
- API authentication (token management)
- Service ID lookups
- Cache purging patterns
- Configuration snippet management
- Stats and monitoring

**Keywords to Trigger:** cdn, cache, purge, invalidate, edge

### 3. Documentation System Delegation

**Priority:** MEDIUM (documentation queries)

**Shell Wrappers Needed:**
```bash
~/bin/docs-search       # Search pages
~/bin/docs-page         # Get page content
~/bin/docs-create       # Create new page
~/bin/docs-update       # Update existing page
~/bin/docs-spaces       # List spaces
```

**Agent Instructions Should Cover:**
- Space navigation
- Page hierarchy
- Content formatting
- Search strategies
- Link generation

**Keywords to Trigger:** docs, documentation, wiki, page, article

### 4. Monitoring System Delegation

**Priority:** MEDIUM (monitoring, alerts, log patterns)

**Shell Wrappers Needed:**
```bash
~/bin/monitor-query       # Execute monitoring queries
~/bin/monitor-alerts      # List active alerts
~/bin/monitor-apps        # List monitored applications
~/bin/monitor-logs        # Quick log searching
~/bin/monitor-verify      # Verify query syntax
~/bin/monitor-rules       # List/manage filtering rules
~/bin/monitor-patterns    # Analyze log patterns & anomalies
```

**Bonus: Pattern Analysis & API Documentation**
- Pattern analysis with multiple modes (recent, compare, anomalies)
- API documentation integration for complex queries
- Verification workflow before creating rules
- Integration with existing infrastructure

**Agent Instructions Should Cover:**
- Query language patterns
- API query patterns
- Alert correlation APIs
- Rule management workflows
- Pattern analysis and anomaly detection
- API documentation access for advanced queries
- Decision tree with all available wrappers

**Keywords to Trigger:** monitor, monitoring, query, alerts, apm, logs, observability, metrics

## Implementation Steps (Per Service)

### Phase 1: Shell Wrappers (Week 1)
1. Identify 5-7 most common operations
2. Create guard-railed scripts in `~/bin/`
3. Embed authentication (tokens, profiles)
4. Format output consistently
5. Test each wrapper manually

### Phase 2: Agent Instructions (Week 1-2)
1. Create `AGENT-INSTRUCTIONS.md` with:
   - All wrapper documentation
   - Decision tree for tool selection
   - Output format specifications
   - Error handling patterns
2. Include 10+ examples
3. Pressure test scenarios

### Phase 3: Main Delegation Skill (Week 2)
1. Create delegation skill:
   ```
   skills/{category}/delegating-to-{service}-agent/
   ```
2. Files:
   - `SKILL.md` - Main delegation logic
   - `AGENT-INSTRUCTIONS.md` - Sub-agent knowledge
   - `USAGE-EXAMPLE.md` - Complete patterns

### Phase 4: Forceful Documentation (Week 2)
1. Create startup doc:
   ```
   config/startup-docs/0X-{service}-delegation.md
   ```
2. Add ⛔ STOP warnings and 🚨 emojis
3. Make it impossible to miss

### Phase 5: Testing (Week 2)
1. Start fresh AI session
2. Ask service-related query
3. Verify delegation happens automatically
4. Verify sub-agent chooses correct tool
5. Verify results are accurate

## Template Structure

For each service, create:

```
skills/{category}/delegating-to-{service}-agent/
├── SKILL.md                    # Main delegation skill
├── AGENT-INSTRUCTIONS.md       # Complete service knowledge (for sub-agent)
└── USAGE-EXAMPLE.md            # How main assistant delegates

bin/
├── {service}-{operation}       # Shell wrapper 1
├── {service}-{operation}       # Shell wrapper 2
└── ...                         # More wrappers

config/startup-docs/
└── 0X-{service}-delegation.md  # ⛔ Loaded every session

config/concepts/
└── {service}-access.md         # 🚨 STOP - delegate pattern
```

## Copy-Paste Template

When starting a new service:

```bash
# Create new service structure
SERVICE="your-service"  # cloud, cdn, docs, monitor, etc.

# Create directory structure
mkdir -p skills/infrastructure/delegating-to-${SERVICE}-agent
mkdir -p config/startup-docs
mkdir -p config/concepts

# Create files (use templates from this repo)
# 1. Update keywords in SKILL.md frontmatter
# 2. Write AGENT-INSTRUCTIONS.md with service details
# 3. Write USAGE-EXAMPLE.md
# 4. Create startup doc
# 5. Create/update concept file
```

## Key Lessons to Apply

### What Worked:
✅ Shell wrappers with embedded auth (no token extraction errors)
✅ Forceful documentation (⛔ STOP warnings, startup docs)
✅ Pressure tests (10 ways users might phrase queries)
✅ Decision tree in agent instructions
✅ "QUICKSTART first" structure (minimal cognitive load)
✅ Integration with existing CLI tools when available

### What to Avoid:
❌ Loading full API knowledge in main session
❌ Token extraction from external sources in sandbox
❌ Manual API call construction (error-prone)
❌ Assuming delegation without forceful docs
❌ Detailed technical docs before QUICKSTART

## Cost Savings Projection

**Per query without delegation:**
- Main session: 5-10KB (expensive model tokens)
- Cost: High

**Per query with delegation:**
- Main session: < 1KB (delegation logic only)
- Sub-agent: 5-10KB (cheaper model)
- Cost: 50-70% reduction

**At 10 queries/day across 4 services:**
- Daily savings: ~150-300KB in expensive tokens
- Monthly: ~4.5-9MB moved to cheaper tier
- Scales as more services added

## Implementation Checklist

When implementing a new service:

- [ ] Read this guide
- [ ] Pick a service (start with highest impact)
- [ ] Identify 5-7 most common operations
- [ ] Create shell wrappers
- [ ] Test wrappers manually
- [ ] Write AGENT-INSTRUCTIONS.md
- [ ] Write SKILL.md
- [ ] Create startup doc and concept file
- [ ] Test delegation in fresh session
- [ ] Validate sub-agent chose correct tools

## Questions to Answer During Implementation

1. **Auth:** Where is token/credential stored? How to access reliably?
2. **Common operations:** What are the 5-7 most frequent tasks?
3. **Output format:** JSON? Table? How to make readable?
4. **Errors:** What failures are common? How to handle?
5. **Safety:** What operations need confirmation?
6. **Keywords:** What terms trigger delegation?

## Success Criteria

Each service delegation is successful when:
- [ ] Fresh session recognizes keywords
- [ ] Assistant delegates without manual prompting
- [ ] Sub-agent spawns and uses correct wrapper
- [ ] Results are accurate and well-formatted
- [ ] Main session context stays < 2KB
- [ ] User doesn't have to explain delegation pattern
- [ ] Works first try, every time

## Time Investment

Each service takes approximately:
- 1 hour: Shell wrappers (TDD approach)
- 1 hour: Agent instructions and delegation skill
- 30 min: Forceful documentation
- 30 min: Testing in fresh session

**Total:** ~3 hours per service

**ROI:** 50-70% cost savings on all queries to that service

## Architecture Pattern Summary

1. Recognition keywords in startup doc (⛔ CRITICAL section)
2. Main assistant delegates via Task tool
3. Sub-agent reads AGENT-INSTRUCTIONS.md
4. Sub-agent uses shell wrappers from ~/bin/
5. Sub-agent can consult API documentation for complex queries
6. Results formatted and returned to main session
7. Main context stays < 1KB (50-70% savings)

## Benefits Beyond Cost Savings

- **Separation of concerns** (main context vs specialized sub-agents)
- **Guard rails** (shell wrappers prevent dangerous operations)
- **Self-healing** (sub-agents can explore and discover solutions)
- **Scalability** (add services without bloating main context)
- **Testing protocols** (validate behavior in realistic scenarios)
- **Knowledge preservation** (main assistant doesn't forget important context)

---

**GitHub Repository:** https://github.com/ryancnelson/deli-gator

**See Also:**
- `templates/` - Template files to adapt for your services
- `examples/` - Complete example implementations
- `docs/architecture-blog-post.md` - Detailed explanation of the pattern
