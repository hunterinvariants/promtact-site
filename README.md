# promtact.com

Static pages: what Promtact is, how it is secured, what it does with data. No
build step, no JavaScript, nothing fetched from another host — the content
security policy in `_headers` forbids scripts entirely, which is easy to promise
when there are none.

```
index.html          the overview
security/           reporting, scope, how a release is built
privacy/            the website and email; the software sends nothing back
terms/              Apache 2.0 for the program, Swiss law for the site
cookies/            there are none
404.html
_headers            CSP, HSTS, and the rest
.well-known/        security.txt, per RFC 9116
style.css           the whole design
```

## Deploying

The Pages project is **direct upload**, not connected to this repository. GitHub
holds the history; Cloudflare holds what is live. Pushing does not publish, and
deploying does not commit — both steps are needed.

```bash
git add -A && git commit -m "..." && git push origin main
```

```bash
npx wrangler pages deploy . --project-name promtact-site
```

Wrangler reads the account and project from `.wrangler/cache/`, which is
untracked, so a fresh clone runs `npx wrangler login` first. It infers the
deployment branch from git: on `main` that is a production deploy and
`promtact.com` changes within seconds.

`.assetsignore` keeps this file and the leftover `CNAME` out of the published
site. `_headers` is consumed by Pages rather than served.

`promtact.com` and `www.promtact.com` are attached in the Pages project. Email
for `contact@promtact.com` runs on Spacemail through separate MX, SPF, DKIM and
DMARC records and is untouched by a deploy.

## Working on it locally

```bash
python -m http.server 8788
```

The absolute paths in the pages (`/style.css`, `/security/`) need a server root,
so opening the files directly will render them unstyled.

## Keeping it honest

`/security/` mirrors `SECURITY.md` in the main repository, and the measured
figures on the front page come from `EVIDENCE.md`. When either changes, change
the page and move the review date at the top of it.
