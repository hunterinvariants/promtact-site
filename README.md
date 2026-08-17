# promtact.com

Static pages: what Promtact is, how it is secured, what it does with data. No
build step, no JavaScript, nothing fetched from another host — the content
security policy in `site/_headers` forbids scripts entirely, which is easy to
promise when there are none.

```
site/                    everything that gets published, and nothing else
  index.html             the overview
  security/              reporting, scope, how a release is built
  privacy/               the website and email; the software sends nothing back
  terms/                 Apache 2.0 for the program, Swiss law for the site
  cookies/               there are none
  404.html
  _headers               CSP, HSTS, and the rest
  .well-known/           security.txt, per RFC 9116
  style.css              the whole design
README.md                this file, and it stays out of the deployment
```

Pages publishes every file under the directory it is given, dotfiles included —
`.gitignore` and `.assetsignore` were both reachable over HTTPS while the deploy
root was the repository root. `site/` is the boundary, and it is the only one
Pages honours.

## Deploying

The Pages project is **direct upload**, not connected to this repository. GitHub
holds the history; Cloudflare holds what is live. Pushing does not publish, and
deploying does not commit — both steps are needed.

```bash
git add -A && git commit -m "..." && git push origin main
```

```bash
npx wrangler pages deploy site --project-name promtact-site
```

Wrangler reads the account and project from `.wrangler/cache/`, which is
untracked, so a fresh clone runs `npx wrangler login` first. It infers the
deployment branch from git: on `main` that is a production deploy and
`promtact.com` changes within seconds. The `*.pages.dev` address it prints
names that one deployment; the custom domain is what visitors see.

Each deployment replaces the whole asset manifest, so a file dropped from
`site/` stops being served after the next deploy.

`promtact.com` and `www.promtact.com` are attached in the Pages project. Email
for `contact@promtact.com` runs on Spacemail through separate MX, SPF, DKIM and
DMARC records and is untouched by a deploy.

## Working on it locally

```bash
python -m http.server 8788 --directory site
```

The pages use absolute paths (`/style.css`, `/security/`), so they need a server
root; opening the files directly renders them unstyled.

## Keeping it honest

`site/security/` mirrors `SECURITY.md` in the main repository, and the measured
figures on the front page come from `EVIDENCE.md`. When either changes, change
the page and move the review date at the top of it.
