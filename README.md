# Fattura Estera → XML FatturaPA

Skill per [Claude](https://claude.ai) che converte fatture ricevute da fornitori esteri (UE ed extra-UE) in file XML conformi allo schema FatturaPA v1.2.2, pronti per l'importazione nei gestionali contabili italiani.

## Il problema

I gestionali contabili italiani non gestiscono le fatture estere in formato XML. Ogni fattura estera va letta, interpretata e imputata manualmente. Questa skill elimina la parte di imputazione manuale dei dati.

## Cosa fa

- Prende in input una fattura estera: PDF, immagine o testo, in qualsiasi lingua
- Estrae i dati rilevanti (fornitore, importi, aliquote, descrizioni)
- Genera un file XML conforme allo schema FatturaPA v1.2.2
- Si ferma e chiede conferma su tre controlli obbligatori prima di generare l'output

## Cosa NON fa

- **Non genera autofatture.** La predisposizione e l'invio delle autofatture restano responsabilità del professionista.
- **Non classifica il tipo di integrazione.** L'XML usa sempre TipoDocumento TD01. La classificazione TD17, TD18, TD19 è responsabilità del gestionale in fase di registrazione.
- **Non sostituisce i controlli del professionista.** Causale corretta, compatibilità del gestionale e verifica dei dati restano in capo all'operatore.

Questa è una soluzione di **workaround**: semplifica e ottimizza i tempi di imputazione manuale, ma richiede sempre l'intervento e la supervisione del professionista.

## Controlli integrati (stop-and-ask)

La skill si ferma automaticamente e chiede conferma su:

1. **TipoDocumento = TD01, sempre.** Non usa mai TD17, TD18, TD19 o altri codici.
2. **P.IVA del cessionario/committente italiano.** Se non è presente nel documento, la chiede all'utente. Non procede con campi vuoti.
3. **Dati del fornitore estero.** Verifica che i dati del cedente/prestatore siano completi e corretti.

## Come installarla

Serve un piano Claude a pagamento (Pro, Max, Team o Enterprise) con la funzione "Esecuzione codice e creazione file" attiva.

1. Scarica la cartella `fattura-estera-xml` da questa repository
2. Su Claude, vai in **Impostazioni → Personalizza → Competenze**
3. Clicca **"+"** e carica la cartella
4. La skill si attiva automaticamente quando carichi una fattura estera

## Struttura della repository

```
fattura-estera-xml/
├── SKILL.md              # Istruzioni principali della skill
└── references/
    └── xml-template.md   # Template XML completo e regole campo per campo
```

## Compatibilità

La skill è pensata per Claude come skill nativa. L'approccio e le istruzioni sono replicabili, previa sperimentazione, anche con altri sistemi di intelligenza artificiale.

## Gestionali testati

La skill genera XML conformi allo schema FatturaPA v1.2.2. L'importazione è stata testata con:

- Wolters Kluwer (Genya)

Se avete testato l'importazione con altri gestionali (TeamSystem, Zucchetti, Profis, etc.), aprite una issue o una pull request per aggiornare questa lista.

## Feedback e contributi

Se la usate, scrivetemi. Feedback reali su cosa funziona e cosa no sono benvenuti.

- **Issue**: per segnalare problemi o incompatibilità con gestionali specifici
- **Pull request**: per miglioramenti alle istruzioni o al template XML

## Autore

**Luca Piovano**
Dottore Commercialista e Revisore Legale
Partner — Studio Associato CMFC (Torino / Milano)

- LinkedIn: [linkedin.com/in/luca-piovano-9266b49](https://www.linkedin.com/in/luca-piovano-9266b49)
- Libro: [Commercialista oggi — Guida strategica per governare AI, persone e processi nello studio moderno](https://www.amazon.it/dp/B0GGXGT2BP)

## Licenza

Questo progetto è rilasciato con licenza [MIT](LICENSE). Libero di usare, modificare e distribuire.
