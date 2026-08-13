# areel.org

Landing page for **areel.org** — open source works.

A single self-contained `index.html`. No build step, no dependencies, no framework.
Fonts come from Google Fonts and the album player is a Spotify embed; everything
else is inline.

## Structure

| Section | What it is |
|---|---|
| Hero | AREEL wordmark, GitHub link, site spec block |
| Origin | Collapsed to a magenta hairline; expands to the name story + album embed |
| `01 / Works` | Hydrogen — Micro Agents, the request flow, two use cases |
| `02 / Pending` | Placeholder for what lands next |

Design follows the *AREEL* album cover: flat concrete grey, one magenta sweep,
CAD hairlines visible through clear-plastic panels, checkerboard, near-black band.

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
