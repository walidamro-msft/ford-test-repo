# Accessing GitHub Repos Using GitHub Copilot in Visual Studio Code

This guide walks you through setting up **GitHub Copilot** in **Visual Studio Code** to interact with your GitHub repositories using the **GitHub MCP Server**.

---

## Table of Contents

1. [Prerequisites](#prerequisites)
2. [Install Required VS Code Extensions](#install-required-vs-code-extensions)
3. [Generate a GitHub Personal Access Token (PAT)](#generate-a-github-personal-access-token-pat)
4. [Configure the GitHub MCP Server](#configure-the-github-mcp-server)
5. [Verify the Connection](#verify-the-connection)
6. [What Can You Do?](#what-can-you-do)
7. [Troubleshooting](#troubleshooting)

---

## Prerequisites

- [Visual Studio Code](https://code.visualstudio.com/) installed (version 1.99 or later recommended)
- A [GitHub account](https://github.com/)
- [Node.js](https://nodejs.org/) installed (version 18 or later) — needed if using the `npx` method
- An active GitHub Copilot subscription (Individual, Business, or Enterprise)

---

## Install Required VS Code Extensions

Install the following extensions from the VS Code Marketplace:

| Extension | Publisher | Purpose |
|-----------|-----------|---------|
| **GitHub Copilot** | GitHub | AI-powered code suggestions and chat |
| **GitHub Copilot Chat** | GitHub | Interactive chat interface for Copilot |
| **GitHub Pull Requests** | GitHub | PR and issue management integration |

### How to Install

1. Open VS Code
2. Press `Ctrl+Shift+X` to open the Extensions panel
3. Search for each extension by name
4. Click **Install** for each one
5. Sign in to GitHub when prompted

---

## Generate a GitHub Personal Access Token (PAT)

The GitHub MCP Server requires a **Personal Access Token (PAT)** to authenticate API requests to GitHub on your behalf.

### Step 1: Navigate to GitHub Token Settings

1. Go to [https://github.com/settings/tokens](https://github.com/settings/tokens)
2. Click **"Generate new token"** → Choose **"Generate new token (classic)"**

> **Tip:** You can also use [Fine-grained tokens](https://github.com/settings/personal-access-tokens/new) for more granular control.

### Step 2: Configure Token Scopes

For full repository access, select the following scopes:

| Scope | Purpose |
|-------|---------|
| `repo` | Full control of private repositories |
| `read:org` | Read org membership (if accessing org repos) |
| `read:user` | Read user profile data |
| `user:email` | Access user email addresses |
| `read:project` | Read project boards |

### Step 3: Generate and Copy the Token

1. Set an **expiration date** (e.g., 90 days)
2. Click **"Generate token"**
3. **Copy the token immediately** — you won't be able to see it again!

> **Important:** Your token will start with `ghp_` (classic) or `github_pat_` (fine-grained). Store it securely.

---

## Configure the GitHub MCP Server

The MCP (Model Context Protocol) Server allows GitHub Copilot to interact with your GitHub repositories directly from VS Code chat.

### Step 1: Open the MCP Configuration File

1. Press `Ctrl+Shift+P` to open the Command Palette
2. Type **"Preferences: Open User Settings (JSON)"** and select it
3. Alternatively, navigate directly to the `mcp.json` file:
   ```
   Windows: %APPDATA%\Code\User\mcp.json
   macOS:   ~/Library/Application Support/Code/User/mcp.json
   Linux:   ~/.config/Code/User/mcp.json
   ```

> **Note:** If the `mcp.json` file doesn't exist, create it manually in the location above.

### Step 2: Add the GitHub MCP Server Configuration

There are **two methods** to configure the server:

#### Method 1: Using the Pre-built Binary (Recommended)

First, download the official GitHub MCP Server binary from:
[https://github.com/github/github-mcp-server/releases](https://github.com/github/github-mcp-server/releases)

Download the appropriate version for your OS (e.g., `github-mcp-server_Windows_x86_64.zip`), extract it, and note the path to the executable.

Then add the following to your `mcp.json`:

```jsonc
{
    "servers": {
        "io.github.github/github-mcp-server": {
            "type": "stdio",
            "command": "C:\\path\\to\\github-mcp-server.exe",
            "args": [
                "stdio"
            ],
            "env": {
                "GITHUB_PERSONAL_ACCESS_TOKEN": "YOUR_PAT_TOKEN_HERE"
            }
        }
    }
}
```

> **Replace** `C:\\path\\to\\github-mcp-server.exe` with the actual path where you extracted the binary.  
> **Replace** `YOUR_PAT_TOKEN_HERE` with your GitHub PAT token.

#### Method 2: Using Docker

If you have Docker Desktop installed and running:

```jsonc
{
    "servers": {
        "io.github.github/github-mcp-server": {
            "type": "stdio",
            "command": "docker",
            "args": [
                "run",
                "-i",
                "--rm",
                "-e",
                "GITHUB_PERSONAL_ACCESS_TOKEN=YOUR_PAT_TOKEN_HERE",
                "ghcr.io/github/github-mcp-server:latest"
            ]
        }
    }
}
```

> **Replace** `YOUR_PAT_TOKEN_HERE` with your GitHub PAT token.

### Step 3: Secure Your Token (Optional but Recommended)

Instead of hardcoding your PAT in the config file, you can use VS Code's input prompt feature:

```jsonc
{
    "servers": {
        "io.github.github/github-mcp-server": {
            "type": "stdio",
            "command": "C:\\path\\to\\github-mcp-server.exe",
            "args": [
                "stdio"
            ],
            "env": {
                "GITHUB_PERSONAL_ACCESS_TOKEN": "${input:github-pat}"
            }
        }
    },
    "inputs": [
        {
            "id": "github-pat",
            "type": "promptString",
            "description": "Enter your GitHub Personal Access Token",
            "password": true
        }
    ]
}
```

This will prompt you for the token each time the MCP server starts, keeping it out of plain text files.

### Step 4: Reload VS Code

After saving `mcp.json`, reload VS Code:

1. Press `Ctrl+Shift+P`
2. Type **"Developer: Reload Window"**
3. Press Enter

---

## Verify the Connection

1. Open **GitHub Copilot Chat** (`Ctrl+Shift+I` or click the Copilot icon)
2. Switch to **Agent Mode** (select "Agent" from the chat mode dropdown)
3. Type: **"Can you access my GitHub repos?"**
4. Copilot should respond with your GitHub profile information

You can also try:
- *"List all my repos"*
- *"Show me the issues in my repo"*
- *"Create a new repository"*

---

## What Can You Do?

Once connected, you can ask GitHub Copilot to perform many GitHub operations:

| Action | Example Prompt |
|--------|---------------|
| List repositories | *"List all my GitHub repos"* |
| Search repositories | *"Find repos with 'azure' in the name"* |
| Create a repository | *"Create a new private repo called my-project"* |
| Read file contents | *"Show me the README from my repo"* |
| Create/update files | *"Add a .gitignore file to my repo"* |
| Manage issues | *"List open issues in my repo"* |
| Create issues | *"Create an issue about fixing the login bug"* |
| Pull requests | *"Show me open PRs in my repo"* |
| Create PRs | *"Create a PR from feature-branch to main"* |
| Search code | *"Search for TODO across my repos"* |
| Branch management | *"List branches in my repo"* |

---

## Troubleshooting

### MCP Server Won't Start

- **Check Node.js:** Run `node --version` in terminal — must be v18+
- **Check the binary path:** Ensure the path in `mcp.json` points to the correct executable
- **Check Docker:** If using the Docker method, ensure Docker Desktop is running

### Authentication Errors

- **Token expired:** Generate a new PAT at [https://github.com/settings/tokens](https://github.com/settings/tokens)
- **Insufficient scopes:** Ensure your token has the `repo` scope at minimum
- **Token revoked:** Check your GitHub security log for any revoked tokens

### Common Fixes

1. **Reload VS Code** after any config change (`Ctrl+Shift+P` → "Developer: Reload Window")
2. **Check the MCP output panel:** `Ctrl+Shift+U` → select "GitHub MCP Server" from the dropdown
3. **Verify JSON syntax:** Ensure your `mcp.json` is valid JSON (no trailing commas, proper quotes)

---

## Resources

- [GitHub MCP Server Repository](https://github.com/github/github-mcp-server)
- [GitHub Copilot Documentation](https://docs.github.com/en/copilot)
- [VS Code MCP Documentation](https://code.visualstudio.com/docs/copilot/chat/mcp-servers)
- [GitHub PAT Documentation](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens)
- [Model Context Protocol Specification](https://modelcontextprotocol.io/)

---

> **Security Reminder:** Never commit your PAT token to a public repository. Always use environment variables or VS Code's input prompt feature to keep your tokens secure.
