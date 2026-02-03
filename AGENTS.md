# AGENTS.md - TaskChampion DAppNode Package

## Project Overview

This is a **DAppNode Package (DNP)** for TaskChampion Sync Server - a Docker-based package that enables Taskwarrior task synchronization across devices via DAppNode.

**Project Type**: DAppNode Package (Configuration/Deployment)
**Upstream**: https://github.com/gothenburgbitfactory/taskchampion-sync-server
**Package Format**: Docker Compose + DAppNode manifest

## Build Commands

```bash
# Install DAppNode SDK globally
npm install -g @dappnode/dappnodesdk

# Build the package (creates IPFS release)
dappnodesdk build

# Lint and validate package manifest
dappnodesdk lint

# List available commands
dappnodesdk --help
```

## Testing

Since this is a deployment package (not source code):

```bash
# Build and get IPFS hash for testing
dappnodesdk build

# Install on DAppNode via IPFS hash for manual testing
# (Requires DAppNode environment)
```

**Manual Testing Checklist**:
1. Package builds without errors
2. Docker Compose syntax is valid (`docker-compose config`)
3. Setup wizard YAML is valid
4. Manifest JSON follows DAppNode schema

## Code Style Guidelines

### File Organization

```
/home/jstcz/dev/dappnode/taskchampion/
├── dappnode_package.json    # Package manifest (required)
├── docker-compose.yml       # Service definition (required)
├── setup-wizard.yml         # Configuration UI (optional)
├── avatar.png              # Package icon (required)
├── README.md               # User documentation
└── AGENTS.md               # This file
```

### YAML Style (docker-compose.yml, setup-wizard.yml)

- **Indentation**: 2 spaces
- **Quotes**: Use double quotes for strings containing special characters
- **Version**: Use `"3.5"` for Docker Compose
- **Naming**: Use kebab-case for service names and volume names

```yaml
# Good
services:
  taskchampion:
    image: ghcr.io/.../taskchampion-sync-server:0.7.1
    restart: unless-stopped
    environment:
      - RUST_LOG=info

# Bad - inconsistent indentation, no version pinning
services:
    taskchampion:
        image: taskchampion  # No version tag!
```

### JSON Style (dappnode_package.json)

- **Indentation**: 2 spaces
- **Naming**: camelCase for keys
- **Required Fields**: `name`, `version`, `shortDescription`, `type`
- **Package Name Format**: `service-name.public.dappnode.eth`

```json
{
  "name": "taskchampion.public.dappnode.eth",
  "version": "0.1.0",
  "upstreamVersion": "0.7.1",
  "shortDescription": "Taskwarrior sync server",
  "type": "service"
}
```

### Environment Variables

- Use UPPER_CASE for environment variable names
- Provide sensible defaults where possible
- Document all environment variables in setup-wizard.yml

```yaml
environment:
  - DATA_DIR=/var/lib/taskchampion-sync-server/data
  - LISTEN=0.0.0.0:8080
  - CLIENT_ID=${CLIENT_ID}  # Required, from wizard
```

### Version Management

- **Package Version**: Semver (0.1.0, 0.2.0, etc.)
- **Upstream Version**: Track upstream release exactly
- **Update Process**: Bump both versions in manifest and compose

## DAppNode-Specific Conventions

### Manifest Requirements

```json
{
  "name": "taskchampion.public.dappnode.eth",
  "version": "0.1.0",
  "upstreamVersion": "0.7.1",
  "shortDescription": "Max 80 chars",
  "description": "Detailed markdown description",
  "type": "service",
  "architectures": ["linux/amd64", "linux/arm64"],
  "exposable": [
    {
      "name": "Service Name",
      "port": 8080
    }
  ],
  "backup": [
    {
      "name": "data-volume",
      "path": "/path/in/container",
      "service": "service-name"
    }
  ]
}
```

### Setup Wizard Fields

```yaml
fields:
  - id: CLIENT_ID
    title: Client ID
    description: |
      Multi-line description with markdown.
      Explain what this does.
    target:
      type: environment
      name: CLIENT_ID
    required: true
    pattern: "^[a-zA-Z0-9_-]+$"
    patternErrorMessage: "Alphanumeric only"
```

## Security Guidelines

1. **Never commit secrets** - Client IDs go in wizard, not in files
2. **Use VPN-only access** by default (no public exposure)
3. **Pin image versions** - Never use `:latest`
4. **Validate inputs** - Use pattern validation in wizard
5. **Backup critical data** - Define backup paths in manifest

## Common Tasks

### Adding a New Environment Variable

1. Add to `docker-compose.yml` environment section
2. Add field to `setup-wizard.yml` if user-configurable
3. Update `README.md` with documentation

### Updating Upstream Version

1. Update `upstreamVersion` in `dappnode_package.json`
2. Update image tag in `docker-compose.yml`
3. Bump package `version` in manifest
4. Run `dappnodesdk build` to test
5. Update `README.md` if breaking changes

### Testing Package Locally

```bash
# Validate YAML syntax
docker-compose config

# Validate manifest
dappnodesdk lint

# Build release
dappnodesdk build
```

## Resources

- [DAppNode SDK Docs](https://docs.dappnode.io/docs/dev/sdk)
- [DAppNode Manifest Spec](https://docs.dappnode.io/docs/dev/manifest)
- [TaskChampion Sync Server](https://github.com/gothenburgbitfactory/taskchampion-sync-server)
