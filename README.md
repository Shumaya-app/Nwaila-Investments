# nwaila.co.za

Static site for Nwaila Investments. No build step, no dependencies, no server.
`index.html` is fully self-contained — everything else is metadata, icons and one extra page.

## Files

| File | What it does |
|---|---|
| `index.html` | The entire site. Open it locally to check it before pushing. |
| `privacy.html` | Privacy notice (POPIA). Linked from the footer. |
| `404.html` | Branded not-found page GitHub Pages serves automatically. |
| `CNAME` | Tells GitHub Pages to answer on `nwaila.co.za`. Do not delete. |
| `.nojekyll` | Stops GitHub reprocessing the files. Hidden, but do not delete. |
| `robots.txt` | Lets search engines index the site, points at the sitemap. |
| `sitemap.xml` | Submit to Google Search Console. Update `lastmod` after big changes. |
| `og-image.png` | 1200×630 preview card for WhatsApp, LinkedIn, Facebook, X. |
| `favicon.png` | Browser tab icon. |
| `apple-touch-icon.png` | Icon when saved to an iPhone home screen. |
| `nwaila-wordmark.png` | Logo for press, decks, directory listings. Not used by the site. |

Upload all of these to the **repository root**, not inside a folder.

## Publishing

1. Create a repository. Public is required for free Pages on a custom domain.
2. Upload the files.
3. **Settings → Pages → Build and deployment**: source `Deploy from a branch`, branch `main`, folder `/ (root)`.
4. Under **Custom domain**, enter `nwaila.co.za` and save.
5. Do the DNS records below, then tick **Enforce HTTPS** once it becomes available.

To update later, replace `index.html` and commit. Live in about a minute.

---

## Two things to switch on

### 1. Google Analytics

Open `index.html`, search for `MEASUREMENT_ID`, replace it with your GA4 ID (`G-ABC123XYZ`).
Until you do, nothing loads and nothing is tracked.

Events already wired up:

- `view_home`, `view_check`, `view_result`, `view_report`, `view_demos`, `view_about`
- `health_check_complete` — someone finished all eighteen questions
- `report_open` / `report_print` — someone viewed or saved their PDF report
- `discovery_start` — someone clicked through to book a call, with their score attached

### 2. The Tally form

Open `index.html`, search for `TALLY_FORM = ""`, paste your form link between the quotes:

```js
const TALLY_FORM = "https://tally.so/r/xxxxx";
```

Add these hidden fields in Tally, named exactly:

`overall_score`, `band`, `source`, `report_url`
`score_compliance`, `score_finance`, `score_tax`, `score_people`, `score_funding`, `score_growth`
`q1` … `q18`

Turn on email notifications to **info@nwaila.co.za**. Every submission then arrives with the
respondent's full health check, and `report_url` links straight to their branded report.

Leave `TALLY_FORM` empty and the built-in on-page form is used instead.

---

## DNS

Do this wherever the domain is held — Domains.co.za, Xneelo, Afrihost, GoDaddy, Cloudflare.
Look for *DNS*, *Zone editor* or *Advanced DNS*.

### Point the bare domain at GitHub

Delete existing **A** records on `@` (shown as blank or `nwaila.co.za`), then add all four:

| Type | Host | Value | TTL |
|---|---|---|---|
| A | @ | 185.199.108.153 | 3600 |
| A | @ | 185.199.109.153 | 3600 |
| A | @ | 185.199.110.153 | 3600 |
| A | @ | 185.199.111.153 | 3600 |

If your registrar supports **ALIAS** or **ANAME**, one of those pointing `@` at
`YOUR-USERNAME.github.io` is better — but the four A records always work.

### Point www at GitHub

| Type | Host | Value | TTL |
|---|---|---|---|
| CNAME | www | YOUR-USERNAME.github.io. | 3600 |

Replace `YOUR-USERNAME` with the account or organisation that owns the repository.
Delete any existing A or CNAME on `www` first.

### Leave email alone

**Do not touch MX records.** They route info@nwaila.co.za and have nothing to do with the website.
Same for TXT records holding SPF, DKIM or DMARC. If a registrar offers to reset the zone to
defaults, say no — it wipes those.

### Verification records

GitHub → Settings → Pages → *Add a domain* gives you a TXT record. Adding it stops anyone else
claiming the domain on Pages later:

| Type | Host | Value |
|---|---|---|
| TXT | _github-pages-challenge-YOUR-USERNAME | (string GitHub shows you) |

Google Search Console asks for one more, on `@`:

| Type | Host | Value |
|---|---|---|
| TXT | @ | google-site-verification=… |

A domain can hold several TXT records. Add, don't replace.

### Then wait

DNS takes 15 minutes to a few hours, occasionally 24. Once it resolves, go back to
**Settings → Pages** and tick **Enforce HTTPS**. If greyed out, the certificate is still being
issued — check again in an hour.

Both addresses work, with `www` redirecting to the bare `nwaila.co.za`. Keep it that way; one
canonical address is better for search than two.

### Checking

```
dig nwaila.co.za +short        # the four 185.199.x.153 addresses
dig www.nwaila.co.za +short    # YOUR-USERNAME.github.io
```

Or paste the domain into dnschecker.org.

### If something breaks

- **404 on the custom domain** — `CNAME` missing from the repo root, or Pages on the wrong branch.
- **"Domain does not resolve to the GitHub Pages server"** — DNS not propagated, or an old A record remains.
- **Site loads unstyled** — `.nojekyll` is missing.
- **Certificate warning** — remove the custom domain in Pages settings, save, add it back.

---

## Search and social, once live

- **Google Search Console** — add `https://nwaila.co.za/`, verify by TXT, submit `sitemap.xml`.
- **Bing Webmaster Tools** — imports from Search Console in one click.
- **Google Business Profile** — worth claiming for the Bryanston address; it feeds local search.
- **LinkedIn Post Inspector** and **Facebook Sharing Debugger** — run the URL through each once.
  They cache hard, so re-scrape whenever `og-image.png` changes.

## Notes

- The report link (`?r=…`) encodes answers in the address itself. Nothing is stored anywhere —
  no database, no cookies, which keeps POPIA simple. `privacy.html` says so plainly.
- GA4 does set analytics cookies. If you'd rather stay cookie-free, Plausible or Fathom are
  drop-in replacements for that one script block.
