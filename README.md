# areel.org

Landing page for **areel.org** — open source works.

Self-contained HTML. No build step, no dependencies, no framework. Fonts come from
Google Fonts and the album player is a Spotify embed; everything else is inline,
and each page carries its own copy of the design tokens.

## Structure

| Path | What it is |
|---|---|
| `index.html` | The landing page |
| `fishball/` | Language stub — redirects to `fishball/en/` |
| `fishball/en/` | FishBall's official site, in English |
| `fishball/zh/` | FishBall's official site, in Chinese |
| `fishball/latest.json` | Release manifest. **The app reads this on launch** |
| `hydrogen/` | Language stub — redirects to `hydrogen/en/` |
| `hydrogen/en/` | Hydrogen's official site, in English |
| `hydrogen/zh/` | Hydrogen's official site, in Chinese |
| `hydrogen/favicon.svg` | Hydrogen's own mark — a copy of the app's `web/public/favicon.svg` |

### `index.html`

| Section | What it is |
|---|---|
| Hero | AREEL wordmark, GitHub link, site spec block |
| Origin | Collapsed to a magenta hairline; expands to the name story + album embed |
| `01 / Works` | Two units, each in its own card: **Hydrogen** (Micro Agents, the request flow, two use cases) and **FishBall** (source grading, the turn pipeline, two use cases) |
| `02 / Pending` | Placeholder for what lands next |

## FishBall releases

[FishBall](https://github.com/Arrosam/fishball) is sideloaded, so this site is where it
learns that a new build exists. On launch the app fetches `fishball/latest.json` and
offers anything whose `versionCode` is higher than its own.

```json
{
  "versionCode": 2,
  "versionName": "0.2a",
  "url": "https://github.com/Arrosam/fishball/releases/latest/download/fishball.apk",
  "size": 1879602,
  "notes": "shown verbatim in the modal, in Chinese"
}
```

To publish an update:

1. Attach the APK to a GitHub release on `Arrosam/fishball`, named **`fishball.apk`** —
   the `releases/latest/download/` URL only stays stable if the asset name does.
2. Bump `versionCode`, `versionName` and `size` here, and write `notes`.
3. Push. GitHub Pages redeploys, and the next phone to open the app is offered it.

`versionCode` is what decides, never `versionName`: comparing "0.1a" to "0.10a" as text is
how an update stops arriving three releases later. The English page reads its version number
from the same file, so it never has to be edited to match.

Each unit with a site has a language stub — `fishball/`, `hydrogen/` — that picks between
`en/` and `zh/` by browser language, with both offered on the stub itself.

Design follows the *AREEL* album cover: flat concrete grey, one magenta sweep,
CAD hairlines visible through clear-plastic panels, checkerboard, near-black band.
FishBall's site shares that language, because the app is built to it.

**Hydrogen's site is the exception.** It is built to Hydrogen's own design language — the
dashboard's dark ink palette and cyan→teal brand pair, Inter, rounded cards on hairline borders,
Bootstrap Icons, and the emission-line spectrum its brand mark is cut from. The tokens are the
app's Tailwind theme (`web/tailwind.config.js`, `web/src/index.css`) copied verbatim, and the hero
is the README hero (`docs/images/hero.svg`) set as a page. When the app's theme changes, change
these pages to match; the only link back to areel.org's own language is the footer.

## Local preview

Open `index.html` directly, or serve it:

```bash
python -m http.server 8000
```

## Deploy — GitHub Pages + Cloudflare DNS

Served by GitHub Pages from `main` at the repo root. `CNAME` pins the custom
domain to the apex, `areel.org`. DNS lives at Cloudflare.

### Required DNS records

Both records, in the Cloudflare dashboard for `areel.org`:

| Type | Name | Target | Proxy |
|---|---|---|---|
| CNAME | `areel.org` | `arrosam.github.io` | **DNS only** (grey cloud) |
| CNAME | `www` | `arrosam.github.io` | **DNS only** |

No GitHub IP addresses need hardcoding: Cloudflare flattens the apex CNAME into
the correct `A`/`AAAA` records by itself, and follows GitHub if those ever change.

Both records are required. GitHub validates the primary domain *and* its
alternate name — with an apex primary the alternate is `www`, so a missing `www`
record produces `InvalidDNSError` even while the apex reports as valid. With both
in place, `www.areel.org` redirects to `areel.org`.

> **Proxy must be off.** An orange cloud hides the real record from GitHub, so
> domain verification fails and no TLS certificate is ever issued. Leave these
> records DNS-only.

### After DNS propagates

1. **Settings → Pages** — both the domain and its alternate should show green.
2. Tick **Enforce HTTPS** once the certificate is issued (this can take up to
   ~15 minutes after the record resolves).

Pushes to `main` redeploy automatically.
