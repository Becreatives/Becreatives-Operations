---
name: scrivi
description: Genera il documento preventivo su Google Docs partendo da template Becreatives e informazioni del progetto. Usare dopo la stima o con informazioni pronte.
disable-model-invocation: true
argument-hint: "[informazioni progetto]"
---

# Scrivi Preventivo

Questa skill genera il documento preventivo su Google Docs partendo da un template e dalle informazioni del progetto.

Puo essere usata:
- **Dopo la skill stima** (`/preventivo:stima`): riceve la SCHEDA PROGETTO gia prodotta nella conversazione
- **Standalone**: raccoglie tutte le informazioni necessarie dall'utente

**Lingua: Italiano.** Comunica sempre in italiano.

## Configurazione Utente

Se non e stata ancora specificata in questa conversazione, chiedi all'utente la sua email Google Workspace (es: `nome.cognome@becreatives.com`).
Usa questa email come parametro `user_google_email` in **tutte** le chiamate MCP successive.

## Procedura

### Step 1: Selezione Template

**Se nella conversazione esiste una SCHEDA PROGETTO** (delimitata da `=== SCHEDA PROGETTO ===`):
- Proponi il template suggerito nella scheda con la motivazione indicata
- Chiedi conferma: "La Scheda Progetto suggerisce il template **[nome]** perche [motivazione]. Confermi o preferisci un altro?"

**Altrimenti**, chiedi all'utente quale template utilizzare.
Per la lista completa dei template disponibili con i relativi ID, leggi [templates.md](references/templates.md).

### Step 2: Raccogli Informazioni Mancanti

Identifica quali informazioni servono e quali sono GIA disponibili nella SCHEDA PROGETTO (se presente).

**Chiedi SOLO le informazioni mancanti.** Ecco cosa serve:

**Informazioni Cliente** (mai presenti nella Scheda, chiedi sempre):
- Nome azienda
- Indirizzo
- P.IVA
- Nome referente/i

**Informazioni Progetto** (spesso gia nella Scheda):
- Oggetto del preventivo
- Descrizione del progetto
- Funzionalita richieste
- Tempi di lavorazione stimati
- Data di partenza prevista

**Informazioni Economiche** (mai presenti nella Scheda, chiedi sempre):
- Costo totale
- Condizioni di pagamento

Se l'utente fornisce un link a Google Slides con lo scope del progetto, leggi il contenuto con `mcp__google-workspace__get_presentation` usando l'email raccolta dall'utente.

### Step 3: Crea il Documento

#### 3.1 Copia il template

Usa `mcp__google-workspace__copy_drive_file`:
- `file_id`: l'ID del template selezionato (da [templates.md](references/templates.md))
- `new_name`: "Preventivo [Tipo] - [Nome Azienda]"
- `user_google_email`: l'email raccolta dall'utente

Salva l'ID del documento copiato.

#### 3.2 Leggi il template copiato

Usa `mcp__google-workspace__get_doc_content` per leggere il documento appena copiato:
- `document_id`: l'ID del documento copiato
- `user_google_email`: l'email raccolta dall'utente

Analizza il contenuto per identificare i placeholder presenti (es: `Nome Azienda`, `Nome Referente`, `gg/mm/aaaa`, etc.). NON assumere quali placeholder esistano: scoprili leggendo il documento.

#### 3.3 Sostituisci i placeholder

Usa `mcp__google-workspace__find_and_replace_doc` per ogni placeholder identificato:
- `document_id`: l'ID del documento copiato
- `user_google_email`: l'email raccolta dall'utente
- Sostituisci con i valori raccolti dall'utente

Placeholder tipici (ma verifica sempre leggendo il template):
- `Nome Azienda` → Nome azienda cliente
- `Nome Referente` → Nome/i referente/i
- `gg/mm/aaaa` → Date appropriate
- Altri placeholder specifici del template

#### 3.4 Adatta il contenuto tecnico

Utilizza la struttura in moduli e sottomoduli (indicati con bulletpoints) per definire in modo chiaro nel preventivo le funzionalita e caratteristiche che saranno incluse nel progetto. Siccome il documento sara client-facing, non esternare punteggi di rischio e tempistiche necessarie per ciascun modulo. L'obiettivo e quello di dare una panoramica chiara al cliente di quello che e effettivamente incluso nel preventivo.

**Importante sul linguaggio:** Il preventivo e un documento cliente-facing. Trasforma il linguaggio tecnico della Scheda in linguaggio comprensibile per il cliente. Evita gergo tecnico dove possibile.

### Step 4: Verifica e Consegna

1. **Leggi il documento generato** con `mcp__google-workspace__get_doc_content` per verificare:
   - Non ci sono placeholder residui del template
   - Il contenuto e coerente e completo
   - La formattazione e corretta

2. **Correggi** eventuali problemi trovati

3. **Consegna** all'utente:
   - Link al documento generato
   - Riepilogo di cosa e stato compilato
   - Lista delle sezioni che richiedono revisione manuale (es: dettagli economici specifici, timeline esatte, condizioni particolari)

## Note Importanti

- Usa SEMPRE l'email Google Workspace dell'utente (raccolta nella sezione Configurazione Utente) per le chiamate MCP
- Preferisci sostituzioni piccole e mirate invece di blocchi di testo grandi
- Verifica sempre il documento finale per residui del template originale
- NON includere mai score di complessita/rischio nel documento cliente
- I template sono nella cartella: https://drive.google.com/drive/folders/1R5zRxYN40K7CEAq8M9cF2384VW_0S5pr

$ARGUMENTS
