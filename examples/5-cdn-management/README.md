# CDN Management Delegation Example

Complete implementation of the Deli-Gator pattern for Fastly-like CDN management platforms.

## What This Example Demonstrates

- Profile-based authentication for CDN CLI tools
- Service management and configuration inspection
- Backend/origin server investigation
- VCL (Varnish Configuration Language) inspection
- Cache purge operations with safety guards
- Sub-agent delegation for 50-70% cost savings
- Complete working example from production use at ExampleJobInc

## Prerequisites

- Claude Code with superpowers framework
- Fastly (or compatible) CDN account with CLI access
- Fastly CLI installed (`fastly`)
- Bash shell (macOS/Linux)
- jq (JSON processor)

## Installation

### 1. Set Up Fastly CLI

**Install Fastly CLI:**
```bash
# macOS
brew install fastly/tap/fastly

# Or download from: https://github.com/fastly/cli/releases
```

**Configure Profile:**
```bash
fastly profile create --name user
# Follow prompts to enter API token
```

### 2. Install Shell Wrappers

```bash
# Copy to ~/bin/
cp bin/* ~/bin/
chmod +x ~/bin/cdn-*

# Verify installation
cdn-services --help
```

### 3. Install Superpowers Skill

```bash
# Copy to superpowers skills directory
cp -r skills/delegating-to-cdn-agent \
  ~/.config/superpowers/skills/skills/company/
```

## Usage

Start a new Claude session and try:

```
User: "List all CDN services"
User: "Describe the image processing CDN"
User: "Show backends for service abc123xyz"
User: "List VCL configurations"
User: "Purge cache for surrogate key image-cache"
```

Expected behavior:
1. ✅ Claude recognizes keywords (cdn, fastly, cache, vcl) and delegates
2. ✅ Sub-agent uses cdn-* wrappers
3. ✅ Results returned cleanly formatted
4. ✅ Main context stays < 2KB

## Available Operations

### Service Management
- `cdn-services` - List all CDN services
- `cdn-describe --service-id ID` - Get service details

### Backend Investigation
- `cdn-backends --service-id ID` - List backend servers
- `cdn-backend-describe --service-id ID --name BACKEND` - Describe specific backend

### VCL Configuration
- `cdn-vcl-list --service-id ID` - List VCL versions
- `cdn-vcl-show --service-id ID --version VERSION` - View VCL content

### Cache Management
- `cdn-purge --service-id ID --all` - Purge all cache (use with caution)
- `cdn-purge --service-id ID --key KEY` - Purge by surrogate key
- `cdn-purge --service-id ID --url URL` - Purge specific URL

## Example Workflows

### Investigate CDN Configuration
```bash
# List all services
cdn-services

# Get details for image processing CDN
cdn-describe --service-id abc123xyz789example

# Check backends
cdn-backends --service-id abc123xyz789example

# View VCL configuration
cdn-vcl-list --service-id abc123xyz789example
cdn-vcl-show --service-id abc123xyz789example --version 42
```

### Cache Management
```bash
# Purge specific surrogate key
cdn-purge --service-id abc123xyz --key image-cache

# Purge specific URL
cdn-purge --service-id abc123xyz --url https://examplejobinc-cdn.com/image.png

# Purge all (use with extreme caution)
cdn-purge --service-id abc123xyz --all
```

## Cost Savings

**Before:** 5-10KB per query (expensive model)
**After:** < 1KB main context, 5-10KB sub-agent (cheap model)
**Savings:** 50-70% per query

## Customization

### Using Different Profile
```bash
# Set environment variable
export FASTLY_PROFILE=production

# Or use --profile flag
cdn-services --profile production
```

### Adding New Operations

1. Create wrapper script in `bin/cdn-custom`
2. Update `AGENT-INSTRUCTIONS.md` with usage
3. Test with real CDN
4. Sanitize for examples

See [SANITIZATION-MAP.md](./SANITIZATION-MAP.md) for full adaptation guide.

## Security

This example demonstrates profile-based authentication. **Never hard-code API tokens in scripts.**

All shell wrappers support:
- Fastly CLI profiles (recommended)
- Environment variables (`FASTLY_PROFILE`)

Configure your profile:
```bash
fastly profile create --name user
```

## Cache Purge Safety

**IMPORTANT:** Cache purging affects production traffic. The wrappers include safety features:

1. **Confirmation prompts** for `--all` purges
2. **Clear warnings** about impact
3. **Prefer specific purges** (URL/key) over full purges
4. **Sub-agent provides additional context** about operation impact

### Purge Hierarchy (Safest to Most Impactful)
1. Single URL purge - Minimal impact
2. Surrogate key purge - Moderate impact
3. Full service purge - High impact, use with caution

## Common CDN Services

Example services in a typical setup:
- **Image Processing CDN** - Dynamic image transformations
- **Static Assets CDN** - CSS, JavaScript, fonts
- **Video Streaming CDN** - HLS video delivery
- **Photo Assets CDN** - High-resolution images
- **General Web Assets** - Mixed static content

## Troubleshooting

**"Service not found"**
- List services first: `cdn-services`
- Verify service ID format

**"Permission denied"**
- Check Fastly profile: `fastly profile list`
- Verify API token has required permissions

**"Version not found"**
- List VCL versions: `cdn-vcl-list --service-id ID`
- Use `--version active` for current version

## License

MIT License - Use freely, adapt for your needs
