# Preventivo Generator

Questa skill orchestra la generazione di un preventivo in 2 fasi, con una pausa obbligatoria per la revisione tra le fasi.

**Lingua: Italiano.** Comunica sempre in italiano.

## Benvenuto

Saluta l'utente e presenta il processo:

"Ciao! Ti guido nella creazione del preventivo. Il processo ha 2 fasi:

1. **Stima** — Analizziamo il progetto, identifichiamo i moduli, valutiamo complessita e rischi, e produciamo una Scheda Progetto strutturata
2. **Scrittura** — Usando la Scheda Progetto, generiamo il documento preventivo su Google Docs partendo da un template

Tra le due fasi ci fermeremo per farti rivedere e approvare la Scheda Progetto.

Come vuoi procedere?"

Presenta le opzioni:
1. **"Ho un brief"** — "Condividi il link Google Slides/Docs o incolla il testo del brief, e parto con l'analisi"
2. **"Definiamo insieme"** — "Ti guido io con domande mirate per definire il progetto"
3. **"Ho gia la stima"** — "Se hai gia una Scheda Progetto o le informazioni pronte, saltiamo direttamente alla scrittura del documento"

---

## Flusso

### Se l'utente sceglie opzione 1 o 2: Flusso completo

#### FASE 1: Stima

Leggi e segui le istruzioni contenute nel file `.claude/commands/preventivo-stima.md`.

Esegui tutte le 5 fasi della stima (Intake, Identificazione Moduli, Check Allineamento, Deep Dive, Output SCHEDA PROGETTO).

#### PAUSA OBBLIGATORIA

Dopo che la SCHEDA PROGETTO e stata prodotta, FERMATI e di:

"La Scheda Progetto e pronta. Prima di procedere con la scrittura del preventivo, prenditi il tempo per rivederla:

- I moduli sono corretti e completi?
- Gli score di complessita/rischio riflettono la tua visione?
- Le raccomandazioni hanno senso?
- Il template suggerito e quello giusto?

Modifica quello che serve. Quando sei soddisfatto, dimmi **procedi con il preventivo** e passo alla Fase 2."

**NON procedere alla Fase 2 senza conferma esplicita dell'utente** (es: "procedi", "ok vai", "procedi con il preventivo", o simili).

#### FASE 2: Scrittura

Leggi e segui le istruzioni contenute nel file `.claude/commands/preventivo-scrivi.md`.

La skill di scrittura usera automaticamente la SCHEDA PROGETTO presente nella conversazione per:
- Proporre il template suggerito
- Evitare di chiedere informazioni gia disponibili
- Popolare le sezioni tecniche del preventivo

---

### Se l'utente sceglie opzione 3: Solo scrittura

Leggi e segui direttamente le istruzioni contenute nel file `.claude/commands/preventivo-scrivi.md`.

La skill chiedera tutte le informazioni necessarie dato che non c'e una Scheda Progetto.

---

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
