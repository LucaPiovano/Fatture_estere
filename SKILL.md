---
name: fattura-estera-xml
description: >
  Converte fatture estere (PDF, immagini, testo) in file XML conformi allo schema FatturaPA v1.2.2,
  pronti per l'importazione nei gestionali contabili italiani (es. Genya Wolters Kluwer, TeamSystem, Zucchetti).
  Usare SEMPRE questa skill quando l'utente carica una fattura estera (UE o extra-UE), chiede di convertire
  una fattura in XML, o menziona: fattura estera, fattura intracomunitaria, fattura extra-UE, autofattura,
  integrazione, reverse charge, TD17, TD18, TD19, FatturaPA, importare fattura in Genya, registrare fattura
  estera, XML da fattura, convertire invoice. Attivare anche quando l'utente carica un PDF/immagine con
  intestazione in lingua straniera (Invoice, Rechnung, Facture, Factura, Számla) o con fornitore estero.
---

# Fattura Estera → XML FatturaPA

Converte fatture ricevute da fornitori esteri (UE ed extra-UE) in XML FatturaPA v1.2.2 per importazione nei gestionali contabili. Per il template XML completo e le regole campo per campo, consultare `references/xml-template.md`.

## Tre controlli obbligatori (stop-and-ask)

Prima di generare l'XML, verificare sempre questi tre punti. Se uno fallisce, fermarsi e chiedere all'utente.

### 1. TipoDocumento = TD01, sempre

Il campo `TipoDocumento` nell'XML generato è **sempre e solo TD01**. Non usare mai TD17, TD18, TD19 o altri codici. La classificazione del tipo di integrazione/autofattura è esclusiva responsabilità del gestionale in fase di registrazione.

### 2. P.IVA del cessionario/committente italiano

La P.IVA italiana è obbligatoria nel `CessionarioCommittente`. Se non è disponibile nel documento né nel contesto:

> "Non ho trovato la partita IVA del soggetto italiano (cessionario/committente).
> 1. **Inseriscila manualmente** – dimmi la P.IVA e proseguo
> 2. **Scarta la fattura** – non genero l'XML"

Non procedere con campi vuoti o placeholder.

### 3. Fatture in valuta non-EUR

Se la fattura è in valuta diversa dall'EUR, fermarsi e chiedere il tasso di cambio:

> "La fattura è espressa in {VALUTA} (importo: {IMPORTO} {VALUTA}).
> A quale tasso di cambio devo convertire in EUR?
> Dimmi il cambio {VALUTA}/EUR da applicare (es. 1 USD = 0,9234 EUR)."

Una volta ricevuto il tasso: convertire tutti gli importi in EUR, impostare `Divisa = EUR`, e indicare in `Causale` la valuta originale, l'importo e il tasso applicato.

## Workflow

### Step 1 – Analisi documento

Leggere il documento ed estrarre: dati fornitore, dati cliente italiano, numero/data fattura, righe dettaglio, importi, coordinate bancarie. Verificare i tre controlli obbligatori sopra.

### Step 2 – Classificazione fiscale

Identificare il tipo di operazione per determinare la Natura IVA:

| Scenario | Natura | Riferimento normativo |
|----------|--------|-----------------------|
| Servizi da UE | N2.1 | Art. 7-ter DPR 633/72 |
| Servizi da extra-UE | N2.1 | Art. 7-ter DPR 633/72 |
| Beni da UE (intracomunitario) | N3.3 | Art. 38 DL 331/93 |
| Beni da extra-UE (importazione) | N2.1 | Art. 17 c.2 DPR 633/72 |

Paesi UE: AT, BE, BG, HR, CY, CZ, DK, EE, FI, FR, DE, GR, HU, IE, LV, LT, LU, MT, NL, PL, PT, RO, SK, SI, ES, SE. Il Regno Unito (GB) è extra-UE dal 1° gennaio 2021.

### Step 3 – Generazione XML

Consultare `references/xml-template.md` per il template completo. Regole essenziali:

- **Indirizzo fornitore**: troncare a MAX 20 caratteri (limite bloccante Genya)
- **CAP fornitore estero**: `00000`
- **RegimeFiscale**: `RF18` (Altro) per soggetti non residenti
- **Fornitore extra-UE senza VAT**: `IdPaese = OO`, `IdCodice = 99999999999`
- **Fornitore con VAT**: separare prefisso paese (IdPaese) dal numero (IdCodice)
- **CodiceDestinatario**: `0000000`
- **Divisa**: sempre `EUR` nell'XML
- **AliquotaIVA**: `0.00` con Natura obbligatoria

### Step 4 – Output

Salvare con naming convention: `{IdPaese}_{FORNITORE}_{NumeroFattura}.xml`
- IdPaese: ISO 2 lettere maiuscole
- Fornitore: max 10 caratteri, maiuscole, senza spazi/caratteri speciali
- Numero fattura: come in fattura, sostituire `/` con `-`

Esempi: `US_ANT_5NF8S47B-0004.xml`, `HU_ABCTECH_RKK-2-2026.xml`

Presentare il file con un riepilogo delle scelte chiave (natura IVA, riferimento normativo, modalità pagamento).

## Causali suggerite per tipo operazione

| Tipo operazione | Causale |
|-----------------|---------|
| Servizi UE | Fattura estera - servizi - reverse charge art. 7-ter DPR 633/72 |
| Servizi extra-UE | Fattura estera - servizi digitali - reverse charge art. 7-ter DPR 633/72 |
| Beni intra-UE | Acquisto intracomunitario beni - art. 38 DL 331/93 - Reverse charge ex art. 46 DL 331/93 |
| Beni extra-UE | Acquisto beni extra-UE - art. 17 c.2 DPR 633/72 |

## Deduzione modalità di pagamento

| Indizio in fattura | Codice |
|--------------------|--------|
| IBAN, bank transfer, wire, bonifico | MP05 |
| Credit card, carta, card payment, PayPal | MP08 |
| Check, cheque, assegno | MP02 |
| SEPA Direct Debit | MP19 |
| Non specificato | MP05 (default) |

## Casi particolari

- **Fatture a importo zero**: generare l'XML con ImportoTotaleDocumento = 0.00
- **Righe miste beni + servizi**: separare per Natura IVA, segnalare all'utente che il gestionale potrebbe richiedere integrazioni separate
- **Persona fisica (cessionario)**: usare Nome + Cognome + CodiceFiscale invece di Denominazione
