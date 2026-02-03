# TaskChampion Sync Server - DAppNode Package

DAppNode Package (DNP) for TaskChampion Sync Server to enable Taskwarrior task synchronization across devices.

## Overview

TaskChampion Sync Server is the official sync server for Taskwarrior 3.x, enabling end-to-end encrypted task synchronization across multiple devices.

**Key Features:**
- End-to-end encryption (server never sees plaintext task data)
- Simple token-based authentication
- VPN-only access by default (no public internet exposure)
- Automatic data persistence and backup support

## Installation

1. Install the package via DAppNode's DAppStore or IPFS hash
2. During setup, enter a **Client ID** (secure random string)
3. The service will start automatically on port 8080

## Taskwarrior Client Configuration

After installation, configure each Taskwarrior client:

```bash
# Generate an encryption secret (store securely!)
openssl rand -hex 32

# Set sync server URL (accessible over VPN)
task config sync.server.url http://taskchampion.public.dappnode:8080

# Set the client ID (same as configured in DAppNode)
task config sync.server.client_id <your-client-id>

# Set the encryption secret (same on ALL devices)
task config sync.encryption.secret <your-generated-secret>

# Initialize sync (first time)
task sync init

# Sync tasks
task sync
```

**Important:** Use the same encryption secret on all devices. Store it safely.

## Network Access

### VPN-Only (Default)
The service is accessible via DAppNode VPN at:
```
http://taskchampion.public.dappnode:8080
```

### Public Exposure (Optional)
If needed, you can expose the service to the public internet via DAppNode's "Expose Service" feature in the UI.

## Data Persistence

- Tasks are stored in a named Docker volume `sync-data`
- Data survives package updates
- Backup and restore supported via DAppNode UI

## Security

- **End-to-end encryption**: Taskwarrior encrypts data before sending
- **Client ID as auth**: Simple token-based access control
- **VPN-only by default**: Not exposed to public internet
- **No server-side decryption**: Server only stores encrypted blobs

## Support

- Taskwarrior documentation: https://taskwarrior.org/docs/
- TaskChampion Sync Server: https://github.com/gothenburgbitfactory/taskchampion-sync-server

## Maintainer

Created and maintained by [Jacob Czepluch (@czepluch)](https://github.com/czepluch)

## License

MIT License - See upstream repository for details.
