# Preventivo Generator Plugin

Plugin per Claude Code e Claude Cowork che genera preventivi professionali in 2 fasi per il team Becreatives.

## Funzionalita

1. **Stima** (`/preventivo:stima`) — Analisi progetto CTO-level: identificazione moduli, scoring complessita/rischio, produzione Scheda Progetto strutturata
2. **Scrittura** (`/preventivo:scrivi`) — Generazione documento Google Docs da template con contenuto adattato dalla Scheda Progetto
3. **Flusso completo** (`/preventivo:preventivo`) — Orchestratore che guida entrambe le fasi con pausa per revisione

## Prerequisiti

- Account Google Workspace Becreatives
- Credenziali OAuth Google Cloud (progetto: `becreatives-mcp`)
- `uv` installato (`brew install uv`) — solo per Claude Code con server locale

## Installazione

### Claude Code

```bash
# Da plugin directory locale
claude --plugin-dir ./preventivo-plugin

# Oppure installa permanentemente
claude plugin install ./preventivo-plugin --scope project
```

### Claude Cowork

Installa dalla tab Plugins in Claude Desktop. Richiede MCP server remoto (vedi [CONNECTORS.md](CONNECTORS.md)).

## Configurazione

### Variabili d'Ambiente

Aggiungi al tuo shell profile:

```bash
export GOOGLE_OAUTH_CLIENT_ID="your-client-id.apps.googleusercontent.com"
export GOOGLE_OAUTH_CLIENT_SECRET="your-client-secret"
```

### Email Google

Il plugin chiede l'email Google Workspace al primo utilizzo in ogni sessione. Usa la tua email `@becreatives.com`.

## Comandi Disponibili

| Comando | Descrizione | Uso |
|---------|-------------|-----|
| `/preventivo:preventivo` | Flusso completo (stima + pausa + scrittura) | Entry point principale |
| `/preventivo:stima` | Solo analisi e stima progetto | Standalone per stime senza documento |
| `/preventivo:scrivi` | Solo scrittura documento Google Docs | Se hai gia le informazioni pronte |

## Template Disponibili

11 template su Google Drive (7 sviluppo + 4 manutenzione). Vedi `skills/scrivi/references/templates.md` per la lista completa.

## Architettura

```
preventivo-plugin/
├── .claude-plugin/plugin.json          # Manifest
├── commands/preventivo.md              # Orchestratore
├── skills/
│   ├── stima/                          # Fase 1: analisi progetto
│   │   ├── SKILL.md
│   │   └── references/
│   └── scrivi/                         # Fase 2: scrittura documento
│       ├── SKILL.md
│       └── references/
├── .mcp.json                           # Config MCP Google Workspace
└── google_workspace_mcp/               # Server MCP (symlink)
```

## Contratto Dati

La Fase 1 produce una **SCHEDA PROGETTO** delimitata da `=== SCHEDA PROGETTO ===` / `=== FINE SCHEDA PROGETTO ===`. Contiene: info generali, moduli con scoring, rischi critici, domande aperte, raccomandazioni, template consigliato.

La Fase 2 consuma la SCHEDA PROGETTO dalla conversazione per popolare il documento.
