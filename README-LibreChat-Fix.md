# LibreChat on Proxmox - Fix Summary

## Problem
LibreChat deployment failed with MongoDB installation errors in Debian 13 Trixie containers due to:
1. Incorrect deb822 repository format (Components line in flat repository)
2. MongoDB 8.0 incompatible with Linux kernel 6.19+ (TCMalloc issue SERVER-121912)

## Solution
Changed to Debian 12 (Bookworm) which has native MongoDB 7.0 support.

## Changes Made

### Repository Structure
Forked at: `ben-amberowl/ProxmoxVE_LibreChatFix`

| File | Change |
|------|--------|
| `ct/librechat.sh` | `var_version="${var_version:-12}"` (was 13) |
| `ct/librechat.sh:2` | Source forked `build.func` |
| `misc/build.func:4447,4842` | Use forked repo URLs for install scripts |
| `install/librechat-install.sh:16` | `MONGO_VERSION="7.0"` |

## Requirements

### Hard Constraint
**MongoDB 8.0 is incompatible with Linux kernel 6.19+** (TCMalloc issue SERVER-121912). Proxmox 8.x hosts run kernel 6.8+, making MongoDB 8.0 unusable regardless of container OS.

### Why Debian 12 Works
- MongoDB 7.0 natively supports Debian 12 (Bookworm)
- No suite fallback logic needed (`trixie` → `bookworm`)
- deb822 format works correctly for flat repositories

## Deployment

```bash
bash <(curl -fsSL https://raw.githubusercontent.com/ben-amberowl/ProxmoxVE_LibreChatFix/main/ct/librechat.sh)
```

## Result
- LibreChat running successfully in LXC container
- MongoDB 7.0 installed and operational
- All services functional