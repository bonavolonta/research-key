# Changelog

Tutte le modifiche rilevanti a questo progetto sono documentate in questo file.

Il formato è basato su [Keep a Changelog](https://keepachangelog.com/it/1.0.0/) e questo progetto aderisce a [Semantic Versioning](https://semver.org/lang/it/).

## [1.0.0] - 2026-10-22

### Aggiunto
- Generazione crittografica locale di codici pseudonimi a 12 caratteri (`XXXX-XXXX-XXXX`) basata su `crypto.getRandomValues()` con alfabeto a 32 caratteri privo di ambiguità visive (60 bit di entropia).
- Isolamento della persistenza locale tramite `localStorage` basato su chiavi specifiche per studio (`?studio=...`).
- Sistema di recupero crittografico tramite QR code con codice veicolato esclusivamente nel frammento URL (`#id=...`), impedendo la trasmissione dei dati al server web (privacy-by-design).
- Pulizia automatica della barra degli indirizzi e della cronologia di navigazione tramite `history.replaceState`.
- Rilevamento e gestione dei conflitti con richiesta di conferma prima di sovrascrivere codici preesistenti.
- Esportazione della tessera identificativa in formato immagine PNG ad alta definizione ($1400 \times 820$ px).
- Motore di esportazione PDF 1.4 nativo in JavaScript client-side (formato A4 orizzontale) con incorporamento grafico e annotazione ipertestuale interattiva sul QR code.
- Stili di stampa dedicati (`@media print`) per la stampa diretta della tessera dal browser.
- Interfaccia web statica accessibile, responsive e priva di dipendenze runtime esterne o chiamate di rete.
- Integrazione bundle QR Code client-side (`vendor/qr-bundle.js`) con documentazione delle licenze terze parti (MIT e Apache-2.0).
- Predisposizione per la pubblicazione su GitHub Pages (`.nojekyll`) e per l'archiviazione scientifica con metadati Citation File Format (`CITATION.cff`).
