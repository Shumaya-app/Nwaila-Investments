# nwaila.co.za

Static site for Nwaila Investments. No build step, no dependencies, no server.
`index.html` is fully self-contained - everything else is metadata, icons and one extra page.

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
| `README.md` | This file - your notes. The site doesn't use it; delete it if you'd rather. |

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

Already wired to `G-H0ZGCDV538` (property 451838042). Nothing to do.

Analytics are **consent-gated**: no script loads and no cookie is set until a visitor presses
Accept on the banner. "No thanks" means nothing ever loads, and the choice is remembered in their
own browser.

Events already wired up:

- `view_home`, `view_check`, `view_result`, `view_report`, `view_demos`, `view_about`
- `health_check_complete` - someone finished all eighteen questions. Carries `overall_score`,
  the six `score_*` values and `weakest`, all anonymous - no name, no email, nothing identifying.
  In GA4, register these under *Admin → Custom definitions* as custom dimensions to chart them.
- `report_open` / `report_print` - someone viewed or saved their PDF report
- `discovery_start` - someone opened the booking page
- `discovery_booked` - someone actually booked a slot

### 2. The discovery call (Calendly)

Already wired to `calendly.com/nwaila-advisory/business-health-check`, embedded in the page  - 
visitors pick a slot without leaving the site, and the invitation is sent by Calendly.

The embed passes your colours through the URL (`#0F150F` ground, `#F0F0EB` text, `#7AB648`
accent), so it matches the page without any custom CSS. Colour parameters need a paid Calendly
plan; on the free plan they're ignored and the widget renders light.

**Add one custom question to the event type** and the health check comes with every booking:

- Question 1 must be **"Health check results"** (any wording, but it has to be the first custom
  question). The site passes the visitor's results link into it automatically as `a1`.
- Opening that link shows their full report. Add `&full=1` to the end - already included in the
  link the site sends - and you also see the appendix: all eighteen questions with the answer they
  chose. Clients never see the appendix; it renders only with that flag.

Worth doing in Calendly itself:

- Add a required question to the event type: **"What's the constraint right now?"** That's the one
  thing the old form collected that scheduling doesn't.
- Set the duration to 45 minutes to match the copy on the page.
- Turn on reminder emails, and add your Zoom or Meet link to the event type.

When someone books, Calendly posts an event up to the page, so the site shows its own "You're
booked." confirmation and logs `discovery_booked` to Analytics.

The health check is deliberately **not** attached to the booking. Results stay in the visitor's
browser; the call is where the conversation starts.

---

## DNS for nwaila.co.za

Zone is at your existing host. GitHub account is **shumaya-app**.

### 1. Before you switch: protect the mail subdomains

Four records are CNAMEd to the apex, so they will follow it to GitHub the moment you change it:
`mail`, `pop`, `imap`, `smtp`. Incoming mail is safe (MX points at Microsoft 365), but any
Outlook or phone configured with `smtp.nwaila.co.za` or `imap.nwaila.co.za` will stop working.

Change all four from CNAME to A records on the old host first:

| Name | TTL | Type | Record |
|---|---|---|---|
| mail.nwaila.co.za. | 3600 | A | 129.232.194.138 |
| pop.nwaila.co.za. | 3600 | A | 129.232.194.138 |
| imap.nwaila.co.za. | 3600 | A | 129.232.194.138 |
| smtp.nwaila.co.za. | 3600 | A | 129.232.194.138 |

`ftp`, `webmail`, `cpanel`, `whm`, `webdisk`, `cpcalendars` and `cpcontacts` are already A
records on that IP. Leave them alone.

### 2. Delete the old apex record

`nwaila.co.za.` A `129.232.194.138`

### 3. Add GitHub's four apex records

| Name | TTL | Type | Record |
|---|---|---|---|
| nwaila.co.za. | 3600 | A | 185.199.108.153 |
| nwaila.co.za. | 3600 | A | 185.199.109.153 |
| nwaila.co.za. | 3600 | A | 185.199.110.153 |
| nwaila.co.za. | 3600 | A | 185.199.111.153 |

All four. GitHub uses them for redundancy.

### 4. Repoint www

Currently `www.nwaila.co.za. CNAME nwaila.co.za`. Change the value to:

| Name | TTL | Type | Record |
|---|---|---|---|
| www.nwaila.co.za. | 3600 | CNAME | shumaya-app.github.io. |

A CNAME to the apex will serve the site, but the HTTPS certificate for www will not issue.

### 5. Verify the domain with GitHub

GitHub - your profile - **Settings - Pages - Add a domain**. It shows a value to paste:

| Name | TTL | Type | Record |
|---|---|---|---|
| _github-pages-challenge-shumaya-app | 3600 | TXT | (the string GitHub shows you) |

This stops anyone else claiming nwaila.co.za on GitHub Pages later.

### 6. Do not touch

The **MX** record (`nwaila-co-za.mail.protection.outlook.com`), the **SPF** TXT, `_dmarc`,
`default._domainkey`, the two `hs1/hs2._domainkey` HubSpot CNAMEs, and `autodiscover`.
Those are your email. If the host offers to reset the zone to defaults, say no.

### 7. Safe to delete

`discovery.nwaila.co.za CNAME cname.tally.so` - that was the Tally form, now replaced by Calendly.
Keep `newsletter.nwaila.co.za` and `portal.nwaila.co.za`.

### 8. Tidy the SPF afterwards

Current value:

```
v=spf1 +mx +a +ip4:129.232.194.138 +include:spf.protection.outlook.com -all
```

`+a` authorises whatever the apex resolves to, which will be GitHub. Harmless but sloppy.
Once the site is live, and if 129.232.194.138 no longer sends mail, reduce it to:

```
v=spf1 +mx +include:spf.protection.outlook.com -all
```

### 9. Then enforce HTTPS

DNS takes 15 minutes to a few hours. Once it resolves, go to **Settings - Pages** and tick
**Enforce HTTPS**. Greyed out means the certificate is still issuing; check again in an hour.

### Checking

```
dig nwaila.co.za +short        # the four 185.199.x.153 addresses
dig www.nwaila.co.za +short    # shumaya-app.github.io
```

Or paste the domain into dnschecker.org.

### If something breaks

- **404 on the custom domain** - `CNAME` missing from the repo root, or Pages on the wrong branch.
- **"Domain does not resolve to the GitHub Pages server"** - DNS not propagated, or an old A record remains.
- **Site loads unstyled** - `.nojekyll` is missing.
- **Certificate warning** - remove the custom domain in Pages settings, save, add it back.

## Search and social, once live

- **Google Search Console** - add `https://nwaila.co.za/`, verify by TXT, submit `sitemap.xml`.
- **Bing Webmaster Tools** - imports from Search Console in one click.
- **Google Business Profile** - worth claiming for the Bryanston address; it feeds local search.
- **LinkedIn Post Inspector** and **Facebook Sharing Debugger** - run the URL through each once.
  They cache hard, so re-scrape whenever `og-image.png` changes.

## Notes

- The report link (`?r=…`) encodes answers in the address itself. Nothing is stored anywhere  - 
  no database, no cookies, which keeps POPIA simple. `privacy.html` says so plainly.
- GA4 does set analytics cookies. If you'd rather stay cookie-free, Plausible or Fathom are
  drop-in replacements for that one script block.

## Analytics

GA4 property **451838042**, measurement ID **G-H0ZGCDV538**. Event names are plain, no prefix.

### What gets sent

| Event | When | Useful parameters |
|---|---|---|
| `view_home` … `view_about` | Each screen | `journey` |
| `health_check_start` | Begin pressed | `journey` |
| `question_answered` | Each of the 18, once each | `question`, `pillar`, `journey` |
| `health_check_complete` | Last question | six `score_*`, `overall_score`, `score_band`, `weakest`, `answers_code`, `report_link`, `journey` |
| `health_check_abandoned` | Two minutes idle mid-check | `answered`, `stopped_at`, `stopped_pillar`, `answers_code`, `report_link` |
| `report_open` | Full report opened | `overall_score`, `score_band` |
| `report_pdf` | PDF downloaded | `overall_score`, `journey` |
| `discovery_booked` | Booking button | `overall_score`, `score_band`, `from_health_check`, `journey` |
| `email_click` | Any mailto link | `where` |
| `retake_check` | Retake pressed | - |
| `tour_step` | Each platform tour step seen | `step`, `label`, `source` (click or autoplay) |
| `scroll_depth` | 25 / 50 / 75 / 100% on SME, enterprise, platform | `page`, `percent` |
| `consent_choice` | Banner answered | `choice` |

`journey` records which fork they came through - `sme`, `enterprise` or `direct` - and rides on
every later event, so a booking can be attributed to the path that produced it.

`report_link` is a clickable link to that person's own report, appendix included, pinned to
https://nwaila.co.za/ so it works from any GA4 report. About 49 characters, well inside GA4's
100-character parameter limit.

### Set up in GA4 (once)

**Admin - Events - Mark as key event** for these five:

- `discovery_booked`
- `health_check_complete`
- `report_pdf`
- `email_click`
- `report_open`

**Admin - Custom definitions - Create custom dimension** for each parameter you want to filter or
group by. Without this GA4 collects them but won't show them in reports:

| Dimension name | Scope | Event parameter |
|---|---|---|
| Journey | Event | `journey` |
| Score band | Event | `score_band` |
| Weakest pillar | Event | `weakest` |
| Report link | Event | `report_link` |
| Answers code | Event | `answers_code` |
| Question number | Event | `question` |
| Pillar | Event | `pillar` |
| Stopped at | Event | `stopped_at` |
| Tour step | Event | `label` |
| Scroll page | Event | `page` |

Data takes up to 24 hours to appear in standard reports. **Reports - Realtime** shows events
immediately, which is the fastest way to confirm it works.

### Reading a client's answers

`health_check_complete` carries `report_link`. Click it and you get that person's full report
with the appendix - every question and the answer they chose.

Or paste the code into **nwaila.co.za/decode.html**, which accepts a bare 18-character code or a
whole URL. That page is `noindex` and disallowed in robots.txt, so it stays out of search.

### Consent

Measurement starts on the first page view. Accepting the banner allows the normal analytics
cookie; declining switches to cookieless measurement - the visit is still counted, nothing is
stored on the device. Advertising signals are denied in both cases.

### Excluding your own visits

Two ways, same switch:

- Visit **nwaila.co.za/?nwstaff=1** on any device. Nothing is sent from that browser again.
  **?nwstaff=0** turns it back on.
- Or use the toggle at the bottom of **decode.html**, which shows Counted or Excluded.

The setting lives in that browser's local storage, so do it once per device you test from.

### Turning measurement off entirely

In `index.html`, find `var GA4_ID = "G-H0ZGCDV538";` near the top and set it to `""`. Nothing
loads, nothing is sent, and the site behaves normally. The banner still appears; remove
`askConsent` if you want it gone too.

## PDF download

The report's **Download PDF** button writes a real file - no browser print sheet. It loads
html2canvas and jsPDF from cdnjs on first use, captures the report at a fixed 718px width with the
print-compact styling, and slices the image at card boundaries so a page never cuts a card in half.
Output is A4, two pages, about 450 KB, named `nwaila-business-health-check.pdf`.

If those scripts cannot load (offline, or a network that blocks cdnjs) the button falls back to the
browser print dialog, where the destination is "Save as PDF". Nothing to configure either way.
