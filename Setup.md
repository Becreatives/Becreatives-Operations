# Setup Guide: Google Workspace MCP for Claude Code

This guide walks through setting up Google API access for the Preventivo Generator skill.

## Prerequisites

- macOS with Homebrew
- Google Workspace account (for Internal OAuth)
- Claude Code CLI installed

## Step 1: Install uv (Python Package Manager)

```bash
brew install uv
```

Verify installation:
```bash
uv --version
```

## Step 2: Google Cloud Project Setup

1. Go to [Google Cloud Console](https://console.cloud.google.com/)

2. **Create a new project:**
   - Click the project dropdown → "New Project"
   - Name: `becreatives-mcp` (or your preferred name)
   - Click Create

3. **Enable APIs** (APIs & Services → Library):
   - Google Docs API
   - Google Slides API
   - Google Drive API

4. **Configure OAuth Consent Screen** (Google Auth Platform → Branding):
   - User Type: **Internal** (if using Google Workspace) or **External** (for personal Gmail)
   - App name: `Becreatives MCP`
   - User support email: your email
   - Developer contact: your email
   - Save and continue through the wizard

5. **Create OAuth Credentials** (Google Auth Platform → Client):
   - Click "+ Create Client" → "OAuth client ID"
   - Application type: **Desktop app**
   - Name: `Claude Code`
   - Click Create
   - Download the JSON credentials file

## Step 3: MCP Server

The MCP server is already bundled in the project at `./google_workspace_mcp`. No need to clone separately.

> **Upstream repo:** [taylorwilsdon/google_workspace_mcp](https://github.com/taylorwilsdon/google_workspace_mcp) — refer to the original project for updates and documentation.

## Step 4: Configure Environment Variables

The project uses environment variables for credentials (keeps secrets out of git).

**Option A: Shell profile (recommended)**

Add to your `~/.zshrc` or `~/.bashrc`:
```bash
export GOOGLE_OAUTH_CLIENT_ID="YOUR_CLIENT_ID.apps.googleusercontent.com"
export GOOGLE_OAUTH_CLIENT_SECRET="YOUR_CLIENT_SECRET"
```

Then reload: `source ~/.zshrc`

**Option B: Project `.env` file**

Copy the example and fill in your values:
```bash
cp .env.example .env
# Edit .env with your credentials
```

Note: Claude Code reads environment variables from your shell, not directly from `.env` files. You'll need to `source .env` before running Claude, or use a tool like `direnv`.

Replace:
- `YOUR_CLIENT_ID` with the Client ID from Google Cloud
- `YOUR_CLIENT_SECRET` with the Client Secret from Google Cloud

## Step 5: Authenticate

1. Restart Claude Code (exit and run `claude` again)
2. The MCP server will start automatically
3. On first use of a Google tool, a browser window opens for OAuth
4. Sign in with your Google Workspace account
5. Authorize the requested permissions

## Step 6: Test Connection

In Claude Code, try:
```
List my recent Google Drive files
```

If successful, you'll see your Drive files listed.

## Troubleshooting

### MCP server not starting
- Check that `uv` is installed: `uv --version`
- Verify the path in `.mcp.json` points to your cloned repo
- Check Claude Code logs for errors

### OAuth errors
- Ensure APIs are enabled in Google Cloud Console
- Verify Client ID and Secret are correct in `.mcp.json`
- For Internal OAuth, ensure you're signing in with a Workspace account

### Permission denied
- Re-authenticate by deleting the token file in `~/google_workspace_mcp/`
- Try the OAuth flow again

## Security Notes

- **Environment variables**: Credentials are stored in env vars, not in `.mcp.json`
- **`.gitignore`**: The project excludes `.env` and `client_secret_*.json` from git
- **OAuth token**: Stored locally by the MCP server in `google_workspace_mcp/.credentials/`
- **Never commit**: Credentials or tokens to version control
