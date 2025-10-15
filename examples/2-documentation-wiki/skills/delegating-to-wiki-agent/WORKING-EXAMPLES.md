# Confluence Shell Wrapper Working Examples

## Test Commands

These commands work right now. Use them as reference when building queries.

### Read Operations

**Read DevOPS Home page:**
```bash
~/bin/wiki-read 123456789
```

**Read with version info (for updates):**
```bash
~/bin/wiki-read 123456789 --with-version
```

**Read S3 Bucket Creation Process:**
```bash
~/bin/wiki-read 987654321
```

**Read Serving S3 Content via Fastly CDN:**
```bash
~/bin/wiki-read 555555555
```

### Search Operations

**Search all spaces:**
```bash
~/bin/wiki-search "S3 bucket"
```

**Search OPS space only:**
```bash
~/bin/wiki-search "Fastly" OPS
```

**Search for multiple terms:**
```bash
~/bin/wiki-search "AWS VPN" OPS
```

### List Operations

**List pages in OPS space:**
```bash
~/bin/wiki-list-space OPS
```

**List with custom limit:**
```bash
~/bin/wiki-list-space OPS --limit 100
```

### Create Operations

**Create page under DevOPS Home:**
```bash
~/bin/wiki-create "Test Page" "<h1>Test</h1><p>This is a test page.</p>" 123456789 OPS
```

**Create page with code block:**
```bash
CONTENT='<h1>Example</h1>
<ac:structured-macro ac:name="code" ac:schema-version="1" ac:macro-id="test-123">
  <ac:parameter ac:name="language">bash</ac:parameter>
  <ac:plain-text-body><![CDATA[
echo "Hello World"
  ]]></ac:plain-text-body>
</ac:structured-macro>'

~/bin/wiki-create "Code Example" "$CONTENT" 123456789 OPS
```

### Update Operations

**Simple update:**
```bash
# First read to see current content
~/bin/wiki-read 123456789 --with-version

# Then update (this is safe - just adds a note at top)
~/bin/wiki-update 123456789 "<p><em>Updated: $(date)</em></p>"
```

**Update with title change:**
```bash
~/bin/wiki-update 123456789 "<p>New content</p>" "New Title"
```

## Real-World Workflow Examples

### Workflow 1: Find and Update Page

```bash
# Step 1: Search for page
~/bin/wiki-search "S3 bucket" OPS

# Step 2: Read page (let's say we found ID 987654321)
~/bin/wiki-read 987654321 --with-version

# Step 3: Update with new section
NEW_CONTENT='<h1>S3 Bucket Creation Process</h1>
<p><strong>NEW: Important Note</strong></p>
<p>Always check with DevOps before creating production buckets.</p>
<!-- Rest of existing content here -->'

~/bin/wiki-update 987654321 "$NEW_CONTENT"
```

### Workflow 2: Create Documentation Page with Examples

```bash
# Create comprehensive page
CONTENT='<h1>New Service Documentation</h1>

<h2>Overview</h2>
<p>This service handles XYZ functionality.</p>

<h2>Configuration</h2>
<ac:structured-macro ac:name="code" ac:schema-version="1" ac:macro-id="config-example">
  <ac:parameter ac:name="language">yaml</ac:parameter>
  <ac:plain-text-body><![CDATA[
service:
  name: my-service
  port: 8080
  ]]></ac:plain-text-body>
</ac:structured-macro>

<h2>Related Pages</h2>
<ul>
  <li><p><ac:link><ri:page ri:content-title="S3 Bucket Creation Process" /><ac:link-body>S3 Bucket Creation Process</ac:link-body></ac:link></p></li>
</ul>'

~/bin/wiki-create "New Service Docs" "$CONTENT" 123456789 OPS
```

### Workflow 3: Add Bullet Point to Menu

```bash
# Read OPS home
~/bin/wiki-read 123456789 --with-version

# Extract current content, add bullet, update
# (This requires jq manipulation - best done in sub-agent context)

# Simpler: Read content, manually edit, then:
~/bin/wiki-update 123456789 "$UPDAAcme_CONTENT"
```

## Output Formats

### wiki-read output:
```json
{
  "id": "123456789",
  "title": "DevOPS Home",
  "content": "<hr /><p>Welcome to the Acme devops confluence page!</p>..."
}
```

### wiki-search output:
```json
{
  "id": "555555555",
  "title": "Serving S3 Content via Fastly CDN",
  "space": "OPS",
  "url": "https://example.atlassian.net/wiki/spaces/OPS/pages/555555555/Serving+S3+Content+via+Fastly+CDN"
}
{
  "id": "987654321",
  "title": "S3 Bucket Creation Process",
  "space": "OPS",
  "url": "https://example.atlassian.net/wiki/spaces/OPS/pages/987654321/S3+Bucket+Creation+Process"
}
```

### wiki-create output:
```json
{
  "id": "555555555",
  "title": "New Page Title",
  "space": "OPS",
  "url": "https://example.atlassian.net/wiki/spaces/OPS/pages/555555555/New+Page+Title"
}
```

### wiki-update output:
```json
{
  "id": "123456789",
  "title": "DevOPS Home",
  "version": 15,
  "url": "https://example.atlassian.net/wiki/spaces/OPS/overview"
}
```

## Common Patterns

### Pattern: Safe Read-Only Query
```bash
# Always safe - no modifications
~/bin/wiki-read PAGE_ID
~/bin/wiki-search "query"
~/bin/wiki-list-space SPACE
```

### Pattern: Read-Then-Update
```bash
# Step 1: Read with version
CURRENT=$(~/bin/wiki-read PAGE_ID --with-version)

# Step 2: Extract and modify content
# (manipulation here)

# Step 3: Update
~/bin/wiki-update PAGE_ID "$NEW_CONTENT"
```

### Pattern: Search-Then-Create
```bash
# Step 1: Search to verify doesn't exist
~/bin/wiki-search "My New Page" OPS

# Step 2: If not found, create
~/bin/wiki-create "My New Page" "$CONTENT" PARENT_ID OPS
```

### Pattern: Batch Operations
```bash
# Create multiple pages
for i in 1 2 3; do
  ~/bin/wiki-create "Page $i" "<p>Content $i</p>" 123456789 OPS
done
```

## Error Examples

### Error: Page Not Found
```bash
$ ~/bin/wiki-read 999999999
Error: No content with the given id can be found, or you do not have permission to view it.
```

**Solution:** Verify page ID, try searching instead

### Error: Malformed Content
```bash
$ ~/bin/wiki-update 123456789 "Plain text without HTML"
# May work but renders poorly - always use HTML
```

**Solution:** Wrap in `<p>` tags at minimum

### Error: Missing Parent
```bash
$ ~/bin/wiki-create "Test" "<p>Test</p>" 999999999 OPS
Error: No content with the given id can be found
```

**Solution:** Verify parent page ID exists

## Tips for Sub-Agents

1. **Always format output nicely** - Don't dump raw JSON
2. **Test with read-only first** - Search and read are safe
3. **Read before update** - See current content
4. **Use proper HTML** - Follow Confluence Storage Format
5. **Include URLs in responses** - Users can click to verify
6. **Handle errors gracefully** - Suggest alternatives
7. **Batch related operations** - Multiple creates/updates together

## Authentication Note

All wrappers have authentication embedded. You never need to:
- Provide tokens
- Do base64 encoding
- Set HTTP headers
- Handle `--http1.1` flag

Just call the wrapper and it works!
