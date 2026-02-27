---
description: Flusso completo preventivo Becreatives - stima progetto + pausa revisione + scrittura documento Google Docs
argument-hint: "[brief o link al brief]"
---

# Preventivo Generator

Questa skill orchestra la generazione di un preventivo in 2 fasi, con una pausa obbligatoria per la revisione tra le fasi.

**Lingua: Italiano.** Comunica sempre in italiano.

## Benvenuto

Saluta l'utente e presenta il processo:

"Ciao! Ti guido nella creazione del preventivo. Il processo ha 2 fasi:

1. **Stima** — Analizziamo il progetto, identifichiamo i moduli, valutiamo complessita e rischi, e produciamo una Scheda Progetto strutturata
2. **Scrittura** — Usando la Scheda Progetto, generiamo il documento preventivo su Google Docs partendo da un template

Tra le due fasi ci fermeremo per farti rivedere e approvare la Scheda Progetto.

## Flusso

#### FASE 1: Stima

Invoca la skill **stima** (`/preventivo:stima`), per raccogliere tutte le informazioni necessarie. Essa invocherà la skill **scrivi** (`/preventivo:scrivi`) che poi guiderà l'utente fino al completamento della procedura. 

## Completamento

Dopo che il documento e stato generato, presenta:

1. **Link al documento** generato su Google Docs
2. **Riepilogo** di cosa e stato inserito nel preventivo
3. **Sezioni da revisionare manualmente**, tipicamente:
   - Dettagli economici specifici (margini, sconti)
   - Timeline esatte
   - Condizioni contrattuali particolari
   - Formattazione finale

"Il preventivo e pronto! Rivedi le sezioni indicate e poi puoi condividerlo con il cliente."

$ARGUMENTS
