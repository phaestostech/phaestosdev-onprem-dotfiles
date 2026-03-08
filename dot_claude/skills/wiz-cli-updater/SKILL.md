---
name: wiz-cli-manager
description: Download, install, and update Wiz CLI v1.x on macOS (Darwin) arm64 systems. Handles binary downloads, signature verification, permission setup, version checking, and environment configuration. Use when asked to install, update, check, or manage Wiz CLI on macOS arm64 systems.
version: 1.0.0
---

# Wiz CLI Manager for macOS ARM64

This skill manages Wiz CLI v1.x installation and updates on macOS (Darwin) systems with arm64 architecture. It provides automated download, verification, installation, and update capabilities for the Wiz CLI binary.

**Version:** 1.0.0  
**Platform:** macOS (Darwin) arm64  
**Installation Method:** Binary (not Docker)

## Core Capabilities

This skill provides three main operations:

1. **Install Wiz CLI** - Download and set up Wiz CLI for the first time
2. **Update Wiz CLI** - Check for and install updates to existing installations
3. **Verify Installation** - Check current version and validate setup

## Installation Workflow

### Step 1: Pre-Installation Checks

Before installing Wiz CLI, verify the system environment:

1. **Check Platform Compatibility:**
```bash
# Verify macOS and ARM64 architecture
uname -s  # Should output: Darwin
uname -m  # Should output: arm64
```

2. **Verify Prerequisites:**
- macOS system (Darwin)
- ARM64 architecture
- curl installed (pre-installed on macOS)
- Internet connectivity to downloads.wiz.io
- Write permissions in installation directory

3. **Determine Installation Location:**
- Default: `/usr/local/bin/wizcli`
- Alternative: User-specified path
- Ensure parent directory exists and is writable

### Step 2: Download Wiz CLI Binary

Download the latest Wiz CLI binary for macOS ARM64:
```bash
#!/bin/bash
# Download Wiz CLI for macOS ARM64
INSTALL_DIR="/usr/local/bin"
BINARY_NAME="wizcli"

echo "Downloading Wiz CLI for macOS ARM64..."
curl -Lo "${INSTALL_DIR}/${BINARY_NAME}" \\
  https://downloads.wiz.io/v1/wizcli/latest/wizcli-darwin-arm64

echo "Download complete: ${INSTALL_DIR}/${BINARY_NAME}"
```

**Alternative - Compressed Download (faster over slow connections):**
```bash
#!/bin/bash
# Download compressed version
INSTALL_DIR="/usr/local/bin"
BINARY_NAME="wizcli"

echo "Downloading compressed Wiz CLI for macOS ARM64..."
curl -Lo "${INSTALL_DIR}/${BINARY_NAME}.gz" \\
  https://downloads.wiz.io/v1/wizcli/latest/wizcli-darwin-arm64.gz

echo "Decompressing..."
gunzip "${INSTALL_DIR}/${BINARY_NAME}.gz"

echo "Download and extraction complete: ${INSTALL_DIR}/${BINARY_NAME}"
```

### Step 3: Verify Binary Signature (Optional but Recommended)

Verify the authenticity of the downloaded binary using GPG signature verification:
```bash
#!/bin/bash
INSTALL_DIR="/usr/local/bin"
BINARY_NAME="wizcli"

# Import Wiz CLI public key
echo "Importing Wiz CLI GPG public key..."
curl -Lo /tmp/public_key.asc https://downloads.wiz.io/wizcli/public_key.asc
gpg --import /tmp/public_key.asc

# Download signature files
echo "Downloading signature files..."
curl -Lo /tmp/wizcli-sha256 \\
  https://downloads.wiz.io/v1/wizcli/latest/wizcli-darwin-arm64-sha256
curl -Lo /tmp/wizcli-sha256.sig \\
  https://downloads.wiz.io/v1/wizcli/latest/wizcli-darwin-arm64-sha256.sig

# Verify GPG signature
echo "Verifying GPG signature..."
gpg --verify /tmp/wizcli-sha256.sig /tmp/wizcli-sha256

# Verify SHA256 checksum
echo "Verifying SHA256 checksum..."
cd "${INSTALL_DIR}"
echo "$(cat /tmp/wizcli-sha256)  ${BINARY_NAME}" | shasum -a 256 --check

# Cleanup
rm /tmp/wizcli-sha256 /tmp/wizcli-sha256.sig /tmp/public_key.asc

echo "Signature verification complete!"
```

**Note:** GPG must be installed on the system. Install via Homebrew if needed:
```bash
brew install gnupg
```

### Step 4: Set Execute Permissions

Make the binary executable:
```bash
#!/bin/bash
INSTALL_DIR="/usr/local/bin"
BINARY_NAME="wizcli"

echo "Setting executable permissions..."
chmod +x "${INSTALL_DIR}/${BINARY_NAME}"

# Verify permissions
ls -l "${INSTALL_DIR}/${BINARY_NAME}"
```

### Step 5: Verify Installation

Confirm Wiz CLI is working correctly:
```bash
#!/bin/bash
# Check version
wizcli version

# Verify binary is in PATH
which wizcli

# Display installation location and permissions
ls -lh "$(which wizcli)"
```

### Step 6: Configure Environment (if needed)

If installing to a non-standard location, update PATH:
```bash
#!/bin/bash
CUSTOM_INSTALL_DIR="$HOME/bin"

# Add to shell profile (choose based on shell)
# For zsh (default on macOS):
echo "export PATH=\\"${CUSTOM_INSTALL_DIR}:\\$PATH\\"" >> ~/.zshrc
source ~/.zshrc

# For bash:
echo "export PATH=\\"${CUSTOM_INSTALL_DIR}:\\$PATH\\"" >> ~/.bash_profile
source ~/.bash_profile
```

## Update Workflow

### Step 1: Check Current Version

Before updating, determine the currently installed version:
```bash
#!/bin/bash
# Get current installed version
CURRENT_VERSION=$(wizcli version 2>/dev/null)

if [ $? -ne 0 ]; then
  echo "Error: Wiz CLI not found or not executable"
  exit 1
fi

echo "Current version: ${CURRENT_VERSION}"
```

### Step 2: Check Latest Available Version

Query the latest version available from Wiz:
```bash
#!/bin/bash
# Get latest version from Wiz downloads
LATEST_VERSION=$(curl -sL https://downloads.wiz.io/v1/wizcli/latest/wizcli-version)

echo "Latest version: ${LATEST_VERSION}"
```

### Step 3: Compare and Update

Determine if an update is needed and perform the update:
```bash
#!/bin/bash
INSTALL_DIR="/usr/local/bin"
BINARY_NAME="wizcli"

# Get current version
CURRENT_VERSION=$(wizcli version 2>/dev/null | grep -oE 'cli: "[^"]+"' | cut -d'"' -f2)

# Get latest version
LATEST_VERSION=$(curl -sL https://downloads.wiz.io/v1/wizcli/latest/wizcli-version | grep -oE 'cli: "[^"]+"' | cut -d'"' -f2)

echo "Current: ${CURRENT_VERSION}"
echo "Latest:  ${LATEST_VERSION}"

if [ "${CURRENT_VERSION}" = "${LATEST_VERSION}" ]; then
  echo "✓ Wiz CLI is up to date!"
  exit 0
fi

echo "Update available. Downloading latest version..."

# Backup current binary
cp "${INSTALL_DIR}/${BINARY_NAME}" "${INSTALL_DIR}/${BINARY_NAME}.backup"

# Download latest version using conditional request (only if newer)
curl -z "${INSTALL_DIR}/${BINARY_NAME}" \\
     -Lo "${INSTALL_DIR}/${BINARY_NAME}" \\
     https://downloads.wiz.io/v1/wizcli/latest/wizcli-darwin-arm64

# Set permissions
chmod +x "${INSTALL_DIR}/${BINARY_NAME}"

# Verify new version
NEW_VERSION=$(wizcli version 2>/dev/null | grep -oE 'cli: "[^"]+"' | cut -d'"' -f2)

echo "✓ Updated to version: ${NEW_VERSION}"

# Remove backup if successful
rm "${INSTALL_DIR}/${BINARY_NAME}.backup"
```

### Step 4: Efficient Update with Time Conditional

Use curl's time-conditional flag to avoid unnecessary downloads:
```bash
#!/bin/bash
INSTALL_DIR="/usr/local/bin"
BINARY_NAME="wizcli"

# Only download if remote file is newer than local
curl -z "${INSTALL_DIR}/${BINARY_NAME}" \\
     -Lo "${INSTALL_DIR}/${BINARY_NAME}" \\
     https://downloads.wiz.io/v1/wizcli/latest/wizcli-darwin-arm64

# If curl didn't download (304 Not Modified), file is unchanged
if [ $? -eq 0 ]; then
  echo "✓ Checked for updates"
  chmod +x "${INSTALL_DIR}/${BINARY_NAME}"
fi
```

## Complete Installation Script

Here's a complete, production-ready installation script:
```bash
#!/bin/bash
set -e  # Exit on error

# Configuration
INSTALL_DIR="/usr/local/bin"
BINARY_NAME="wizcli"
DOWNLOAD_URL="https://downloads.wiz.io/v1/wizcli/latest/wizcli-darwin-arm64"
VERSION_URL="https://downloads.wiz.io/v1/wizcli/latest/wizcli-version"

# Colors for output
GREEN='\\033[0;32m'
YELLOW='\\033[1;33m'
RED='\\033[0;31m'
NC='\\033[0m' # No Color

# Check prerequisites
echo "Checking system compatibility..."

# Verify macOS
if [ "$(uname -s)" != "Darwin" ]; then
  echo -e "${RED}Error: This script is for macOS only${NC}"
  exit 1
fi

# Verify ARM64
if [ "$(uname -m)" != "arm64" ]; then
  echo -e "${RED}Error: This script is for ARM64 architecture only${NC}"
  exit 1
fi

echo -e "${GREEN}✓ System compatible (macOS ARM64)${NC}"

# Check if wizcli already exists
if command -v wizcli &> /dev/null; then
  CURRENT_VERSION=$(wizcli version 2>/dev/null)
  echo -e "${YELLOW}Wiz CLI already installed: ${CURRENT_VERSION}${NC}"
  read -p "Update to latest version? (y/n) " -n 1 -r
  echo
  if [[ ! $REPLY =~ ^[Yy]$ ]]; then
    exit 0
  fi
fi

# Create installation directory if needed
if [ ! -d "${INSTALL_DIR}" ]; then
  echo "Creating installation directory..."
  sudo mkdir -p "${INSTALL_DIR}"
fi

# Download Wiz CLI
echo "Downloading Wiz CLI for macOS ARM64..."
sudo curl -Lo "${INSTALL_DIR}/${BINARY_NAME}" "${DOWNLOAD_URL}"

# Set executable permissions
echo "Setting executable permissions..."
sudo chmod +x "${INSTALL_DIR}/${BINARY_NAME}"

# Verify installation
echo "Verifying installation..."
if ! command -v wizcli &> /dev/null; then
  echo -e "${RED}Error: Installation failed - wizcli not found in PATH${NC}"
  exit 1
fi

VERSION=$(wizcli version 2>/dev/null)
echo -e "${GREEN}✓ Wiz CLI installed successfully!${NC}"
echo "Version: ${VERSION}"
echo "Location: $(which wizcli)"

# Display next steps
echo ""
echo "Next steps:"
echo "1. Set environment variables for authentication:"
echo "   export WIZ_CLIENT_ID=<your-client-id>"
echo "   export WIZ_CLIENT_SECRET=<your-client-secret>"
echo ""
echo "2. Or use authentication flags directly:"
echo "   wizcli scan dir ./folder --client-id <id> --client-secret <secret>"
echo ""
echo "For more information, visit: https://docs.wiz.io/docs/set-up-wiz-cli"
```

## Complete Update Script

Production-ready update script:
```bash
#!/bin/bash
set -e

# Configuration
INSTALL_DIR="/usr/local/bin"
BINARY_NAME="wizcli"
DOWNLOAD_URL="https://downloads.wiz.io/v1/wizcli/latest/wizcli-darwin-arm64"
VERSION_URL="https://downloads.wiz.io/v1/wizcli/latest/wizcli-version"

# Colors
GREEN='\\033[0;32m'
YELLOW='\\033[1;33m'
RED='\\033[0;31m'
NC='\\033[0m'

# Check if wizcli is installed
if ! command -v wizcli &> /dev/null; then
  echo -e "${RED}Error: Wiz CLI not installed${NC}"
  echo "Run the installation script first"
  exit 1
fi

# Get current version
CURRENT_VERSION=$(wizcli version 2>/dev/null | grep -oE 'cli: "[^"]+"' | cut -d'"' -f2)
echo "Current version: ${CURRENT_VERSION}"

# Get latest version
echo "Checking for updates..."
LATEST_VERSION=$(curl -sL "${VERSION_URL}" | grep -oE 'cli: "[^"]+"' | cut -d'"' -f2)
echo "Latest version:  ${LATEST_VERSION}"

# Compare versions
if [ "${CURRENT_VERSION}" = "${LATEST_VERSION}" ]; then
  echo -e "${GREEN}✓ Wiz CLI is already up to date!${NC}"
  exit 0
fi

echo -e "${YELLOW}Update available${NC}"

# Backup current binary
BACKUP_PATH="${INSTALL_DIR}/${BINARY_NAME}.backup"
echo "Creating backup..."
sudo cp "${INSTALL_DIR}/${BINARY_NAME}" "${BACKUP_PATH}"

# Download latest version
echo "Downloading update..."
sudo curl -Lo "${INSTALL_DIR}/${BINARY_NAME}" "${DOWNLOAD_URL}"

# Set permissions
sudo chmod +x "${INSTALL_DIR}/${BINARY_NAME}"

# Verify new version
NEW_VERSION=$(wizcli version 2>/dev/null | grep -oE 'cli: "[^"]+"' | cut -d'"' -f2)

if [ "${NEW_VERSION}" = "${LATEST_VERSION}" ]; then
  echo -e "${GREEN}✓ Successfully updated to ${NEW_VERSION}${NC}"
  sudo rm "${BACKUP_PATH}"
else
  echo -e "${RED}Error: Version mismatch after update${NC}"
  echo "Restoring backup..."
  sudo mv "${BACKUP_PATH}" "${INSTALL_DIR}/${BINARY_NAME}"
  exit 1
fi
```

## Environment Configuration

### Authentication Setup

Configure Wiz CLI authentication after installation:
```bash
#!/bin/bash
# Set authentication environment variables (recommended method)

# Add to shell profile
cat >> ~/.zshrc << 'EOF'

# Wiz CLI Authentication
export WIZ_CLIENT_ID="your-client-id-here"
export WIZ_CLIENT_SECRET="your-client-secret-here"
EOF

# Reload shell configuration
source ~/.zshrc

echo "✓ Wiz CLI authentication configured"
```

### Proxy Configuration (if needed)

Configure proxy settings for corporate environments:
```bash
#!/bin/bash
# Configure proxy for Wiz CLI

# Add to shell profile
cat >> ~/.zshrc << 'EOF'

# Wiz CLI Proxy Configuration
export HTTPS_PROXY="http://proxy-hostname:proxy-port"
EOF

source ~/.zshrc

echo "✓ Proxy configured for Wiz CLI"
```

### Custom Directory Configuration

Configure custom Wiz CLI cache directory:
```bash
#!/bin/bash
# Set custom Wiz directory for cache and auth data

# Add to shell profile
cat >> ~/.zshrc << 'EOF'

# Wiz CLI Directory
export WIZ_DIR="$HOME/.wiz"
EOF

source ~/.zshrc

echo "✓ Custom Wiz directory configured: $WIZ_DIR"
```

## Verification and Testing

### Verify Installation
```bash
#!/bin/bash
# Comprehensive installation verification

echo "=== Wiz CLI Installation Verification ==="
echo ""

# Check if binary exists and is executable
if command -v wizcli &> /dev/null; then
  echo "✓ wizcli found in PATH"
else
  echo "✗ wizcli not found in PATH"
  exit 1
fi

# Display location
WIZCLI_PATH=$(which wizcli)
echo "  Location: ${WIZCLI_PATH}"

# Check permissions
PERMS=$(ls -l "${WIZCLI_PATH}" | cut -d' ' -f1)
echo "  Permissions: ${PERMS}"

# Display version
VERSION=$(wizcli version 2>/dev/null)
echo "  Version: ${VERSION}"

# Check architecture
FILE_INFO=$(file "${WIZCLI_PATH}")
echo "  Architecture: ${FILE_INFO}"

# Verify it's ARM64
if echo "${FILE_INFO}" | grep -q "arm64"; then
  echo "✓ Correct architecture (ARM64)"
else
  echo "✗ Warning: Not ARM64 binary"
fi

echo ""
echo "=== Configuration Check ==="

# Check environment variables
if [ -n "${WIZ_CLIENT_ID}" ]; then
  echo "✓ WIZ_CLIENT_ID configured"
else
  echo "○ WIZ_CLIENT_ID not set"
fi

if [ -n "${WIZ_CLIENT_SECRET}" ]; then
  echo "✓ WIZ_CLIENT_SECRET configured"
else
  echo "○ WIZ_CLIENT_SECRET not set"
fi

if [ -n "${WIZ_DIR}" ]; then
  echo "✓ WIZ_DIR configured: ${WIZ_DIR}"
fi

if [ -n "${HTTPS_PROXY}" ]; then
  echo "✓ HTTPS_PROXY configured: ${HTTPS_PROXY}"
fi

echo ""
echo "Installation verification complete!"
```

## Common Issues and Solutions

### Issue: Permission Denied

**Problem:** Cannot execute wizcli or write to installation directory

**Solution:**
```bash
# Fix permissions on binary
sudo chmod +x /usr/local/bin/wizcli

# Or install to user directory instead
INSTALL_DIR="$HOME/bin"
mkdir -p "${INSTALL_DIR}"
curl -Lo "${INSTALL_DIR}/wizcli" \\
  https://downloads.wiz.io/v1/wizcli/latest/wizcli-darwin-arm64
chmod +x "${INSTALL_DIR}/wizcli"

# Add to PATH
echo 'export PATH="$HOME/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
```

### Issue: Command Not Found

**Problem:** wizcli command not found after installation

**Solution:**
```bash
# Check if binary exists
ls -l /usr/local/bin/wizcli

# If exists, add to PATH
echo 'export PATH="/usr/local/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc

# Verify
which wizcli
```

### Issue: Architecture Mismatch

**Problem:** Downloaded wrong architecture

**Solution:**
```bash
# Verify system architecture
uname -m  # Should output: arm64

# If arm64, re-download correct binary
curl -Lo /usr/local/bin/wizcli \\
  https://downloads.wiz.io/v1/wizcli/latest/wizcli-darwin-arm64

chmod +x /usr/local/bin/wizcli
```

### Issue: SSL/TLS Certificate Errors

**Problem:** Certificate verification failed during download

**Solution:**
```bash
# Update CA certificates
brew install ca-certificates

# Or use --insecure flag (not recommended for production)
curl --insecure -Lo /usr/local/bin/wizcli \\
  https://downloads.wiz.io/v1/wizcli/latest/wizcli-darwin-arm64
```

## When to Use This Skill

Use this skill when users request:

- "Install Wiz CLI on my Mac"
- "Update Wiz CLI to the latest version"
- "Set up Wiz CLI on macOS ARM64"
- "Download Wiz CLI binary for Apple Silicon"
- "Check if my Wiz CLI is up to date"
- "Configure Wiz CLI on my M1/M2/M3 Mac"
- "Verify Wiz CLI installation"
- "Help me install Wiz security scanner"

## Skill Limitations

This skill is specifically designed for:

- **Platform:** macOS (Darwin) only
- **Architecture:** ARM64 (Apple Silicon) only
- **Installation Method:** Binary installation (not Docker)
- **Version:** Wiz CLI v1.x

For other platforms or Docker installations, refer to the official Wiz CLI documentation.

## Additional Resources

- [Official Wiz CLI Documentation](https://docs.wiz.io/docs/set-up-wiz-cli)
- [Wiz CLI Download Page](https://downloads.wiz.io/v1/wizcli/latest/)
- [GPG Public Key](https://downloads.wiz.io/wizcli/public_key.asc)

## Version History

**v1.0.0** (Initial Release)
- Complete installation workflow for macOS ARM64
- Update functionality with version checking
- Signature verification support
- Environment configuration helpers
- Comprehensive verification and testing scripts
- 
