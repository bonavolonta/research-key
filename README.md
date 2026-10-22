# ResearchKey

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.23555554.svg)](https://doi.org/10.5281/zenodo.23555554)

[Italiano](#italiano) | [English](#english)

---

<a id="italiano"></a>
## Italiano

**ResearchKey** è un'applicazione web statica e client-side progettata per la generazione, l'assegnazione e il recupero di identificativi pseudonimi specifici per studio nell'ambito della ricerca scientifica.

L'applicazione opera interamente nel browser del partecipante: non richiede database centralizzati, non impone la creazione di account e garantisce una rigorosa separazione delle identità tra studi differenti, prevenendo la trasmissione di codici identificativi ai server di hosting.

### Finalità e contesto di ricerca

Nelle indagini scientifiche longitudinali o a rilevazioni ripetute (es. pre/post test, questionari periodici o studi sul campo) è spesso necessario ricondurre le risposte dello stesso partecipante a una medesima chiave univoca, salvaguardando al tempo stesso la riservatezza ed evitando la raccolta diretta di dati personali (come nome, matricola o indirizzo email).

ResearchKey risponde a questa esigenza fornendo:
1. Un identificativo pseudonimo stabile per un determinato studio.
2. Una tessera digitale scaricabile (PNG o PDF) conservabile dal partecipante.
3. Un meccanismo di recupero tramite QR code che permette di sincronizzare il codice su altri dispositivi senza l'intermediazione di un server centrale.

### Funzionalità principali

- **Generazione crittografica locale**: codici alfanumerici a 12 caratteri formattati in triplette (`XXXX-XXXX-XXXX`), generati tramite `crypto.getRandomValues()`.
- **Alfabeto ottimizzato a 32 caratteri**: privo di caratteri visivamente ambigui (`I`, `O`, `0`, `1`), con distribuzione perfettamente uniforme (nessun modulo bias) e 60 bit di entropia ($2^{60} \approx 1{,}15 \times 10^{18}$ combinazioni).
- **Isolamento per studio**: memorizzazione su `localStorage` con chiavi isolate per ciascuno studio tramite parametro nell'URL (`?studio=nome-studio`).
- **Privacy-by-Design nel recupero**: il QR code contiene un URL con il codice posizionato nel frammento URI (`#id=...`). Per specifica dello standard HTTP, il frammento non viene mai trasmesso al server web.
- **Pulizia della cronologia di navigazione**: rimozione automatica del frammento dalla barra degli indirizzi (`history.replaceState`) non appena il codice viene acquisito.
- **Protezione dai conflitti**: rilevamento preventivo se nel browser è già presente un codice differente per il medesimo studio, con richiesta di conferma prima di qualsiasi sovrascrittura.
- **Esportazione tessera in PNG**: immagine ad alta risoluzione ($1400 \times 820$ px) pronta per la conservazione visiva o fotografica.
- **Esportazione tessera in PDF con link interattivo**: documento PDF 1.4 generato nativamente in JavaScript (senza librerie esterne), con un'annotazione ipertestuale sul QR code che consente il recupero del codice sia tramite scansione sia con un semplice clic.
- **Supporto per la stampa**: foglio di stile ottimizzato per la stampa cartacea diretta (`@media print`).
- **Zero dipendenze runtime di rete**: nessun cookie di profilazione, nessun tracciamento, nessun font remoto o script esterno.

### Istruzioni di utilizzo

#### Per i ricercatori

1. Distribuire ai partecipanti il link alla pagina web aggiungendo il parametro `studio` per identificare la propria ricerca:
   ```text
   https://bonavolonta.github.io/research-key/?studio=nome-dello-studio
   ```
   *Regole del parametro:* lettere, numeri, trattini e underscore (massimo 48 caratteri, es. `studio-pilota`, `survey-2026`). Se il parametro viene omesso, l'applicazione utilizza il valore predefinito `studio-generale`.
2. Invitare i partecipanti ad aprire il link all'avvio dello studio, a generare il proprio identificativo e a scaricare la tessera digitale (PDF o PNG).
3. Nei successivi questionari o moduli di rilevazione, richiedere unicamente l'inserimento dell'identificativo ResearchKey.

#### Per i partecipanti

1. Cliccare su **Genera il mio codice** all'inizio della ricerca.
2. Scaricare la propria tessera in formato **PDF** o **PNG** e conservarla in un luogo sicuro.
3. In caso di cambio browser o dispositivo, recuperare il codice inquadrando il QR code presente sulla tessera, cliccando sul QR all'interno del PDF, oppure reinserendo manualmente il codice nell'apposito campo di ripristino.

### Pubblicazione su GitHub Pages

L'applicazione è distribuita come sito statico tramite GitHub Pages:

1. Nel repository GitHub [bonavolonta/research-key](https://github.com/bonavolonta/research-key), accedere a **Settings > Pages**.
2. Alla voce **Build and deployment > Source**, selezionare **Deploy from a branch**.
3. Indicare il ramo `main` e la cartella `/ (root)`, quindi salvare.
4. L'applicazione è accessibile pubblicamente all'indirizzo:
   ```text
   https://bonavolonta.github.io/research-key/
   ```
   *(Il file `.nojekyll` presente nella root garantisce che i file statici e la directory `vendor/` vengano serviti correttamente senza interferenze del compilatore Jekyll).*

### Funzionamento locale e limiti della pseudonimizzazione

L'adozione di ResearchKey in protocolli di ricerca scientifica deve tenere conto delle seguenti caratteristiche operative ed etiche:

- **Assenza di database centralizzato**: l'applicazione non memorizza i codici generati su alcun server. Di conseguenza, non è possibile verificare centralmente l'esistenza di un codice né ripristinarlo in base a nome, matricola o email del partecipante.
- **Pseudonimizzazione, non anonimato assoluto**: il codice generato costituisce un identificativo pseudonimo. Qualora i dati raccolti nei questionari contengano combinazioni di variabili indirettamente re-identificanti, il set di dati rimane soggetto alle normative sulla protezione dei dati personali (es. GDPR) e ai requisiti del comitato etico di riferimento.
- **Dispositivi e browser multipli**: un partecipante che acceda da un dispositivo o browser diverso senza utilizzare il QR di recupero o il codice salvato genererà un nuovo identificativo. L'applicazione non impone il principio "una persona fisica = un codice" in assenza di autenticazione forte.
- **Persistenza e cancellazione cache**: l'identificativo locale viene memorizzato nel `localStorage` del browser. La pulizia manuale della cronologia/dati di navigazione o l'uso di finestre anonime comporta la perdita del dato locale: da qui la raccomandazione tassativa di conservare la tessera PDF o PNG.
- **Riservatezza della tessera**: chiunque entri in possesso della tessera o del link di recupero in essa contenuto può visualizzare e ripristinare il codice associato.

### Tecnologie e licenze di terze parti

L'applicazione è sviluppata in HTML5, CSS3 e JavaScript moderno standard (ECMAScript 6+).

- **Generazione QR Code**: il motore di codifica in `vendor/qr-bundle.js` si basa sull'implementazione originale di **Kazuhiko Arase** (licenza MIT), integrata tramite il progetto `qrcode-terminal` di **gtanner** (licenza Apache-2.0). I testi completi delle licenze sono disponibili nella directory `vendor/`:
  - `vendor/THIRD_PARTY_LICENSES.txt`
  - `vendor/LICENSE-qrcode-terminal.txt`
- **Motore PDF**: generatore proprietario integrato in JavaScript, senza librerie esterne.

### Archiviazione scientifica su Zenodo e identificativi persistenti DOI

Il software è archiviato in modo permanente su [Zenodo](https://zenodo.org/) mediante l'integrazione ufficiale con GitHub Releases, con attribuzione di identificativi persistenti **Digital Object Identifier (DOI)**:

- **DOI di versione (v1.0.0)**: [`10.5281/zenodo.23555555`](https://doi.org/10.5281/zenodo.23555555) — identifica in modo immutabile e riproducibile la specifica release v1.0.0.
- **Concept DOI (tutte le versioni)**: [`10.5281/zenodo.23555554`](https://doi.org/10.5281/zenodo.23555554) — identifica l'opera software complessiva e reindirizza sempre all'ultima versione pubblicata.
- **Record pubblico Zenodo**: [https://zenodo.org/records/23555555](https://zenodo.org/records/23555555)
- **Applicazione web attiva (GitHub Pages)**: [https://bonavolonta.github.io/research-key/](https://bonavolonta.github.io/research-key/)
- **Release ufficiale del codice (GitHub Releases)**: [https://github.com/bonavolonta/research-key/releases/tag/v1.0.0](https://github.com/bonavolonta/research-key/releases/tag/v1.0.0)

### Come citare il software

Se utilizzi ResearchKey nei tuoi progetti di ricerca scientifica o nella raccolta dati, sei invitato a citare il software utilizzando i metadati forniti nel file `CITATION.cff`.

#### Formato testo (APA-like)
> Bonavolontà, G. (2026). *ResearchKey: Generatore statico client-side di identificativi pseudonimi per la ricerca scientifica* (Versione 1.0.0) [Software]. Zenodo. https://doi.org/10.5281/zenodo.23555555

#### Formato BibTeX
```bibtex
@software{Bonavolonta_ResearchKey_2026,
  author    = {Bonavolont{\`a}, Gianmarco},
  title     = {{ResearchKey: Generatore statico client-side di identificativi pseudonimi per la ricerca scientifica}},
  version   = {1.0.0},
  year      = {2026},
  publisher = {Zenodo},
  doi       = {10.5281/zenodo.23555555},
  url       = {https://bonavolonta.github.io/research-key/}
}
```

### Licenza e Copyright

Questo progetto è un software personale e indipendente, sviluppato senza alcuna affiliazione istituzionale.

Distribuito con licenza **MIT**. Si veda il file [LICENSE](LICENSE) per i termini completi.

Copyright (c) 2026 Gianmarco Bonavolontà.

---

<a id="english"></a>
## English

**ResearchKey** is a static, client-side web application designed for generating, assigning, and recovering study-specific pseudonymous identifiers in scientific research.

The application executes entirely within the participant's web browser: it requires no central databases, enforces no user account creation, and ensures strict identity isolation across distinct research projects, preventing the transmission of participant identifiers to web hosting servers.

### Scientific Purpose and Research Context

In longitudinal scientific investigations and repeated-measures research designs (e.g., pre/post-test assessments, periodic surveys, or field trials), investigators frequently need to link successive responses from the same participant using a stable unique key, while safeguarding privacy and avoiding the direct collection of personally identifiable information (PII, such as full names, student IDs, or email addresses).

ResearchKey addresses this requirement by providing:
1. A stable pseudonymous identifier tailored to a specific study.
2. A downloadable digital ID card (PNG or PDF format) retained by the participant.
3. A cryptographic QR-code recovery mechanism allowing cross-device synchronization without third-party server involvement.

### Key Features

- **Local Cryptographic Generation**: 12-character alphanumeric keys formatted in triplets (`XXXX-XXXX-XXXX`), generated using `crypto.getRandomValues()`.
- **Optimized 32-Character Alphabet**: Eliminates visually ambiguous characters (`I`, `O`, `0`, `1`), providing an exact uniform probability distribution (zero modulo bias) and 60 bits of cryptographic entropy ($2^{60} \approx 1.15 \times 10^{18}$ combinations).
- **Study-Specific Isolation**: Local persistence in browser `localStorage` keyed separately for each study via URL parameter (`?studio=study-name`).
- **Privacy-by-Design Recovery Mechanism**: The QR code encodes a recovery URL with the identifier stored exclusively in the URI fragment (`#id=...`). Under the HTTP/HTTPS standard, URI fragments are never transmitted to the web server during page requests.
- **Automated Navigation History Cleansing**: Instantaneous removal of the fragment from the browser address bar (`history.replaceState`) upon key ingestion.
- **Conflict Prevention**: Built-in detection alert when an existing local identifier differs from an imported recovery key, requiring explicit confirmation before any overwrite.
- **High-Resolution PNG Card Export**: $1400 \times 820$ px digital graphic card suitable for photo rolls, screenshots, or local image storage.
- **Interactive Native PDF Card Export**: Pure client-side PDF 1.4 document generated in JavaScript (zero external dependencies), including an interactive hyperlink annotation overlaid on the QR code enabling retrieval via either optical scan or direct click.
- **Print Optimization**: Dedicated print stylesheet (`@media print`) for clean, ink-efficient physical printing.
- **Zero Network Runtime Dependencies**: No tracking pixels, no profiling cookies, no external web fonts, and no remote CDN scripts.

### Usage Instructions

#### For Researchers

1. Provide participants with the web link containing the `studio` parameter configured for your specific study:
   ```text
   https://bonavolonta.github.io/research-key/?studio=your-study-name
   ```
   *Parameter constraints:* alphanumeric characters, hyphens, and underscores (up to 48 characters, e.g., `pilot-study`, `trial-2026`). If omitted, the application defaults to `studio-generale`.
2. Instruct participants to open the link at the start of the study, generate their personal research key, and download their digital ID card (PDF or PNG).
3. In subsequent survey rounds or assessment forms, request only the participant's ResearchKey identifier.

#### For Participants

1. Click **Genera il mio codice** (Generate my key) at the commencement of the research project.
2. Download your digital card as a **PDF** or **PNG** file and store it securely.
3. If switching devices or browsers, recover your key by scanning the QR code on your card, clicking the QR code within the PDF document, or manually entering the saved code into the restore field.

### Deployment on GitHub Pages

The application is deployed as a static site on GitHub Pages:

1. In the GitHub repository [bonavolonta/research-key](https://github.com/bonavolonta/research-key), navigate to **Settings > Pages**.
2. Under **Build and deployment > Source**, select **Deploy from a branch**.
3. Select the `main` branch and `/ (root)` directory, then click Save.
4. The application is publicly accessible at:
   ```text
   https://bonavolonta.github.io/research-key/
   ```
   *(The `.nojekyll` file present in the repository root ensures static files and the `vendor/` directory are served without Jekyll preprocessing).*

### Local Architecture and Limitations of Pseudonymization

Deploying ResearchKey within scientific protocols requires consideration of the following operational and ethical boundaries:

- **Absence of a Central Registry**: Identifiers are not registered or cataloged on any central server. Consequently, researchers cannot centrally query whether a code exists, nor recover lost codes based on participant names or email addresses.
- **Pseudonymization vs. Absolute Anonymity**: ResearchKey generates pseudonymous keys, not intrinsic anonymity. If survey instruments collect indirect identifiers that collectively allow re-identification, the dataset remains subject to applicable data protection regulations (e.g., GDPR) and ethics committee protocols.
- **Multiple Browsers and Devices**: A participant accessing the application from an alternative device without using the recovery link or entering their saved key will generate a new identifier. The system does not enforce a "one person = one key" constraint without central identity verification.
- **Browser Cache Clearance**: Local persistence relies on `localStorage`. Clearing browser data or using private browsing windows clears stored keys: participants must preserve their PDF or PNG card.
- **Card Confidentiality**: Any individual possessing the ID card or the recovery link contained within it can view and restore the corresponding key.

### Technologies and Third-Party Licenses

ResearchKey is implemented in pure HTML5, CSS3, and standard modern JavaScript (ECMAScript 6+).

- **QR Code Engine**: The QR generator in `vendor/qr-bundle.js` is based on the original implementation by **Kazuhiko Arase** (MIT License), incorporated via the `qrcode-terminal` project by **gtanner** (Apache-2.0 License). Full license texts are maintained in the `vendor/` directory:
  - `vendor/THIRD_PARTY_LICENSES.txt`
  - `vendor/LICENSE-qrcode-terminal.txt`
- **PDF Engine**: Lightweight, custom-built native client-side PDF 1.4 generator with zero external library overhead.

### Scientific Archival on Zenodo and Persistent Identifiers (DOIs)

ResearchKey is permanently archived on [Zenodo](https://zenodo.org/) via the official GitHub Releases integration, with registered **Digital Object Identifiers (DOIs)**:

- **Version-Specific DOI (v1.0.0)**: [`10.5281/zenodo.23555555`](https://doi.org/10.5281/zenodo.23555555) — immutably and reproducibly identifies the specific v1.0.0 release.
- **Concept DOI (all versions)**: [`10.5281/zenodo.23555554`](https://doi.org/10.5281/zenodo.23555554) — represents the overall software suite and always resolves to the latest release.
- **Zenodo Public Record**: [https://zenodo.org/records/23555555](https://zenodo.org/records/23555555)
- **Live Web Application (GitHub Pages)**: [https://bonavolonta.github.io/research-key/](https://bonavolonta.github.io/research-key/)
- **Source Code Release (GitHub Releases)**: [https://github.com/bonavolonta/research-key/releases/tag/v1.0.0](https://github.com/bonavolonta/research-key/releases/tag/v1.0.0)

### How to Cite

If you employ ResearchKey in your scientific investigations or data collection procedures, please cite the software using the metadata specified in `CITATION.cff`.

#### Text Format (APA-Style)
> Bonavolontà, G. (2026). *ResearchKey: Open-source client-side pseudonymous identifier generator for research participants* (Version 1.0.0) [Software]. Zenodo. https://doi.org/10.5281/zenodo.23555555

#### BibTeX Format
```bibtex
@software{Bonavolonta_ResearchKey_2026,
  author    = {Bonavolont{\`a}, Gianmarco},
  title     = {{ResearchKey: Open-source client-side pseudonymous identifier generator for research participants}},
  version   = {1.0.0},
  year      = {2026},
  publisher = {Zenodo},
  doi       = {10.5281/zenodo.23555555},
  url       = {https://bonavolonta.github.io/research-key/}
}
```

### License and Copyright

This project is personal and independent software, developed without institutional affiliation.

Released under the **MIT License**. See the [LICENSE](LICENSE) file for complete terms.

Copyright (c) 2026 Gianmarco Bonavolontà.
