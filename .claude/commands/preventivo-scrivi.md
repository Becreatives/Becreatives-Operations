# Scrivi Preventivo

Questa skill genera il documento preventivo su Google Docs partendo da un template e dalle informazioni del progetto.

Puo essere usata:
- **Dopo `/preventivo-stima`**: riceve la SCHEDA PROGETTO gia prodotta nella conversazione
- **Standalone**: raccoglie tutte le informazioni necessarie dall'utente

**Lingua: Italiano.** Comunica sempre in italiano.

## Procedura

### Step 1: Selezione Template

**Se nella conversazione esiste una SCHEDA PROGETTO** (delimitata da `=== SCHEDA PROGETTO ===`):
- Proponi il template suggerito nella scheda con la motivazione indicata
- Chiedi conferma: "La Scheda Progetto suggerisce il template **[nome]** perche [motivazione]. Confermi o preferisci un altro?"

**Altrimenti**, chiedi all'utente quale template utilizzare:

**Template Preventivi Sviluppo:**
1. Applicazione (ID: `1fjIBwrTmb5O3t2z9IREEDXvt19ED0NP9z5PjACbwixU`)
2. Applicazione AR (ID: `1gsoXI6EVf9kftKeRvN868ntu9lDhxxXNa2GKr-gX93s`)
3. E-commerce Shopify custom (ID: `1Azp9BrVb6rPCDWFaPLh6RJsRQdPLpYDR5sKY9-IRweY`)
4. E-commerce Shopify headless (ID: `19LkAm-hwHQ48T7keoVw95DME3RQeyn5Yz3mmImxaz5c`)
5. E-commerce WooCommerce (ID: `1t5XiVZePXBTmq6g-9vcXf4RmZbMqWwUkr2YJabEalog`)
6. Piattaforma web Custom (ID: `1XJ7xSYPDAZF2awSyF3GF9OD8fhqh9LwXveCEu4V5nMg`)
7. Piattaforma web Wordpress (ID: `12yd6_rOh47cFdXSgh1w7Lnfrj6m7p7jMixn1eQIO4Wo`)

**Template Preventivi Manutenzione:**
8. Manutenzione evolutiva (ID: `1XzFErDBzkdqNBIrildYq2PCeFWA7WMCUolwFipdRKeU`)
9. Manutenzione ordinaria a giornate (ID: `1EWwvj3Oio4e2rgxxWoqaaRLuH3OrwuO9OsshdnZvmlI`)
10. Manutenzione ordinaria pacchetto a consumo (ID: `1bWQr2cpQfH8EIMoUNxpV31YDS13Vye-Vn7Cone1kj7U`)
11. Manutenzione ordinaria pacchetto mensile (ID: `1cXbpjjyFelt65NaLkSp7qqpfYWBcQ_bw-tirW3Wj3hg`)

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

Se l'utente fornisce un link a Google Slides con lo scope del progetto, leggi il contenuto con `mcp__google-workspace__get_presentation` (email: `v.dipalo@becreatives.com`).

### Step 3: Crea il Documento

#### 3.1 Copia il template

Usa `mcp__google-workspace__copy_drive_file`:
- `file_id`: l'ID del template selezionato
- `new_name`: "Preventivo [Tipo] - [Nome Azienda]"
- `user_google_email`: `v.dipalo@becreatives.com`

Salva l'ID del documento copiato.

#### 3.2 Leggi il template copiato

Usa `mcp__google-workspace__get_doc_content` per leggere il documento appena copiato:
- `document_id`: l'ID del documento copiato
- `user_google_email`: `v.dipalo@becreatives.com`

Analizza il contenuto per identificare i placeholder presenti (es: `Nome Azienda`, `Nome Referente`, `gg/mm/aaaa`, etc.). NON assumere quali placeholder esistano: scoprili leggendo il documento.

#### 3.3 Sostituisci i placeholder

Usa `mcp__google-workspace__find_and_replace_doc` per ogni placeholder identificato:
- `document_id`: l'ID del documento copiato
- `user_google_email`: `v.dipalo@becreatives.com`
- Sostituisci con i valori raccolti dall'utente

Placeholder tipici (ma verifica sempre leggendo il template):
- `Nome Azienda` → Nome azienda cliente
- `Nome Referente` → Nome/i referente/i
- `gg/mm/aaaa` → Date appropriate
- Altri placeholder specifici del template

#### 3.4 Adatta il contenuto tecnico

Se esiste una SCHEDA PROGETTO, usa i suoi dati per popolare le sezioni tecniche del preventivo. Mappatura:

| Scheda Progetto | Preventivo |
|---|---|
| Moduli + Descrizione | Sezioni funzionali del preventivo |
| Sotto-moduli | Dettaglio feature per sezione |
| Rischi Critici | Sezione assunzioni/esclusioni |
| Raccomandazioni | Approccio progettuale |
| Score complessita/rischio | **NON includere** (sono interni) |

Per manipolazioni complesse del contenuto, usa in ordine:
1. `mcp__google-workspace__inspect_doc_structure` per capire la struttura del documento
2. `mcp__google-workspace__find_and_replace_doc` per sostituzioni semplici
3. `mcp__google-workspace__batch_update_doc` per modifiche strutturali
4. `mcp__google-workspace__insert_doc_elements` per inserire nuovi contenuti

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

- Usa SEMPRE l'email `v.dipalo@becreatives.com` per le chiamate MCP
- Preferisci sostituzioni piccole e mirate invece di blocchi di testo grandi
- Verifica sempre il documento finale per residui del template originale
- NON includere mai score di complessita/rischio nel documento cliente
- I template sono nella cartella: https://drive.google.com/drive/folders/1R5zRxYN40K7CEAq8M9cF2384VW_0S5pr

$ARGUMENTS
