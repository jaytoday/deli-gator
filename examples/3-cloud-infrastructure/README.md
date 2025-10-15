# Cloud Infrastructure Delegation Example

Complete implementation of the Deli-Gator pattern for AWS-like cloud infrastructure management.

## What This Example Demonstrates

- CLI wrapper delegation pattern for cloud providers
- AWS profile-based authentication (no hardcoded keys!)
- Shell wrapper pattern with guard rails
- Sub-agent delegation for 50-70% cost savings
- Complete working example from production use at ExampleJobInc

## Prerequisites

- Claude Code with superpowers framework
- AWS CLI configured with profiles
- Bash shell (macOS/Linux)
- jq (JSON processor)

## Installation

### 1. Set Up AWS Credentials

**Using AWS Profiles** (Recommended - no tokens needed!)
```bash
# Configure AWS CLI with named profile
aws configure --profile company-sso

# Or set default profile
export AWS_PROFILE=company-sso
```

### 2. Install Shell Wrappers

```bash
# Copy to ~/bin/
cp bin/* ~/bin/
chmod +x ~/bin/cloud-*

# Verify installation
cloud-instances-list --help
```

### 3. Install Superpowers Skill

```bash
# Copy to superpowers skills directory
cp -r skills/delegating-to-cloud-agent \
  ~/.config/superpowers/skills/skills/company/
```

### 4. Install Startup Doc

```bash
# Copy to Claude config
cp setup/startup-doc.md \
  ~/.claude-config/startup-docs/03-cloud-delegation.md
```

## Usage

Start a new Claude session and try:

```
User: "List running EC2 instances"
User: "Show me S3 buckets"
User: "What ECS services are deployed?"
```

Expected behavior:
1. ✅ Claude recognizes keywords (aws, ec2, s3, ecs) and delegates
2. ✅ Sub-agent uses cloud-* wrappers
3. ✅ Results returned cleanly formatted
4. ✅ Main context stays < 2KB

## Available Operations

- `cloud-instances-list` - List EC2 instances
- `cloud-instance-describe INSTANCE_ID` - Show instance details
- `cloud-storage-list` - List S3 buckets
- `cloud-services-list` - List ECS services
- `cloud-profile` - Show current AWS profile

## Security

This example uses **AWS profiles** - no hardcoded access keys!

All wrappers use `aws --profile` flag, reading from:
- `~/.aws/credentials`
- `~/.aws/config`
- SSO configuration

## Cost Savings

**Before:** 5-10KB per query (expensive model)
**After:** < 1KB main context, 5-10KB sub-agent (cheap model)
**Savings:** 50-70% per query

## Customization

Update AWS profile in wrappers or set environment:
```bash
export AWS_PROFILE=your-profile
```

See [SANITIZATION-MAP.md](./SANITIZATION-MAP.md) for full adaptation guide.

## License

MIT License - Use freely, adapt for your needs
