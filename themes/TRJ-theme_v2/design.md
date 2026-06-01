# TRJ theme - v2

## Context
TRJ is the acronym of "Team Romagna Judo"; that is a Judo team, mostly focused on agonist training of young athletes.

## Design goals
The scope of these theme is to provide a light weight site with just some core information, but mostly used to drive visitors to social media presense of the Team, as these accounts are updated more frequently: Instagram and Facebook ('../../data/social.yml').

Beside links to social sites, the other important information to make visible is:
- sponsors ('../../data/sponsor.yml')
- lessons schedule ('../../content/corsi.md')
- dojo locations ('../../content/palestre.md')

There also some news that are seldomly updated:
- "news" ('../../content/notizie').

The new logo is available here: 'themes/TRJ-theme_v2/assets/images/trj_logo.jpg'.


## Constraints and opportunities

If some information would be better structured in another format, it perfectly fine to fully review also current content structure.


## Examples
Here's a list of public web sites with interesting solutions worth getting some inspiration from:
- https://starjudo.it/

---

## Proposed content model

Today most "core" information lives as free-form Markdown prose. For a hub-style
site that must stay effortless to update, this proposal moves the **structured
facts** (schedule, locations, contacts) into `data/` files, and keeps only genuine
prose (intro lines, news articles) in Markdown.

All data files live at the **site root** (`/data/`), shared by every theme, so
`TRJ-theme_v1` and `TRJ-theme_v2` read the same source. This is what lets us
migrate v1 onto the new format first, with no visible change, then build v2 on top.

Locations (stable) and schedule (changes every season) are kept in **two
separate files**, joined by the gym `id`.

### `data/sedi.yml` — gyms / locations (where)  ✅ implemented
A *sede* is a place: name, optional venue sub-heading, address, map. Powers the
`palestre` page. The `id` is the HTML anchor and the cross-link target from the
Corsi page (`/palestre#<id>`).

```yaml
- id: lugo                              # anchor + cross-link target
  nome: Lugo
  indirizzo: "Via Di Giù, 10 - Lugo (RA)"
  mappa_url: https://maps.app.goo.gl/...
  mappa_img: /images/maps/lugo.png
- id: castel-bolognese
  nome: Castel Bolognese
  struttura: "Palazzetto dello Sport…"  # optional venue sub-heading
  indirizzo: "Via Donati, 25 - Castel Bolognese (RA)"
  mappa_url: https://www.google.it/maps/place/...
  mappa_img: /images/maps/castelbolognese.png
```

### `data/corsi.yml` — schedule (when)  ✅ implemented
Weekly schedule grouped per gym, plus the current season and the notice shown atop
the Corsi page. Each block references a gym by `sede: <id>` (joined to `sedi.yml`
for the name). Set `avviso: ""` to hide the notice.

```yaml
stagione: "2025/26"
avviso: "I corsi per la stagione 2025/26 sono attivi da lunedì 15 settembre!"
calendario:
  - sede: lugo                          # -> data/sedi.yml id
    corsi:
      - fascia: "6-9 anni"              # the bold label
        orario: "17:00-18:00"
        giorni: [lunedì, venerdì]       # rendered "lunedì e venerdì" / "a, b e c"
  - sede: castel-bolognese
    corsi:
      - { fascia: "6-10 anni", orario: "18:00-19:15", giorni: [lunedì, giovedì] }
```

### `data/social.yml` — already data; proposed enrichment
Currently each entry has only a `url`. Add a `nome`/label (and optionally an icon
name) so templates can render an icon + readable handle instead of a bare link.

### `data/sponsor.yml` — already data; proposed enrichment
Add a `nome` (for `alt` text / accessibility) and, if useful, an optional `tier`
for ordering/emphasis.

### `data/societa.yml` — society & contact (proposed)
Lift the club's name, address, email and photo credit (today hard-coded in the v1
footer partial) into one small file, reused by the footer, the hero, a contact
block, and the Open Graph / share tags.

### News — stays as content pages
`content/notizie/*.md` remain Markdown articles (title + date + body). The hub home
shows the latest N as teasers. Optional: a `social_url` front-matter field so a
teaser can deep-link to the matching Instagram/Facebook post (serves the
"drive to social" goal).

## Rendering & gradual migration (v1)

To migrate without changing the live design, `TRJ-theme_v1` now renders the
locations and the schedule from `data/sedi.yml` + `data/corsi.yml` via two
shortcodes embedded in the existing Markdown pages; the remaining prose (intro,
enrolment link) stays in Markdown:

- `content/corsi.md`    → `{{</* corsi */>}}`     schedule grouped by gym
- `content/palestre.md` → `{{</* palestre */>}}`  locations with address + map

The shortcodes live in **each theme** (`layouts/shortcodes/corsi.html`,
`palestre.html`) so every theme can render the same data with its own markup.

### Normalisations applied (please review)
Driving the pages from data regularises a few pre-existing inconsistencies that
were baked into the old hand-written Markdown:

- every course line is now uniformly `Corso **{fascia}**: dalle {orario}, {giorni}`
  (before, "Agonisti" had no "Corso" prefix, and "adulti e agonisti" was two bold spans);
- day names are consistent — one old line read "lunedi e venerdi" without accents;
- a stray invisible character (U+2060) before one course line is removed;
- the Corsi page heading anchors are clean (`#lugo` instead of `#lugopalestrelugo`).

Visible content and layout are otherwise identical to the previous output.

## Open decisions (need your input)

**Layout / IA — gates the v2 mockup**
- one-pager vs multi-page?
- which v1 sections to drop: `Materiale`, `Album`, `Gare/risultati`, the "abilità"
  pages (agilità, equilibrio, forza, rispetto, sicurezza, tecnica), `allenatori`,
  `stage`, `campus`, `team`, `onoreficenze`?
- primary call-to-action: follow on social, see the schedule, or enrol?

**Brand**
- a transparent **PNG/SVG** logo (the current one is a JPG, no transparency);
- colour palette: reuse v1 blue/red, or a new one (e.g. from the logo)?
- typography; are web fonts (e.g. Google Fonts) acceptable?
- tone: playful (kids) vs clean/competitive (agonistic)?

**Content modelling**
- once `data/sedi.yml` covers them, keep the old `corsi.md`/`palestre.md` prose or retire it?
- day format: full names ("lunedì") or abbreviations ("lun")?
- social: any networks beyond Instagram/Facebook (YouTube, TikTok, WhatsApp)?

**Technical niceties (cheap to add)**
- Open Graph / share cards + favicon;
- analytics (currently disabled in `config.toml`).
