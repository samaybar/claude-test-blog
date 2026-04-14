---
title: "Give Claude.ai Superpowers: Deploy Your Own GitHub and Railway MCP Servers"
description: "A step-by-step guide to setting up MCP servers that let Claude.ai manage your GitHub repos and Railway deployments directly."
pubDate: 2026-04-14
tags: ["mcp", "claude", "github", "railway", "tutorial"]
---

What if Claude could create GitHub repos, push code, open pull requests, and deploy services to Railway - all from a conversation? With MCP (Model Context Protocol) servers, it can.

This guide walks you through deploying two MCP servers that give Claude.ai direct access to your GitHub and Railway accounts. You'll have everything running in about 15 minutes.

## What You're Building

**GitHub MCP Server** - Lets Claude:
- Create repositories (private by default)
- Read and write files
- Create branches and pull requests
- Search code across your repos

**Railway MCP Server** - Lets Claude:
- Create and manage Railway projects
- Deploy services from GitHub repos
- Set environment variables
- View logs and trigger redeploys
- Query PostgreSQL databases

Both servers run on Railway with OAuth protection, so only you can access them.

## Prerequisites

You'll need:
- A GitHub account
- A Railway account (free tier works)
- Access to Claude.ai with MCP support

Don't have these yet? Create them now:
1. [Sign up for GitHub](https://github.com/signup)
2. [Sign up for Railway](https://railway.com/) (you can sign in with GitHub)

## Step 1: Create Your GitHub Token

Claude needs a GitHub Personal Access Token to act on your behalf. We'll use a **Classic token** because it can create repositories (fine-grained tokens can't).

1. Go to [github.com/settings/tokens](https://github.com/settings/tokens)
2. Click **Generate new token** > **Generate new token (classic)**
3. Fill in:
   - **Note**: "Claude MCP Server" (or whatever helps you remember)
   - **Expiration**: 90 days (or longer)
4. Select the **repo** scope (this grants full access to repositories)
5. Click **Generate token**
6. **Copy the token immediately** - it looks like `ghp_xxxxxxxxxxxx` and you won't see it again!

Save this somewhere safe. You'll need it in Step 3.

## Step 2: Create Your Railway API Token

1. Go to [railway.com/account/tokens](https://railway.com/account/tokens)
2. Click **Create Token**
3. Give it a name like "Claude MCP Server"
4. **Copy the token immediately**

Save this too. You'll need it in Step 4.

## Step 3: Deploy the GitHub MCP Server

1. Go to [railway.com/new](https://railway.com/new)
2. Click **Deploy from GitHub repo**
3. If prompted, connect your GitHub account
4. Search for and select: `samaybar/github-mcp-server`
   - Or fork [github.com/samaybar/github-mcp-server](https://github.com/samaybar/github-mcp-server) first and select your fork
5. Railway will create the project and start building

**Add environment variables:**
1. Click on the service in your new project
2. Go to **Variables**
3. Add these variables:

| Variable | Value |
|----------|-------|
| `GITHUB_TOKEN` | The `ghp_...` token from Step 1 |
| `AUTH_PASSWORD` | Choose a password (you'll use this to connect Claude) |
| `DATA_DIR` | `/app/data` |

**Add a volume for persistent auth:**
1. Go to **Settings** > **Volumes**
2. Click **Add Volume**
3. Set mount path to `/app/data`

**Generate a public domain:**
1. Go to **Settings** > **Networking**
2. Click **Generate Domain**
3. Note the URL (e.g., `github-mcp-server-production-xxxx.up.railway.app`)

## Step 4: Deploy the Railway MCP Server

Repeat the same process for the Railway MCP server:

1. Go to [railway.com/new](https://railway.com/new)
2. Click **Deploy from GitHub repo**
3. Search for and select: `samaybar/railway-mcp-server`
   - Or fork [github.com/samaybar/railway-mcp-server](https://github.com/samaybar/railway-mcp-server) first

**Add environment variables:**

| Variable | Value |
|----------|-------|
| `RAILWAY_API_TOKEN` | The token from Step 2 |
| `AUTH_PASSWORD` | Choose a password (can be different from the GitHub one) |
| `DATA_DIR` | `/app/data` |

**Add a volume:**
1. Go to **Settings** > **Volumes**
2. Click **Add Volume**
3. Set mount path to `/app/data`

**Generate a public domain:**
1. Go to **Settings** > **Networking**
2. Click **Generate Domain**
3. Note this URL too (e.g., `railway-mcp-server-production-xxxx.up.railway.app`)

## Step 5: Connect Both Servers to Claude.ai

Now let's wire everything up to Claude.

### Connect the GitHub MCP Server

1. Open [Claude.ai](https://claude.ai)
2. Go to **Settings** > **Integrations**
3. Click **Add MCP Server**
4. Enter your GitHub MCP URL with `/mcp` at the end:
   ```
   https://github-mcp-server-production-xxxx.up.railway.app/mcp
   ```
5. When prompted, enter the `AUTH_PASSWORD` you chose for the GitHub MCP
6. Claude should confirm the connection

### Connect the Railway MCP Server

1. Still in Claude.ai Settings > Integrations
2. Click **Add MCP Server** again
3. Enter your Railway MCP URL with `/mcp` at the end:
   ```
   https://railway-mcp-server-production-xxxx.up.railway.app/mcp
   ```
4. When prompted, enter the `AUTH_PASSWORD` you chose for the Railway MCP
5. Claude should confirm this connection too

You now have both MCP servers connected!

## Step 6: Test Both Connections

Start a new conversation with Claude and verify both connections work:

> "Check my GitHub connection"

Claude should respond with your GitHub username, confirming the GitHub MCP is working.

> "Check my Railway connection"

Claude should respond with your Railway account info, confirming the Railway MCP is working.

If both work, you're all set!

## What Can You Do Now?

Here are some things to try:

### GitHub Operations
- "Create a new private repo called 'my-test-project'"
- "Show me my recent repositories"
- "Create a file called hello.txt in my-test-project with the content 'Hello, World!'"
- "Create a branch called 'feature/add-readme' in my-test-project"

### Railway Operations
- "List my Railway projects"
- "Create a new Railway project called 'claude-deployed'"
- "Deploy the PostgreSQL template to my project"
- "Show me the logs for my service"

### Combining Both
- "Create a new GitHub repo, add a simple Node.js app, then deploy it to Railway"

## How It Works

Both MCP servers act as secure bridges between Claude and the respective APIs:

```
Claude.ai <--OAuth--> Your MCP Server <--API Token--> GitHub/Railway
```

- **Your tokens stay on your servers** - Claude never sees them directly
- **OAuth protects access** - Only someone with your password can use the MCP
- **Volumes persist auth** - You won't need to re-authenticate after redeploys

## Security Notes

- Each MCP server runs in your own Railway account
- You control the API tokens and can revoke them anytime
- The `AUTH_PASSWORD` prevents unauthorized access to your MCP endpoints
- All communication happens over HTTPS

## Troubleshooting

**"Unknown client_id" error when connecting**
The server lost its auth state. Make sure you added a volume mounted at `/app/data`. If the error persists, remove and re-add the MCP connection in Claude.

**"Failed to create repo" error**
Make sure you used a Classic token with the `repo` scope, not a fine-grained token.

**Server not responding**
Check the Railway dashboard to make sure the service is running. Verify you generated a public domain.

**Can't find the service URL**
Go to your Railway project, click on the service, then Settings > Networking. The domain is listed there.

## Wrapping Up

You now have Claude.ai connected to your GitHub and Railway accounts. This opens up a lot of possibilities - from quick repo management to full deployment workflows, all through natural conversation.

The source code for both servers is open:
- [github-mcp-server](https://github.com/samaybar/github-mcp-server)
- [railway-mcp-server](https://github.com/samaybar/railway-mcp-server)

Feel free to fork them, add more tools, or adapt them for your own needs.
