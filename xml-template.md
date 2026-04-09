# Template XML FatturaPA per Fatture Estere

## Template completo

Questo è il template XML da utilizzare per generare fatture estere in formato FatturaPA v1.2.2.
I commenti `<!-- -->` indicano le istruzioni per la compilazione di ciascun campo.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<p:FatturaElettronica versione="FPR12"
  xmlns:ds="http://www.w3.org/2000/09/xmldsig#"
  xmlns:p="http://ivaservizi.agenziaentrate.gov.it/docs/xsd/fatture/v1.2"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://ivaservizi.agenziaentrate.gov.it/docs/xsd/fatture/v1.2
    http://www.fatturapa.gov.it/export/fatturazione/sdi/fatturapa/v1.2.2/Schema_del_file_xml_FatturaPA_v1.2.2.xsd">

  <FatturaElettronicaHeader>

    <DatiTrasmissione>
      <IdTrasmittente>
        <!-- IdPaese: sempre "IT" (è il soggetto italiano che trasmette) -->
        <IdPaese>IT</IdPaese>
        <!-- IdCodice: P.IVA o CF del cessionario/committente italiano -->
        <IdCodice>{PIVA_CLIENTE_IT}</IdCodice>
      </IdTrasmittente>
      <!-- ProgressivoInvio: numerazione progressiva libera, max 10 caratteri -->
      <ProgressivoInvio>00001</ProgressivoInvio>
      <!-- FormatoTrasmissione: sempre FPR12 per fatture tra privati -->
      <FormatoTrasmissione>FPR12</FormatoTrasmissione>
      <!-- CodiceDestinatario: 0000000 per documenti non transitanti da SDI -->
      <CodiceDestinatario>0000000</CodiceDestinatario>
    </DatiTrasmissione>

    <!-- ============================================ -->
    <!-- CEDENTE/PRESTATORE (FORNITORE ESTERO)        -->
    <!-- ============================================ -->
    <CedentePrestatore>
      <DatiAnagrafici>
        <IdFiscaleIVA>
          <!-- IdPaese: codice ISO 3166-1 alpha-2 del paese del fornitore -->
          <!-- Esempi: DE, FR, US, GB, HU, CH, CN, JP -->
          <IdPaese>{CODICE_PAESE_FORNITORE}</IdPaese>
          <!-- IdCodice: numero di partita IVA SENZA prefisso paese -->
          <!-- Se il fornitore extra-UE non ha VAT: IdPaese=OO (due O maiuscole), IdCodice=99999999999 (undici 9) -->
          <IdCodice>{VAT_NUMBER_FORNITORE}</IdCodice>
        </IdFiscaleIVA>
        <Anagrafica>
          <!-- Per società: usare Denominazione -->
          <Denominazione>{RAGIONE_SOCIALE_FORNITORE}</Denominazione>
          <!-- Per persone fisiche: sostituire Denominazione con Nome + Cognome -->
          <!-- <Nome>{NOME}</Nome> -->
          <!-- <Cognome>{COGNOME}</Cognome> -->
        </Anagrafica>
        <!-- RegimeFiscale: RF18 (Altro) per tutti i soggetti non residenti -->
        <RegimeFiscale>RF18</RegimeFiscale>
      </DatiAnagrafici>
      <Sede>
        <!-- Indirizzo fornitore: TRONCARE a MAX 20 caratteri. Genya ha un limite bloccante non modificabile. Usare i primi 20 caratteri dell'indirizzo originale. -->
        <Indirizzo>{INDIRIZZO_FORNITORE}</Indirizzo>
        <!-- CAP: "00000" per soggetti esteri (obbligatorio, 5 caratteri) -->
        <CAP>00000</CAP>
        <Comune>{CITTA_FORNITORE}</Comune>
        <!-- Provincia: opzionale per esteri, max 2 caratteri. -->
        <!-- Usare solo se disponibile sigla stato/provincia breve (es. CA per California) -->
        <!-- Omettere se non applicabile -->
        <!-- <Provincia>{SIGLA_PROVINCIA}</Provincia> -->
        <!-- Nazione: codice ISO 3166-1 alpha-2 -->
        <Nazione>{CODICE_PAESE_FORNITORE}</Nazione>
      </Sede>
      <!-- Contatti: opzionali ma utili -->
      <Contatti>
        <Email>{EMAIL_FORNITORE}</Email>
        <!-- <Telefono>{TELEFONO_FORNITORE}</Telefono> -->
      </Contatti>
    </CedentePrestatore>

    <!-- ============================================ -->
    <!-- CESSIONARIO/COMMITTENTE (CLIENTE ITALIANO)   -->
    <!-- ============================================ -->
    <CessionarioCommittente>
      <DatiAnagrafici>
        <!-- Se il cliente ha partita IVA italiana -->
        <IdFiscaleIVA>
          <IdPaese>IT</IdPaese>
          <IdCodice>{PIVA_CLIENTE}</IdCodice>
        </IdFiscaleIVA>
        <!-- CodiceFiscale: obbligatorio per persone fisiche, facoltativo per società -->
        <!-- <CodiceFiscale>{CF_CLIENTE}</CodiceFiscale> -->
        <Anagrafica>
          <!-- Per società -->
          <Denominazione>{RAGIONE_SOCIALE_CLIENTE}</Denominazione>
          <!-- Per persone fisiche: sostituire con Nome + Cognome -->
          <!-- <Nome>{NOME_CLIENTE}</Nome> -->
          <!-- <Cognome>{COGNOME_CLIENTE}</Cognome> -->
        </Anagrafica>
      </DatiAnagrafici>
      <Sede>
        <Indirizzo>{INDIRIZZO_CLIENTE}</Indirizzo>
        <CAP>{CAP_CLIENTE}</CAP>
        <Comune>{CITTA_CLIENTE}</Comune>
        <Provincia>{SIGLA_PROVINCIA_CLIENTE}</Provincia>
        <Nazione>IT</Nazione>
      </Sede>
    </CessionarioCommittente>

  </FatturaElettronicaHeader>

  <FatturaElettronicaBody>

    <!-- ============================================ -->
    <!-- DATI GENERALI DOCUMENTO                      -->
    <!-- ============================================ -->
    <DatiGenerali>
      <DatiGeneraliDocumento>
        <!-- TipoDocumento: SEMPRE e SOLO TD01. Non usare altri codici. -->
        <TipoDocumento>TD01</TipoDocumento>
        <!-- Divisa: sempre EUR nell'XML (convertire da valuta estera se necessario, chiedere il tasso di cambio all'utente) -->
        <Divisa>{CODICE_DIVISA}</Divisa>
        <!-- Data: data emissione fattura, formato YYYY-MM-DD -->
        <Data>{DATA_EMISSIONE}</Data>
        <!-- Numero: numero fattura originale del fornitore, max 20 caratteri -->
        <Numero>{NUMERO_FATTURA}</Numero>
        <!-- ImportoTotaleDocumento: totale fattura con 2 decimali -->
        <ImportoTotaleDocumento>{TOTALE}</ImportoTotaleDocumento>
        <!-- Causale: descrizione libera dell'operazione, max 200 caratteri -->
        <!-- Includere sempre il riferimento normativo e la natura dell'operazione -->
        <Causale>{CAUSALE_DESCRITTIVA}</Causale>
      </DatiGeneraliDocumento>
      <!-- DatiOrdineAcquisto: opzionale, se la fattura riporta un numero d'ordine -->
      <!-- <DatiOrdineAcquisto>
        <IdDocumento>{NUMERO_ORDINE}</IdDocumento>
      </DatiOrdineAcquisto> -->
    </DatiGenerali>

    <!-- ============================================ -->
    <!-- DETTAGLIO BENI/SERVIZI                       -->
    <!-- ============================================ -->
    <DatiBeniServizi>

      <!-- Ripetere DettaglioLinee per ogni riga della fattura -->
      <DettaglioLinee>
        <NumeroLinea>{N}</NumeroLinea>
        <!-- CodiceArticolo: opzionale, per codici doganali/tariffari -->
        <!-- <CodiceArticolo>
          <CodiceTipo>TARIC</CodiceTipo>
          <CodiceValore>{CODICE_TARIC}</CodiceValore>
        </CodiceArticolo> -->
        <Descrizione>{DESCRIZIONE_RIGA}</Descrizione>
        <!-- Quantita: con 2 decimali minimo -->
        <Quantita>{QTA}</Quantita>
        <!-- UnitaMisura: opzionale (PZ, KG, LT, NR, PCS, etc.) -->
        <!-- <UnitaMisura>{UM}</UnitaMisura> -->
        <!-- PrezzoUnitario: massimo 8 decimali -->
        <PrezzoUnitario>{PREZZO_UNITARIO}</PrezzoUnitario>
        <!-- PrezzoTotale: con 2 decimali (Quantita x PrezzoUnitario, arrotondato) -->
        <PrezzoTotale>{PREZZO_TOTALE}</PrezzoTotale>
        <!-- AliquotaIVA: 0.00 per fatture estere ricevute -->
        <AliquotaIVA>0.00</AliquotaIVA>
        <!-- Natura: obbligatoria quando AliquotaIVA = 0 -->
        <!-- N2.1 per servizi (UE e extra-UE) e beni extra-UE -->
        <!-- N3.3 per beni intracomunitari UE -->
        <Natura>{CODICE_NATURA}</Natura>
      </DettaglioLinee>

      <!-- Un DatiRiepilogo per ogni combinazione AliquotaIVA + Natura -->
      <DatiRiepilogo>
        <AliquotaIVA>0.00</AliquotaIVA>
        <Natura>{CODICE_NATURA}</Natura>
        <!-- ImponibileImporto: somma dei PrezzoTotale delle righe con stessa Natura -->
        <ImponibileImporto>{IMPONIBILE}</ImponibileImporto>
        <!-- Imposta: sempre 0.00 per fattura estera ricevuta -->
        <Imposta>0.00</Imposta>
        <!-- RiferimentoNormativo: testo libero con riferimento di legge -->
        <RiferimentoNormativo>{RIFERIMENTO_NORMATIVO}</RiferimentoNormativo>
      </DatiRiepilogo>
    </DatiBeniServizi>

    <!-- ============================================ -->
    <!-- DATI PAGAMENTO                               -->
    <!-- ============================================ -->
    <DatiPagamento>
      <!-- TP01: pagamento a rate, TP02: pagamento completo -->
      <CondizioniPagamento>TP02</CondizioniPagamento>
      <DettaglioPagamento>
        <!-- ModalitaPagamento: MP05 bonifico, MP08 carta, etc. -->
        <ModalitaPagamento>{CODICE_PAGAMENTO}</ModalitaPagamento>
        <DataScadenzaPagamento>{DATA_SCADENZA}</DataScadenzaPagamento>
        <ImportoPagamento>{IMPORTO_PAGAMENTO}</ImportoPagamento>
        <!-- IBAN e BIC: solo per bonifici (MP05) -->
        <!-- <IBAN>{IBAN_FORNITORE}</IBAN> -->
        <!-- <BIC>{BIC_FORNITORE}</BIC> -->
      </DettaglioPagamento>
    </DatiPagamento>

  </FatturaElettronicaBody>

</p:FatturaElettronica>
```

## Regole di compilazione per campo

### DatiTrasmissione.IdTrasmittente
- **IdPaese**: sempre `IT`
- **IdCodice**: partita IVA del soggetto italiano (cessionario/committente)

### CedentePrestatore.IdFiscaleIVA
- **IdPaese**: codice paese ISO del fornitore (DE, FR, US, GB, HU, etc.)
- **IdCodice**: numero VAT senza prefisso paese
  - Se il fornitore ha VAT EU: usare il numero (es. da "DE123456789" → IdPaese=DE, IdCodice=123456789)
  - Se il fornitore extra-UE non ha alcun identificativo fiscale: usare `IdPaese = OO` (due lettere O maiuscole) e `IdCodice = 99999999999` (undici 9)
  - Se la fattura riporta un tax ID non-VAT (es. EIN americano): usarlo come IdCodice

### CedentePrestatore.Sede
- **CAP**: sempre `00000` per soggetti esteri (requisito schema XSD: esattamente 5 cifre)
- **Provincia**: opzionale. Se disponibile e ≤ 2 caratteri, includerla. Altrimenti omettere.
- **Nazione**: stesso codice ISO di IdPaese

### RegimeFiscale
- Sempre `RF18` (Altro) per soggetti non residenti, non stabiliti

### Natura IVA
- **N2.1** (non soggetto): per servizi art. 7-ter e beni art. 17 c.2
- **N3.3** (non imponibile): per acquisti intracomunitari di beni art. 38 DL 331/93

### Riferimenti normativi per la Causale

| Tipo operazione | Causale suggerita |
|-----------------|-------------------|
| Servizi UE | Fattura estera - servizi - reverse charge art. 7-ter DPR 633/72 |
| Servizi extra-UE | Fattura estera - servizi digitali - reverse charge art. 7-ter DPR 633/72 |
| Beni intra-UE | Acquisto intracomunitario beni - art. 38 DL 331/93 - Reverse charge ex art. 46 DL 331/93 |
| Beni extra-UE (import) | Acquisto beni extra-UE - art. 17 c.2 DPR 633/72 |

### Naming convention file output

Formato: `{IdPaese}_{ABBREVIAZIONE_FORNITORE}_{NumeroFattura}.xml`

Regole:
- IdPaese: codice ISO 2 lettere maiuscole
- Abbreviazione fornitore: max 10 caratteri, maiuscole, senza spazi né caratteri speciali
- Numero fattura: come riportato in fattura, sostituendo `/` con `-`

Esempi:
- `US_ANT_5NF8S47B-0004.xml` (Anthropic, PBC - USA)
- `HU_ABCTECH_RKK-2-2026.xml` (ABC Tech Kft. - Ungheria)
- `DE_MUSTER_INV-2026-001.xml` (Muster GmbH - Germania)
- `GB_XYZLTD_12345.xml` (XYZ Ltd - Regno Unito)

### Deduzione modalità pagamento

| Indizio nella fattura | ModalitaPagamento |
|-----------------------|-------------------|
| IBAN, bank transfer, wire, bonifico | MP05 |
| Credit card, carta, card payment | MP08 |
| PayPal, online payment | MP08 |
| Check, cheque, assegno | MP02 |
| Cash, contanti | MP01 |
| SEPA Direct Debit | MP19 |
| Non specificato | MP05 (default) |
