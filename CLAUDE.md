# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**Preventivo Generator** - A Claude Code skill suite for Becreatives that automates quote/estimate (preventivo) generation in 2 fasi:
1. **Stima** — Analisi progetto, identificazione moduli, scoring complessita/rischio, produzione Scheda Progetto
2. **Scrittura** — Generazione documento Google Docs da template con contenuto adattato dalla Scheda Progetto

Integra la logica di stima progetti (derivata dal custom GPT) con la generazione automatica del documento.

## MCP Server Integration

This project uses the `google_workspace_mcp` server for Google Docs/Slides/Drive access.

- **MCP Config:** `.mcp.json` (project-scoped, uses env vars for credentials)
- **MCP Server:** `./google_workspace_mcp` (bundled in project)
- **Google Cloud Project:** `becreatives-mcp`

### Teammate Setup

1. **Set environment variables** (add to your shell profile or `.env` file):
   ```bash
   export GOOGLE_OAUTH_CLIENT_ID="your-client-id.apps.googleusercontent.com"
   export GOOGLE_OAUTH_CLIENT_SECRET="your-client-secret"
   ```
   Get credentials from the team or create your own via Google Cloud Console (see `Setup.md`).

2. **Start Claude Code** from the project directory:
   ```bash
   cd /path/to/Becreatives-Operations
   claude
   ```

3. **Approve the MCP server** when prompted on first run.

4. **Authenticate with Google** - a browser window opens on first use.

### Google Account for MCP

When using MCP tools that require a Google email parameter (`user_google_email`), always use:
```
v.dipalo@becreatives.com
```

### Available MCP Tools

Once authenticated, the following Google Workspace tools are available:
- Google Docs: create, read, update documents
- Google Slides: read presentation content
- Google Drive: file management, copy templates

## Architecture

```
Becreatives-Operations/
├── CLAUDE.md                    # This file
├── Setup.md                     # MCP setup guide
├── gpt-instructions.md          # Logica GPT originale (riferimento)
├── .claude/
│   └── commands/
│       ├── preventivo.md        # Orchestratore: entry point /preventivo
│       ├── preventivo-stima.md  # Fase 1: Stima e analisi progetto
│       └── preventivo-scrivi.md # Fase 2: Scrittura documento Google Docs
└── google_workspace_mcp/        # Bundled MCP server
```

## Templates

I template dei preventivi sono salvati su Google Drive:
- **Folder ID:** `1R5zRxYN40K7CEAq8M9cF2384VW_0S5pr`
- **Link:** https://drive.google.com/drive/folders/1R5zRxYN40K7CEAq8M9cF2384VW_0S5pr

### Template Disponibili

**Sviluppo:**
- Applicazione (`1fjIBwrTmb5O3t2z9IREEDXvt19ED0NP9z5PjACbwixU`)
- Applicazione AR (`1gsoXI6EVf9kftKeRvN868ntu9lDhxxXNa2GKr-gX93s`)
- E-commerce Shopify custom (`1Azp9BrVb6rPCDWFaPLh6RJsRQdPLpYDR5sKY9-IRweY`)
- E-commerce Shopify headless (`19LkAm-hwHQ48T7keoVw95DME3RQeyn5Yz3mmImxaz5c`)
- E-commerce WooCommerce (`1t5XiVZePXBTmq6g-9vcXf4RmZbMqWwUkr2YJabEalog`)
- Piattaforma web Custom (`1XJ7xSYPDAZF2awSyF3GF9OD8fhqh9LwXveCEu4V5nMg`)
- Piattaforma web Wordpress (`12yd6_rOh47cFdXSgh1w7Lnfrj6m7p7jMixn1eQIO4Wo`)

**Manutenzione:**
- Manutenzione evolutiva (`1XzFErDBzkdqNBIrildYq2PCeFWA7WMCUolwFipdRKeU`)
- Manutenzione ordinaria a giornate (`1EWwvj3Oio4e2rgxxWoqaaRLuH3OrwuO9OsshdnZvmlI`)
- Manutenzione ordinaria pacchetto a consumo (`1bWQr2cpQfH8EIMoUNxpV31YDS13Vye-Vn7Cone1kj7U`)
- Manutenzione ordinaria pacchetto mensile (`1cXbpjjyFelt65NaLkSp7qqpfYWBcQ_bw-tirW3Wj3hg`)

## Comandi Disponibili

| Comando | Descrizione | Uso standalone |
|---|---|---|
| `/preventivo` | Flusso completo: stima + pausa revisione + scrittura documento | Entry point principale |
| `/preventivo-stima` | Solo Fase 1: analisi progetto e produzione Scheda Progetto | Si, per stime senza generare documenti |
| `/preventivo-scrivi` | Solo Fase 2: scrittura documento Google Docs da template | Si, se hai gia le info pronte |

## Key Workflows

### Generate Preventivo (comando: `/preventivo`)

Processo a 2 fasi con pausa per revisione:

1. L'utente invoca `/preventivo`
2. Sceglie come procedere: brief, definizione guidata, o salto diretto alla scrittura
3. **Fase 1 - Stima** (`preventivo-stima.md`):
   - Intake del brief (da Slides/Docs/testo o raccolta interattiva)
   - Identificazione moduli (7-15)
   - Gate di allineamento con scoring complessita/rischio (richiede 90%+)
   - Deep dive su rischi, ambiguita, architettura
   - Produzione SCHEDA PROGETTO strutturata
4. **Pausa obbligatoria**: L'utente rivede e approva la Scheda Progetto
5. **Fase 2 - Scrittura** (`preventivo-scrivi.md`):
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
