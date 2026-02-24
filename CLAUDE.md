# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**Preventivo Generator** - A Claude Code/Cowork plugin for Becreatives that automates quote/estimate (preventivo) generation in 2 fasi:
1. **Stima** — Analisi progetto, identificazione moduli, scoring complessita/rischio, produzione Scheda Progetto
2. **Scrittura** — Generazione documento Google Docs da template con contenuto adattato dalla Scheda Progetto

Integra la logica di stima progetti (derivata dal custom GPT) con la generazione automatica del documento.

## Plugin

Il Preventivo Generator e distribuito come **plugin** nella directory `preventivo-plugin/`. Funziona sia in Claude Code che in Claude Cowork.

Per usarlo in Claude Code:
```bash
claude --plugin-dir ./preventivo-plugin
```

Per dettagli su installazione e configurazione, vedi `preventivo-plugin/README.md`.

## MCP Server Integration

This project uses the `google_workspace_mcp` server for Google Docs/Slides/Drive access.

- **MCP Config:** `preventivo-plugin/.mcp.json` (plugin-scoped, uses env vars for credentials)
- **MCP Server:** `./google_workspace_mcp` (bundled in project, symlinked into plugin)
- **Google Cloud Project:** `becreatives-mcp`

### Teammate Setup

1. **Set environment variables** (add to your shell profile or `.env` file):
   ```bash
   export GOOGLE_OAUTH_CLIENT_ID="your-client-id.apps.googleusercontent.com"
   export GOOGLE_OAUTH_CLIENT_SECRET="your-client-secret"
   ```
   Get credentials from the team or create your own via Google Cloud Console (see `Setup.md`).

2. **Start Claude Code** with the plugin:
   ```bash
   cd /path/to/Becreatives-Operations
   claude --plugin-dir ./preventivo-plugin
   ```

3. **Approve the MCP server** when prompted on first run.

4. **Authenticate with Google** - a browser window opens on first use.

### Google Account for MCP

Il plugin chiede l'email Google Workspace dell'utente al primo utilizzo di ogni sessione. Usa la tua email `@becreatives.com`.

### Available MCP Tools

Once authenticated, the following Google Workspace tools are available:
- Google Docs: create, read, update documents
- Google Slides: read presentation content
- Google Drive: file management, copy templates

## Architecture

```
Becreatives-Operations/
├── CLAUDE.md                              # This file
├── Setup.md                               # MCP setup guide
├── gpt-instructions.md                    # Logica GPT originale (riferimento)
├── preventivo-plugin/                     # Plugin directory
│   ├── .claude-plugin/plugin.json         # Plugin manifest
│   ├── commands/preventivo.md             # Orchestratore: entry point
│   ├── skills/
│   │   ├── stima/SKILL.md                 # Fase 1: Stima e analisi progetto
│   │   └── scrivi/SKILL.md               # Fase 2: Scrittura documento Google Docs
│   ├── .mcp.json                          # MCP config (env var refs)
│   └── google_workspace_mcp → (symlink)   # MCP server
└── google_workspace_mcp/                  # Bundled MCP server
```

## Templates

I template dei preventivi sono salvati su Google Drive:
- **Folder ID:** `1R5zRxYN40K7CEAq8M9cF2384VW_0S5pr`
- **Link:** https://drive.google.com/drive/folders/1R5zRxYN40K7CEAq8M9cF2384VW_0S5pr

La lista completa dei template e i loro ID si trovano in `preventivo-plugin/skills/scrivi/references/templates.md`.

## Comandi Disponibili

| Comando | Descrizione | Uso standalone |
|---|---|---|
| `/preventivo:preventivo` | Flusso completo: stima + pausa revisione + scrittura documento | Entry point principale |
| `/preventivo:stima` | Solo Fase 1: analisi progetto e produzione Scheda Progetto | Si, per stime senza generare documenti |
| `/preventivo:scrivi` | Solo Fase 2: scrittura documento Google Docs da template | Si, se hai gia le info pronte |

## Key Workflows

### Generate Preventivo (comando: `/preventivo:preventivo`)

Processo a 2 fasi con pausa per revisione:

1. L'utente invoca `/preventivo:preventivo`
2. Sceglie come procedere: brief, definizione guidata, o salto diretto alla scrittura
3. **Fase 1 - Stima** (skill `stima`):
   - Intake del brief (da Slides/Docs/testo o raccolta interattiva)
   - Identificazione moduli (7-15)
   - Gate di allineamento con scoring complessita/rischio (richiede 90%+)
   - Deep dive su rischi, ambiguita, architettura
   - Produzione SCHEDA PROGETTO strutturata
4. **Pausa obbligatoria**: L'utente rivede e approva la Scheda Progetto
5. **Fase 2 - Scrittura** (skill `scrivi`):
   - Selezione template (suggerito dalla Scheda)
   - Raccolta info mancanti (solo cio che non e nella Scheda)
   - Copia template, scoperta placeholder dinamica, compilazione
   - Adattamento contenuto tecnico in linguaggio cliente-facing
   - Verifica e consegna link documento

### Contratto dati: SCHEDA PROGETTO

La Fase 1 produce e la Fase 2 consuma una SCHEDA PROGETTO delimitata da `=== SCHEDA PROGETTO ===` / `=== FINE SCHEDA PROGETTO ===`. Contiene: info generali, moduli con scoring, rischi critici, domande aperte, raccomandazioni, template consigliato.

## External Dependencies

- **Google Workspace MCP:** Bundled in `./google_workspace_mcp` (Taylor Wilsdon's server)
- **Google Cloud APIs:** Docs API, Slides API, Drive API
- **OAuth:** Internal app for Becreatives Workspace

## Files Not in Git

These files contain secrets and are excluded via `.gitignore`:
- `.env` - Your local environment variables
- `client_secret_*.json` - OAuth credentials file
