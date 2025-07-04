# Coding from iOS with Claude Code: Complete Setup Guide

This comprehensive guide walks you through setting up a complete development environment for coding from iOS using Claude Code, including VPS setup, GitHub CLI configuration, and project bootstrapping.

## Overview

This guide covers:
1. **VPS Setup** - Getting a cloud server (Digital Ocean, AWS, etc.)
2. **Server Configuration** - Installing essential tools and GitHub CLI
3. **iOS Access** - Connecting from iOS devices using SSH
4. **Claude Code Integration** - Setting up the development workflow
5. **Project Bootstrapping** - Quick project creation with GitHub CLI

## Part 1: VPS Setup

### 1.1 Generate SSH Keys on iOS

#### Method 1: 1Password (Recommended)
1. Open 1Password iOS app
2. Tap "+" → "SSH Key"
3. Name it (e.g., "VPS Development Key")
4. Choose Ed25519 key type
5. Generate the key
6. Copy the public key for VPS setup

#### Method 2: iOS Terminal Apps
If you don't have 1Password, use a terminal app:
- **Termius** (Free/Pro) - Built-in key generation
- **Blink Shell** ($20) - Full SSH management
- **SSH Files** (Free) - Basic key support

```bash
ssh-keygen -t ed25519 -C "your-email@example.com"
cat ~/.ssh/id_ed25519.pub  # Copy this public key
```

### 1.2 Create a VPS

#### Digital Ocean (Recommended)
1. Go to https://cloud.digitalocean.com/
2. Create Droplets → Ubuntu 22.04 LTS
3. Choose Basic plan ($4-6/month)
4. **Add SSH Key**: Paste your public key from 1Password/terminal
5. Create Droplet

#### AWS EC2 Alternative
1. Go to https://aws.amazon.com/ec2/
2. Launch Instance → Ubuntu Server 22.04 LTS
3. Choose t2.micro (free tier eligible)
4. Configure security group (allow SSH port 22)
5. Create or select key pair
6. Launch instance

#### Other VPS Providers
- **Linode**: $5/month basic plans
- **Vultr**: $2.50/month starting
- **Hetzner**: €3.29/month in EU

### 1.2 Initial Server Setup

```bash
# Connect to your VPS
ssh root@your-vps-ip

# Update system
apt update && apt upgrade -y

# Install essential tools
apt install -y curl wget git vim htop

# Create non-root user
adduser developer
usermod -aG sudo developer

# Copy SSH keys to new user
rsync --archive --chown=developer:developer ~/.ssh /home/developer
```

## Part 2: Server Configuration

### 2.1 Install GitHub CLI

```bash
# Install GitHub CLI
curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg | sudo dd of=/usr/share/keyrings/githubcli-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | sudo tee /etc/apt/sources.list.d/github-cli.list > /dev/null
apt update
apt install gh -y

# Authenticate with GitHub
gh auth login
# Follow prompts: GitHub.com → HTTPS → Authenticate via web browser
```

### 2.2 Install Claude Code

```bash
# Install Claude Code CLI
curl -fsSL https://anthropic.com/claude-code/install.sh | sh

# Or using npm (if Node.js is installed)
npm install -g @anthropic/claude-code

# Verify installation
claude-code --version
```

### 2.3 Development Environment Setup

```bash
# Install Node.js (for web development)
curl -fsSL https://deb.nodesource.com/setup_lts.x | sudo -E bash -
apt-get install -y nodejs

# Install Python (usually pre-installed)
apt install -y python3 python3-pip

# Install Docker (optional)
curl -fsSL https://get.docker.com -o get-docker.sh
sh get-docker.sh
usermod -aG docker developer
```

## Part 3: iOS Access Setup

### 3.1 SSH Configuration

```bash
# Edit SSH config for better security
sudo vim /etc/ssh/sshd_config

# Recommended settings:
# Port 2222                    # Change default port
# PermitRootLogin no          # Disable root login
# PasswordAuthentication no   # Use keys only
# PubkeyAuthentication yes

# Restart SSH service
sudo systemctl restart sshd
```

### 3.2 iOS SSH Clients

**Recommended iOS Apps:**
- **Termius** (Free/Pro) - Best overall experience, 1Password integration
- **Blink Shell** ($20) - Advanced features, 1Password SSH agent support
- **SSH Files** (Free) - File management + terminal

**Connection Setup:**
```
Host: your-vps-ip
Port: 2222 (if changed)
Username: developer
Authentication: SSH Key (from 1Password or terminal app)
```

### 3.3 Using SSH Keys with iOS Apps

#### With 1Password (Recommended)
- **Termius**: Enable 1Password integration in settings
- **Blink Shell**: Configure SSH agent to use 1Password
- Keys are automatically available and secure

#### With Terminal App Keys
```bash
# If you generated keys in terminal app, copy public key to VPS
ssh-copy-id -p 2222 developer@your-vps-ip

# Or manually add to ~/.ssh/authorized_keys
```

## Part 4: Claude Code Workflow

### 4.1 Basic Claude Code Usage

```bash
# Start Claude Code session
claude-code

# Common commands:
claude-code --resume          # Resume previous session
claude-code --project ./      # Start in specific directory
claude-code --help           # View all options
```

### 4.2 iOS-Optimized Workflow

```bash
# Create workspace directory
mkdir -p ~/workspace
cd ~/workspace

# Use screen/tmux for persistent sessions
sudo apt install screen tmux

# Start persistent session
screen -S coding
# or
tmux new-session -s coding

# Inside session, start Claude Code
claude-code --project .

# Detach: Ctrl+A, D (screen) or Ctrl+B, D (tmux)
# Reattach: screen -r coding or tmux attach -t coding
```

## Part 5: Project Bootstrapping with GitHub CLI

### Prerequisites

- VPS with GitHub CLI installed and authenticated
- SSH access from iOS device
- Claude Code installed and configured

### 1. Create GitHub Repository

```bash
# Initialize git if not already done
git init

# Create GitHub repo from current directory
gh repo create your-project-name --source=. --public
```

### 2. Create README (Optional)

```bash
# Create a basic README file
echo "# Your Project Name" > README.md
echo "" >> README.md
echo "Project description goes here." >> README.md
```

### 3. Configure Git with GitHub CLI

```bash
# Setup git authentication with GitHub
gh auth setup-git

# Set git user info from GitHub account
git config user.name "$(gh api user --jq .login)"
git config user.email "$(gh api user --jq .email)"

# Set default repository for easier operations
gh repo set-default owner/repo-name
```

### 4. Commit and Push

```bash
# Stage all files
git add .

# Commit with descriptive message
git commit -m "Initial commit

Add README and project setup

🤖 Generated with [Claude Code](https://claude.ai/code)

Co-Authored-By: Claude <noreply@anthropic.com>"

# Push to GitHub
git push -u origin master
```

## Key Benefits

- **Fast Setup**: One-command repository creation
- **Integrated Auth**: GitHub CLI handles authentication seamlessly  
- **Auto-Configuration**: Git user info pulled from GitHub account
- **Remote Setup**: Origin remote configured automatically

## Part 6: Troubleshooting

### Common VPS Issues

```bash
# SSH connection refused
sudo systemctl status sshd
sudo systemctl restart sshd

# Port not accessible
sudo ufw allow 2222/tcp
sudo ufw enable

# Out of disk space
df -h
sudo apt autoremove
sudo apt autoclean
```

### GitHub CLI Issues

```bash
# Authentication problems
gh auth status
gh auth logout
gh auth login

# Permission denied
gh auth refresh --scopes repo,workflow

# Repository not found
gh repo set-default owner/repo-name
```

### iOS-specific Troubleshooting

#### Connection Drops
- **Issue**: SSH connection drops frequently
- **Solution**: Add to `~/.ssh/config`:
```
Host your-vps-ip
    ServerAliveInterval 60
    ServerAliveCountMax 3
```

#### Keyboard Issues
- **Issue**: Special keys not working in iOS terminal apps
- **Solutions**:
  - Termius: Use custom keyboard with Esc, Tab, Ctrl keys
  - Blink Shell: Configure external keyboard shortcuts
  - General: Use `screen` or `tmux` for better terminal management

#### File Transfer
```bash
# Using scp from iOS (in supported apps)
scp -P 2222 local-file developer@your-vps-ip:~/

# Using rsync
rsync -avz -e "ssh -p 2222" ./local-dir/ developer@your-vps-ip:~/remote-dir/
```

### Claude Code Issues

```bash
# API key not set
export ANTHROPIC_API_KEY="your-key-here"
echo 'export ANTHROPIC_API_KEY="your-key-here"' >> ~/.bashrc

# Session not resuming
claude-code --resume --session-id your-session-id

# Memory issues on small VPS
# Upgrade to larger droplet or use swap:
sudo fallocate -l 1G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
```

### Project-specific Troubleshooting

- If commit fails due to missing user identity, use `gh auth setup-git` first
- For private repos, use `--private` flag instead of `--public`
- Check authentication status with `gh auth status`
- For large files, consider Git LFS: `git lfs install`

## Part 7: Tips for iOS Development

### Optimizing the Experience

1. **Use Persistent Sessions**: Always use `screen` or `tmux`
2. **Configure Vim**: Install proper vim configuration for better editing
3. **Alias Commands**: Create shortcuts for common operations
4. **Backup Regularly**: Set up automated backups to GitHub

### Sample Aliases

```bash
# Add to ~/.bashrc
alias ll='ls -la'
alias gc='git commit'
alias gs='git status'
alias cc='claude-code'
alias proj='cd ~/workspace && claude-code --resume'

# Quick project setup
alias newproj='mkdir -p ~/workspace/$1 && cd ~/workspace/$1 && git init && gh repo create $1 --source=. --public'
```

### Recommended VPS Specs

- **Minimum**: 1 CPU, 1GB RAM, 25GB SSD ($4-6/month)
- **Recommended**: 1 CPU, 2GB RAM, 50GB SSD ($10-12/month)  
- **Heavy Development**: 2 CPU, 4GB RAM, 80GB SSD ($20-24/month)

## Conclusion

This setup creates a powerful development environment accessible from any iOS device. The combination of VPS + GitHub CLI + Claude Code provides:

- **Professional Development**: Full Linux environment with all tools
- **Persistent Sessions**: Work continues even when disconnected
- **Version Control**: Seamless GitHub integration
- **AI Assistance**: Claude Code for enhanced productivity
- **iOS Accessibility**: Code anywhere with just an iPad/iPhone

The workflow creates a complete project setup in minutes rather than manual repository creation and configuration, while providing the flexibility of a full development environment accessible from mobile devices.