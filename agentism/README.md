# Bootstrapping a Project with GitHub CLI

This document summarizes how to quickly bootstrap a new project using GitHub CLI and Hugo.

## Prerequisites

- GitHub CLI (`gh`) installed and authenticated
- Hugo static site generator installed
- Git initialized in your project directory

## Steps

### 1. Create GitHub Repository

```bash
# Initialize git if not already done
git init

# Create GitHub repo from current directory
gh repo create your-project-name --source=. --public
```

### 2. Setup Hugo Blog (Optional)

```bash
# Create new Hugo site in current directory
hugo new site your-site-name --force

# Navigate to Hugo site directory
cd your-site-name

# Create minimal theme
hugo new theme minimal

# Configure site in hugo.toml
# - Set baseURL, title, theme
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
git commit -m "Initial project setup

- Project structure created
- Configuration files added
- Ready for development

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

## Troubleshooting

- If commit fails due to missing user identity, use `gh auth setup-git` first
- For private repos, use `--private` flag instead of `--public`
- Check authentication status with `gh auth status`

This workflow creates a complete project setup in minutes rather than manual repository creation and configuration.