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

## v2 layout — single page (inspired by starjudo.it)

v2 is a **single scrolling page** with a sticky top nav of in-page anchors, in the
spirit of https://starjudo.it/. Sections top to bottom, each backed by existing
content or data:

1. **Hero** — new logo (`assets/images/trj_logo.jpg`), club name, short tagline,
   the season notice (`data/corsi.yml` → `avviso`), and one CTA button
   **"Contattaci"** that scrolls to the Contatti section.
2. **Perché il judo / I benefici** — icon cards reusing the existing *abilità*
   content (Agilità, Equilibrio, Forza, Rispetto, Sicurezza, Tecnica) and the
   images in `static/images/abilita/`.
3. **I corsi** — schedule cards per gym (`data/corsi.yml`).
4. **Dove siamo** — locations + maps (`data/sedi.yml`).
5. **Chi siamo** — short about/coach block reusing `content/allenatori/` and
   `content/team.md` (rough first pass; to iterate).
6. **Notizie** — latest 3 teasers (`content/notizie/`), linking to a full archive
   page (the only multi-page exception).
7. **Seguici** — prominent Instagram/Facebook call-out (`data/social.yml`): the
   primary "drive to social" goal.
8. **Sponsor & federazioni** — sponsor logos (`data/sponsor.yml`) + FIJLKAM /
   honours (`onoreficenze`).
9. **Contatti** — email and/or WhatsApp link (no form) + address. Target of the hero CTA.
10. **Footer** — società data, social, credits.

Top nav anchors (proposed): **Corsi · Dove siamo · Chi siamo · Notizie · Contatti**
plus the **Contattaci** button.

### Decisions taken
- Single page (multi-page only for the full Notizie archive).
- Primary CTA: a neutral **"Contattaci"** anchor — no trial-booking funnel.
- Contact via **email and/or WhatsApp links**, no form (skips Hugo/Netlify form
  detection; Netlify Forms remains a free option if wanted later).
- *abilità* and *allenatori/team* are **folded in** (sections 2 and 5), not dropped.
- Off the main nav: `Materiale` (surfaced as "iscrizioni" from Corsi), `Album`,
  `Gare/risultati`, `stage`, `campus`.

## Still open

**Brand**
- a transparent **PNG/SVG** logo (the current one is a JPG, no transparency);
- colour palette: reuse v1 blue/red, or a new one (e.g. from the logo)?
- typography; are web fonts (e.g. Google Fonts) acceptable?
- tone: playful (kids) vs clean/competitive (agonistic)?

**Contact details to display**
- email is known (`teamromagnajudo1972@libero.it`); is there a **WhatsApp / phone
  number** to show? (`data/social.yml` has only Instagram/Facebook today.)

**Content modelling**
- once `data/sedi.yml` + `data/corsi.yml` cover them, keep the old
  `corsi.md`/`palestre.md` prose or retire it?
- day format: full names ("lunedì") or abbreviations ("lun")?

**Technical niceties (cheap to add)**
- Open Graph / share cards + favicon;
- analytics (currently disabled in `config.toml`).
