# cftunnel

🌩️ **Cloudflare Tunnel automation script for Laravel Herd** - Share your local development sites instantly with custom domains.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Shell](https://img.shields.io/badge/Shell-Bash-green.svg)](https://www.gnu.org/software/bash/)
[![Platform](https://img.shields.io/badge/Platform-macOS-blue.svg)](#installation)

## Overview

`cftunnel` is a powerful command-line tool that automates the setup and management of Cloudflare tunnels for your Laravel Herd local development environment. With a single command, you can expose your local sites to the internet using your own custom domains.

### Why cftunnel?

- 🚀 **One-command setup** - No complex configuration files
- 🔒 **Secure tunnels** - Uses Cloudflare's zero-trust network
- 🌐 **Custom domains** - Use your own domains instead of random URLs
- 🔄 **Automatic management** - Handles tunnel creation, DNS routing, and cleanup
- 🎯 **Laravel Herd integration** - Perfect for PHP/Laravel development
- 💾 **Persistent tunnels** - Remembers your configurations between sessions

## Features

- ✅ Automatic Cloudflare tunnel creation and management
- ✅ DNS route configuration via Cloudflare API
- ✅ Laravel Herd domain linking
- ✅ HTTPS support with `--secure` flag
- ✅ Multiple tunnel management
- ✅ Configuration persistence
- ✅ Graceful cleanup and removal
- ✅ Error recovery and validation

## Installation

### Prerequisites

Before installing cftunnel, ensure you have the following:

1. **Laravel Herd** - [Download from herd.laravel.com](https://herd.laravel.com)
2. **Cloudflared** - Cloudflare tunnel client
3. **jq** - JSON processor
4. **A domain managed by Cloudflare**

### Install Dependencies

#### macOS (using Homebrew)

```bash
brew install cloudflared jq
```

### Install cftunnel

#### Option 1: Direct Download (Recommended)

```bash
# Download and install
curl -L https://raw.githubusercontent.com/iamswap/cftunnel/main/cftunnel -o cftunnel
chmod +x cftunnel

# Install globally (optional)
sudo mv cftunnel /usr/local/bin/
```

#### Option 2: Clone Repository

```bash
git clone https://github.com/iamswap/cftunnel.git
cd cftunnel
chmod +x cftunnel

# Install globally (optional)
./cftunnel install
```

### Initial Setup

1. **Authenticate with Cloudflare:**

   ```bash
   cloudflared login
   ```

   This opens your browser to authenticate with your Cloudflare account.

2. **Verify installation:**
   ```bash
   cftunnel help
   ```

## Quick Start

1. **Navigate to your Laravel project:**

   ```bash
   cd /path/to/your/laravel/project
   ```

2. **Share your site:**

   ```bash
   cftunnel share myapp.com
   ```

3. **Your site is now live at `https://myapp.com`** 🎉

## Usage

### Basic Commands

#### Share a site

```bash
# Basic sharing
cftunnel share example.com

# With HTTPS (requires Herd secure)
cftunnel share example.com --secure
```

#### List active tunnels

```bash
cftunnel list
```

#### Remove a tunnel

```bash
cftunnel remove example.com
```

#### Clean up corrupted configurations

```bash
cftunnel cleanup example.com
```

### Advanced Examples

#### Working with multiple sites

```bash
# Share different projects on different domains
cd ~/Sites/project1
cftunnel share project1.yourdomain.com

cd ~/Sites/project2
cftunnel share project2.yourdomain.com

# List all active tunnels
cftunnel list
```

#### Using with HTTPS

```bash
# First secure your site with Herd
herd secure myapp

# Then share with HTTPS
cftunnel share myapp.com --secure
```

#### Development workflow

```bash
# Start your development session
cd ~/Sites/my-laravel-app
cftunnel share staging.mycompany.com

# Your site is now accessible at https://staging.mycompany.com
# Share this URL with clients, team members, or for testing

# When done, stop with Ctrl+C
# The tunnel configuration is saved for next time
```

## How It Works

1. **Tunnel Creation**: Creates a Cloudflare tunnel for your domain
2. **DNS Configuration**: Automatically sets up DNS routing through Cloudflare (overwrites stale records)
3. **Host Header Routing**: Sends the correct `Host` header (`project.test`) so Herd serves the right site
4. **Herd Integration**: Links your domain with Laravel Herd's local server
5. **Configuration Persistence**: Saves tunnel information for reuse

```
┌─────────────────┐    ┌──────────────────┐    ┌─────────────────┐
│   Your Domain   │───▶│ Cloudflare Tunnel │───▶│ Laravel Herd    │
│ (example.com)   │    │  (Host: *.test)  │    │ (example.test)  │
└─────────────────┘    └──────────────────┘    └─────────────────┘
```

> **Note:** cftunnel detects the Herd site name from your current directory (e.g., `~/Sites/myapp` becomes `myapp.test`).

## Configuration

### Config File Location

- **macOS/Linux**: `~/.cftunnel/config.json`

### Config File Structure

```json
{
  "tunnels": {
    "example.com": {
      "id": "12345678-1234-1234-1234-123456789abc",
      "name": "herd-example-com"
    }
  }
}
```

### Cloudflared Configs

Individual tunnel configurations are stored in:

- `~/.cloudflared/config-{domain}.yml`

## Troubleshooting

### Common Issues

#### "cloudflared is not installed"

Install cloudflared using the installation instructions above.

#### "Laravel Herd is not installed"

Download and install Laravel Herd from [herd.laravel.com](https://herd.laravel.com).

#### "Domain not accessible"

1. Ensure your domain is managed by Cloudflare
2. Check that you're authenticated: `cloudflared login`
3. Verify Herd is running: `herd status`

#### "Tunnel creation failed"

1. Check your Cloudflare authentication: `cloudflared login`
2. Ensure you have permission to create tunnels
3. Try cleaning up: `cftunnel cleanup yourdomain.com`

#### "DNS route already exists"

cftunnel automatically overwrites stale DNS records, so this should not occur. If it does, run `cftunnel cleanup yourdomain.com` and try again.

### Debug Mode

For detailed output, you can modify the script to add debugging:

```bash
# Add to the top of the script after set -e
set -x  # Enable debug mode
```

### Getting Help

1. Check the help: `cftunnel help`
2. Verify dependencies: `cftunnel` will check and report missing requirements
3. Check Cloudflare tunnel status: `cloudflared tunnel list`
4. Check Herd status: `herd status`

## Contributing

We welcome contributions! Here's how you can help:

### Development Setup

1. Fork the repository
2. Clone your fork: `git clone https://github.com/iamswap/cftunnel.git`
3. Make your changes
4. Test thoroughly with different scenarios
5. Submit a pull request

### Testing Guidelines

Since this is a Bash script, testing involves:

1. Testing with different domain configurations
2. Verifying tunnel creation and cleanup
3. Testing error conditions and recovery
4. Checking integration with Herd and Cloudflare

### Code Style

- Follow existing code patterns
- Use meaningful function and variable names
- Add comments for complex logic
- Maintain backward compatibility

## Security

### Security Considerations

- **Tunnel Access**: Only you control the tunnel through your Cloudflare account
- **Domain Ownership**: Requires domain ownership and Cloudflare management
- **Local Access**: Tunnels only expose what Herd already serves locally
- **Credentials**: Uses Cloudflare's secure authentication flow

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Changelog

### v1.1.0

- Fix Host header routing for Herd (uses `httpHostHeader` so Herd serves the correct site)
- Auto-detect Herd site name from current directory
- Use `--overwrite-dns` to avoid stale CNAME conflicts

### v1.0.0

- Initial release
- Basic tunnel creation and management
- Laravel Herd integration
- DNS route automation
- Configuration persistence

## Acknowledgments

- [Cloudflare](https://cloudflare.com) for the amazing tunnel technology
- [Laravel Herd](https://herd.laravel.com) for simplifying local PHP development
- The open source community for inspiration and feedback

## Support

- 📖 **Documentation**: Check this README and `cftunnel help`
- 🐛 **Bug Reports**: [Open an issue](https://github.com/laravel/cftunnel/issues)
- 💬 **Discussions**: [GitHub Discussions](https://github.com/laravel/cftunnel/discussions)
- 🚀 **Feature Requests**: [Request a feature](https://github.com/laravel/cftunnel/issues/new?template=feature_request.md)

---

Made with ❤️ for the Laravel and PHP community.
