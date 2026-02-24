# Connectors

Questo plugin richiede accesso alle Google Workspace APIs tramite il Google Workspace MCP server.

## Connector Richiesto

| Servizio | Scopo | Tool MCP Utilizzati |
|----------|-------|---------------------|
| **Google Workspace** | Copia template, lettura/scrittura Docs, lettura Slides | `copy_drive_file`, `get_doc_content`, `find_and_replace_doc`, `modify_doc_text`, `batch_update_doc`, `inspect_doc_structure`, `get_presentation` |

## Setup

### Opzione A: MCP Server Locale (solo Claude Code)

1. Assicurati che `uv` sia installato: `brew install uv`
2. Imposta le variabili d'ambiente nel tuo shell profile:
   ```bash
   export GOOGLE_OAUTH_CLIENT_ID="your-client-id.apps.googleusercontent.com"
   export GOOGLE_OAUTH_CLIENT_SECRET="your-client-secret"
   ```
   Ottieni le credenziali dal team o crea le tue via Google Cloud Console.
3. Il server MCP bundled in `google_workspace_mcp/` si avviera automaticamente.
4. Al primo utilizzo si aprira una finestra browser per l'autenticazione Google.

### Opzione B: MCP Server Remoto HTTP (Claude Code + Cowork)

1. Deploy del Google Workspace MCP come endpoint HTTP (es: Google Cloud Run usando il Dockerfile incluso)
2. Aggiorna `.mcp.json` nel plugin:
   ```json
   {
     "mcpServers": {
       "google-workspace": {
         "type": "http",
         "url": "https://your-deployed-endpoint.run.app/mcp"
       }
     }
   }
   ```

**Nota**: L'Opzione A funziona solo in Claude Code. Per Claude Cowork e necessario il deploy remoto (Opzione B).

## API Google Richieste

- Google Docs API
- Google Slides API
- Google Drive API
