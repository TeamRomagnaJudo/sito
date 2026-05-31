# Team Romagna Judo 1972 — sito

Sito statico generato con [Hugo](https://gohugo.io).

## Requisiti

- **Hugo `0.101.0` _extended_** (la versione _extended_ è obbligatoria: il CSS
  è compilato da SCSS tramite la _pipeline_ interna di Hugo `toCSS`, disponibile
  solo nella edizione _extended_).
- Su Netlify la versione è fissata in [`netlify.toml`](netlify.toml)
  (`HUGO_VERSION = "0.101.0"`); l'immagine di build di Netlify installa
  automaticamente l'edizione _extended_.

Verifica di avere l'edizione giusta (deve comparire `+extended`):

```
hugo version
# hugo v0.101.0-...+extended darwin/arm64 ...
```

## Struttura

I contenuti del sito sono separati dalla loro presentazione (i _temi_):

```
content/        testi e pagine (markdown)
data/           dati condivisi (sponsor, gare, album, ...)
static/         risorse condivise: immagini di contenuto, materiale, cms
config.toml     configurazione del sito; la riga `theme = "..."` sceglie il tema
themes/
  TRJ-theme_v1/   design originale del sito (tema attivo)
  TRJ-theme_v2/   nuovo layout sperimentale
```

Ogni tema contiene la propria presentazione (`layouts/`, immagini specifiche del
tema in `static/`, e i sorgenti SCSS in `assets/scss/`). Le immagini di
contenuto (logo, sponsor, mappe, caricamenti del CMS, ...) restano condivise in
`static/images/` alla radice, così entrambi i temi le riutilizzano.

### CSS / SCSS

Il foglio di stile **non** va compilato a mano: è Hugo a trasformare lo SCSS in
CSS durante il build (`resources.Get "scss/..." | toCSS | fingerprint`, vedi la
partial `head`/`meta` di ciascun tema). Per modificare lo stile basta editare i
file in `themes/<tema>/assets/scss/` e ricostruire — niente strumenti esterni.

## Scegliere / cambiare tema

In [`config.toml`](config.toml):

```toml
theme = "TRJ-theme_v1"   # oppure "TRJ-theme_v2"
```

Per provare un tema senza modificare la configurazione si può usare il flag
`--theme`:

```
../hugo server --theme TRJ-theme_v2
```

## Comandi

Percorso del binario: `../hugo` (vedi Requisiti).

#### Build
```
../hugo --cleanDestinationDir --verbose
```

#### Server di sviluppo (con _watch_)
```
../hugo server --disableFastRender --cleanDestinationDir --baseURL localhost --bind 0.0.0.0 --verbose
```

#### Includere bozze / contenuti futuri / scaduti
Aggiungere: `--buildFuture --buildDrafts --buildExpired`
