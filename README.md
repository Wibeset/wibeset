<p align="center">
  <img src="og.png" alt="We are Wibeset — a small studio in Saguenay, Québec, that builds web and mobile products" width="100%" />
</p>

<h1 align="center">wibeset.ca</h1>

<p align="center">
  The presentation site for <strong>Wibeset</strong> — a small studio in Saguenay,
  Québec, that builds web and mobile products.
</p>

<p align="center">
  <a href="https://wibeset.ca"><strong>wibeset.ca</strong></a> ·
  <a href="https://wibeset.ca/fr/">Version française</a>
</p>

<p align="center">
  <em>Static HTML · No build step · No JavaScript · GitHub Pages</em>
</p>

---

One page, in English and French. No framework, no bundler, no dependencies —
two HTML files, one stylesheet, four fonts. The repo root *is* the site.

## Run it locally

The pages use root-relative paths (`/favicon.ico`, `/site.webmanifest`), so
opening `index.html` straight off the disk loses the icons and the manifest.
Serve it instead:

```bash
python3 -m http.server 8000
```

Then open <http://localhost:8000/> and <http://localhost:8000/fr/>.

## Structure

```
index.html              English page
fr/index.html           French page — a full twin, not a stub
styles.css              The whole stylesheet
fonts/                  Self-hosted woff2 (latin + latin-ext subsets)
llms.txt                Structured brief written for LLMs
robots.txt              Allows all crawlers, names the AI ones explicitly
sitemap.xml             Both URLs
site.webmanifest        PWA manifest
CNAME                   wibeset.ca
og.png                  1200×630 social card
favicon.ico, favicon-*.png, apple-touch-icon.png, android-chrome-*.png
.github/workflows/      Pages deployment
```

## Design

Same look and feel as [dominicmartineau.com](https://dominicmartineau.com):
near-black background with one cool bloom top-left, **Headland One** for the
section headings, generous padding that steps up at 810px and 1100px.

The wordmark is **Great Vibes**, and it needs three corrections that are easy to
lose in a refactor:

- **`font-size: min(calc((100vw - <pad>) / 3.05), 290px)`** — the divisor is
  calibrated to the glyph run, not guessed. It fills the text column at every
  breakpoint (the `<pad>` matches the `article` padding of that breakpoint) and
  caps at 290px so it stops growing on wide screens.
- **`padding-left: 0.02em`** — the W's opening swash starts 0.0172em *left* of
  the line origin. Without the pad it hangs outside the text column and reads as
  clipped.
- **`margin-top: 0.15em`** — that same swash climbs above the cap line. Any
  tighter and it crosses the "We are" label above it.

Links carry no colour; they are marked by a faint underline that goes solid
white on hover. Project names never wrap (`white-space: nowrap`), and the
projects table stacks to one column below 600px.

Fonts are self-hosted rather than pulled from Google Fonts — no third-party
request, no render dependency, no privacy question.

## Answer-engine notes

The site is built to be quoted accurately by AI assistants, not just crawled.
Three details carry most of that, and all three are easy to undo by accident:

- **The opening sentence is self-contained** — "Wibeset is a small studio in
  Saguenay, Québec, that builds web and mobile products." An extractor needs the
  `<entity> is a <category> that <does X>` pattern to attribute a claim. Reword
  it so the subject lives in the heading instead, and the sentence becomes an
  unattributable fragment.
- **The JSON-LD `@graph` shares one `@id`** across both languages
  (`https://wibeset.ca/#organization`). Give the pages separate ids and they
  describe two organizations rather than one.
- **Unreleased products carry no `offers`** and say "Not released yet" in their
  description — otherwise an assistant will happily announce a product that
  doesn't exist yet.

`llms.txt` holds the long-form facts (platform, price, status, macOS versions)
that would clutter the page. Keep it in sync when a product ships.

See `AEO.md` (untracked, local only) for the full audit and what is still open.

## Deploying

Push to `main`. `.github/workflows/deploy.yml` verifies the site, then publishes
it to GitHub Pages.

The verify step exists because a static site fails quietly: it checks that every
referenced asset is present, that the JSON-LD in both pages parses, that the
manifest and sitemap are well-formed, and that `CNAME` still reads `wibeset.ca`.
A typo in any of those costs the icons, the rich results, or the domain — with no
error anywhere.

You can also trigger a deploy by hand from the **Actions** tab
(`workflow_dispatch`).

### DNS

The apex needs GitHub Pages' four A records, and `www` a CNAME:

| Type | Host | Value |
|---|---|---|
| A | `@` | `185.199.108.153` |
| A | `@` | `185.199.109.153` |
| A | `@` | `185.199.110.153` |
| A | `@` | `185.199.111.153` |
| CNAME | `www` | `wibeset.github.io` |

AAAA records (`2606:50c0:8000::153` through `8003::153`) are optional but worth
adding. Enable **Enforce HTTPS** once the certificate is issued.

## Editing

Adding or changing a project means touching four places, in both languages:

1. the row in the projects `<table>`;
2. its node in the JSON-LD `@graph`;
3. its entry in `llms.txt`;
4. the `<meta name="description">` list.

Bump `dateModified` in the JSON-LD and `lastmod` in `sitemap.xml` while you're
there.

## Also by the studio

- [Luere](https://luere.app) — Shrink, convert, crop and resize your images
- [Luere Icon Studio](https://luere.app/icon-studio/) — One logo. Every icon

---

<p align="center">
  <sub>Built by <a href="https://dominicmartineau.com">Dominic Martineau</a> in
  Saguenay, Québec.</sub>
</p>
