# Update — re-upload these

The site is live but three things were missing. All fixed here.

## 1. Wire the form (do this before posting anywhere)

`FORM_ENDPOINT` is still empty in `openhouse.html` and `events.html`. With it empty the
form falls back to opening an email draft — and inside the Facebook in-app browser on a
phone, that frequently does nothing at all. Since most of your traffic will arrive from
Facebook on a phone, a large share of responses would simply vanish.

Find this line near the bottom of each file:

```js
const FORM_ENDPOINT = "";
```

Paste a Formspree / Basin / Getform endpoint. Two minutes, free tier is plenty.

**Then test it from your phone, from a Facebook link, not from your laptop.**

## 2. Social preview images — new

`og-home.png`, `og-openhouse.png`, `og-events.png` (1200×630) plus the meta tags.
Without these a pasted link in a Facebook group renders as a bare blue URL, which in a
group already suspicious of vendors reads as low-effort and gets scrolled past.

After deploying, clear Facebook's cache or it will keep serving the old empty preview:
**developers.facebook.com/tools/debug** → paste the URL → *Scrape Again*. Do this for all
three URLs before your first post.

## 3. Favicon and app icons — new

`favicon.svg`, `favicon-32.png`, `apple-touch-icon.png`, `icon-512.png`. The tab was
showing a blank page icon.

## Also changed

The nav mark is now the new **S** — two interlocking hooks that read as an S and a chain
link. Matches `brand/logo/`.

## Files to upload

```
index.html  openhouse.html  events.html
og-home.png  og-openhouse.png  og-events.png
favicon.svg  favicon-32.png  apple-touch-icon.png  icon-512.png
```

The three OG and four icon files must sit at the **site root**, since the tags reference
`/og-home.png`, `/favicon.svg` and so on.

---

## On the form endpoint being visible in the page source

**It is public, it cannot be hidden, and that is how every form service works.** The browser
has to know where to POST, so any URL it calls is visible in view-source or the network tab.
Obfuscating it — base64, string splitting, a variable name that lies — is defeated by opening
devtools, and buys nothing.

**What matters is that it is not a secret.** A Formspree/Basin endpoint is **write-only**.
Knowing it lets someone send you data. It does not let them read submissions, see anyone
else's answers, or touch your account. It is the same class of thing as a public email
address: anyone can send to `hello@lynkst.com`, and that is fine.

**The real risk is not exposure — it is scraper spam**, which burns your free-tier quota
(Formspree free is ~50 submissions/month) and buries real responses.

### What is already in the page

- **Honeypot** — a `company_url` field positioned off-screen with `aria-hidden` and
  `tabindex="-1"`. Humans and screen readers never encounter it; naive bots fill it.
- **Time trap** — submissions under 3 seconds from page load are treated as automated.

Both **fail silently** — the bot sees the success screen and nothing is sent, so it learns
nothing and does not retry with a different approach.

### What to switch on at the provider (2 minutes, do both)

1. **Domain restriction.** Formspree and Basin both let you reject submissions that do not
   originate from your domain. This is the closest thing to "hiding" it — the URL stays
   public but only accepts POSTs from lynkst.com.
2. **Built-in spam filtering / reCAPTCHA.** Free on both, and it catches what the honeypot
   does not.

### If you later want the provider URL off the page entirely

Put a serverless function at `lynkst.com/api/reaction` that forwards to the real endpoint
(Netlify Functions and Cloudflare Workers both do this in about fifteen lines). Note what
this actually achieves: the proxy URL is *still public* — it has to be — but your provider
endpoint is not, and you get rate limiting you control. Worth doing once you are getting real
volume. Not worth doing before your first Facebook post.
