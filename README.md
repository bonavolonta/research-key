# ResearchKey

**ResearchKey** è un'applicazione web statica e client-side progettata per la generazione, l'assegnazione e il recupero di identificativi pseudonimi specifici per studio nell'ambito della ricerca scientifica.

L'applicazione opera interamente nel browser del partecipante: non richiede database centralizzati, non impone la creazione di account e garantisce una rigorosa separazione delle identità tra studi differenti, prevenendo la trasmissione di codici identificativi ai server di hosting.

---

## Finalità e contesto di ricerca

Nelle indagini scientifiche longitudinali o a rilevazioni ripetute (es. pre/post test, questionari periodici o studi sul campo) è spesso necessario ricondurre le risposte dello stesso partecipante a una medesima chiave univoca, salvaguardando al tempo stesso la riservatezza ed evitando la raccolta diretta di dati personali (come nome, matricola o indirizzo email).

ResearchKey risponde a questa esigenza fornendo:
1. Un identificativo pseudonimo stabile per un determinato studio.
2. Una tessera digitale scaricabile (PNG o PDF) conservabile dal partecipante.
3. Un meccanismo di recupero tramite QR code che permette di sincronizzare il codice su altri dispositivi senza l'intermediazione di un server centrale.

---

## Funzionalità principali

- **Generazione crittografica locale**: codici alfanumerici a 12 caratteri formattati in triplette (`XXXX-XXXX-XXXX`), generati tramite `crypto.getRandomValues()`.
- **Alfabeto ottimizzato a 32 caratteri**: privo di caratteri visivamente ambigui (`I`, `O`, `0`, `1`), con distribuzione perfettamente uniforme (nessun modulo bias) e 60 bit di entropia ($2^{60} \approx 1{,}15 \times 10^{18}$ combinazioni).
- **Isolamento per studio**: memorizzazione su `localStorage` con chiavi isolate per ciascuno studio tramite parametro nell'URL (`?studio=nome-studio`).
- **Privacy-by-Design nel recupero**: il QR code contiene un URL con il codice posizionato nel frammento URI (`#id=...`). Per specifica dello standard HTTP, il frammento non viene mai trasmesso al server web.
- **Pulizia della cronologia di navigazione**: rimozione automatica del frammento dalla barra degli indirizzi (`history.replaceState`) non appena il codice viene acquisito.
- **Protezione dai conflitti**: rilevamento preventivo se nel browser è già presente un codice differente per il medesimo studio, con richiesta di conferma prima di qualsiasi sovrascrittura.
- **Esportazione tessera in PNG**: immagine ad alta risoluzione ($1400 \times 820$ px) pronta per la conservazione visiva o fotografica.
- **Esportazione tessera in PDF con link interattivo**: documento PDF 1.4 generato nativamente in JavaScript (senza dipendenze esterne), con un'annotazione ipertestuale sul QR code che consente il recupero del codice sia tramite scansione sia con un semplice clic.
- **Supporto per la stampa**: foglio di stile ottimizzato per la stampa cartacea diretta (`@media print`).
- **Zero dipendenze runtime di rete**: nessun cookie di profilazione, nessun tracciamento, nessun font remoto o script esterno.

---

## Istruzioni di utilizzo

### Per i ricercatori

1. Distribuire ai partecipanti il link alla pagina web aggiungendo il parametro `studio` per identificare la propria ricerca:
   ```text
   https://bonavolonta.github.io/research-key/?studio=nome-dello-studio
   ```
   *Regole del parametro:* lettere, numeri, trattini e underscore (massimo 48 caratteri, es. `studio-pilota`, `survey-2026`). Se il parametro viene omesso, l'applicazione utilizza il valore predefinito `studio-generale`.
2. Invitare i partecipanti ad aprire il link all'avvio dello studio, a generare il proprio identificativo e a scaricare la tessera digitale (PDF o PNG).
3. Nei successivi questionari o moduli di rilevazione, richiedere unicamente l'inserimento dell'identificativo ResearchKey.

### Per i partecipanti

1. Cliccare su **Genera il mio codice** all'inizio della ricerca.
2. Scaricare la propria tessera in formato **PDF** o **PNG** e conservarla in un luogo sicuro.
3. In caso di cambio browser o dispositivo, recuperare il codice inquadrando il QR code presente sulla tessera, cliccando sul QR all'interno del PDF, oppure reinserendo manualmente il codice nell'apposito campo di ripristino.

---

## Pubblicazione su GitHub Pages

L'applicazione è progettata per essere distribuita come sito statico tramite GitHub Pages:

1. Creare un repository pubblico su GitHub (ad esempio denominato `research-key`).
2. Caricare i file del progetto:
   - `index.html`
   - `.nojekyll` (impedisce l'elaborazione Jekyll e garantisce il corretto instradamento dei file)
   - `vendor/` (contenente `qr-bundle.js` e le relative licenze)
   - La documentazione di progetto (`README.md`, `LICENSE`, `CITATION.cff`, `CHANGELOG.md`)
3. Nel repository GitHub, accedere a **Settings > Pages**.
4. Alla voce **Build and deployment > Source**, selezionare **Deploy from a branch**.
5. Indicare il ramo `main` e la cartella `/ (root)`, quindi salvare.
6. L'applicazione sarà accessibile all'indirizzo:
   ```text
   https://bonavolonta.github.io/research-key/
   ```

---

## Funzionamento locale e limiti della pseudonimizzazione

L'adozione di ResearchKey in protocolli di ricerca scientifica deve tenere conto delle seguenti caratteristiche operative ed etiche:

- **Assenza di database centralizzato**: l'applicazione non memorizza i codici generati su alcun server. Di conseguenza, non è possibile verificare centralmente l'esistenza di un codice né ripristinarlo in base a nome, matricola o email del partecipante.
- **Pseudonimizzazione, non anonimato assoluto**: il codice generato costituisce un identificativo pseudonimo. Qualora i dati raccolti nei questionari contengano combinazioni di variabili indirettamente re-identificanti, il set di dati rimane soggetto alle normative sulla protezione dei dati personali (es. GDPR) e ai requisiti del comitato etico di riferimento.
- **Dispositivi e browser multipli**: un partecipante che acceda da un dispositivo o browser diverso senza utilizzare il QR di recupero o il codice salvato genererà un nuovo identificativo. L'applicazione non impone il principio "una persona fisica = un codice" in assenza di autenticazione forte.
- **Persistenza e cancellazione cache**: l'identificativo locale viene memorizzato nel `localStorage` del browser. La pulizia manuale della cronologia/dati di navigazione o l'uso di finestre anonime comporta la perdita del dato locale: da qui la raccomandazione tassativa di conservare la tessera PDF o PNG.
- **Riservatezza della tessera**: chiunque entri in possesso della tessera o del link di recupero in essa contenuto può visualizzare e ripristinare il codice associato.

---

## Tecnologie e licenze di terze parti

L'applicazione è sviluppata in HTML5, CSS3 e JavaScript moderno standard (ECMAScript 6+).

- **Generazione QR Code**: il motore di codifica in `vendor/qr-bundle.js` si basa sull'implementazione originale di **Kazuhiko Arase** (licenza MIT), integrata tramite il progetto `qrcode-terminal` di **gtanner** (licenza Apache-2.0). I testi completi delle licenze sono disponibili nella directory `vendor/`:
  - `vendor/THIRD_PARTY_LICENSES.txt`
  - `vendor/LICENSE-qrcode-terminal.txt`
- **Motore PDF**: generatore proprietario integrato, senza librerie esterne.

---

## Archiviazione scientifica su Zenodo e attribuzione DOI

Il software è predisposto per l'archiviazione a lungo termine su [Zenodo](https://zenodo.org/) mediante l'integrazione ufficiale con GitHub Releases.

Alla pubblicazione della prima release ufficiale su GitHub, verrà assegnato al software un **Digital Object Identifier (DOI)** persistente rilasciato da Zenodo. I metadati per l'indicizzazione bibliografica sono definiti nel file `CITATION.cff`.

---

## Come citare il software

Se utilizzi ResearchKey nei tuoi progetti di ricerca scientifica o nella raccolta dati, sei invitato a citare il software utilizzando i metadati forniti nel file `CITATION.cff`.

### Formato testo (APA-like)
> Bonavolontà, G. (2026). *ResearchKey: Generatore statico client-side di identificativi pseudonimi per la ricerca scientifica* (Versione 1.0.0) [Software]. Disponibile su GitHub e archiviato su Zenodo.

### Formato BibTeX
```bibtex
@software{Bonavolonta_ResearchKey_2026,
  author  = {Bonavolont{\`a}, Gianmarco},
  title   = {{ResearchKey: Generatore statico client-side di identificativi pseudonimi per la ricerca scientifica}},
  version = {1.0.0},
  year    = {2026},
  license = {MIT}
}
```

---

## Licenza e Copyright

Questo progetto è un software personale e indipendente, sviluppato senza alcuna affiliazione istituzionale.

Distribuito con licenza **MIT**. Si veda il file [LICENSE](LICENSE) per i termini completi.

Copyright (c) 2026 Gianmarco Bonavolontà.
