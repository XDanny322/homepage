# CLAUDE.md

Guidance for Claude Code when working in this repo.

## What this repo is

The source for Danny Lai's **personal one-page website**, published at https://xdanny322.com. It is a Hugo site, hosted on GitHub Pages. Cloudflare only holds the domain's DNS records (the grey-cloud "DNS only" setting). GitHub itself serves the site and issues the HTTPS certificate.

This is a **personal, public** repo on github.com (`XDanny322/homepage`). It is not a work repo. Do not add anything from work, and do not add private details (phone number, home address, internal links). Anything committed here is visible to everyone, including the photo.

The page is intentionally tiny: a title, a round photo, a short introduction, and four links (Resume, Facebook, LinkedIn, GitHub). There is no About page, no blog, and no footer. Keep it that way unless Danny asks otherwise.

A step-by-step learning guide for how this site was built lives in a different repo: `/Users/hlai/_git/hlai/standalone_hugo/readme.md`. Read it for background. Do not copy that guide into this repo.

## How the site is built

- **Hugo** (extended build, 0.146.0 or newer; the workflow pins `HUGO_VERSION` in `.github/workflows/hugo.yaml`, keep it equal to the local `hugo version`).
- **Theme**: [Hermit-V2](https://github.com/1bl4z3r/hermit-V2), a git submodule at `themes/hermit-v2`, tracking its `main` branch.
- **Deploy**: a push to `main` runs the GitHub Actions workflow, which builds with Hugo and publishes to GitHub Pages. `public/` is generated and ignored by git.

## Commands

```bash
hugo server                  # live preview at http://localhost:1313
hugo --gc --minify           # one-off build into public/
git submodule update --remote --merge themes/hermit-v2   # update the theme, then commit the new pointer
git clone --recurse-submodules <url>                      # a plain clone leaves themes/hermit-v2 empty
```

## Where things live

| Path                | Purpose                                                                                   |
| ------------------- | ----------------------------------------------------------------------------------------- |
| `hugo.toml`         | Site title, `baseURL`, theme name, and the link list (`[[menus.main]]`, ordered by `weight`) |
| `content/_index.md` | The introduction text shown on the home page, and `title`, which is the browser tab text ("Danny Lai"). The big heading on the page comes from `title` in `hugo.toml` instead |
| `layouts/home.html` | **Overrides the theme's home layout.** Adds the photo and text, and removes the footer     |
| `assets/images/photo.jpg` | The photo. Hugo resizes it at build time (kept in `assets/`, not `static/`)         |
| `static/`           | Files copied to the site unchanged: the icon files, and the resume at `static/resume.pdf` |
| `themes/hermit-v2/` | The theme submodule. **Never edit files inside it**                                       |

## Things that are easy to get wrong

- **The footer is removed by `layouts/home.html`, not by a setting.** The theme's `footerHideThemeName` setting only hides the "Made with Hugo" credit. If someone adds another page type later, the theme's own layouts for it will print the footer again. To remove it there, copy that layout from `themes/hermit-v2/layouts/` into the repo's `layouts/` at the same path and delete the footer lines.
- **Do not edit `themes/hermit-v2/`.** To change theme behavior, copy the file into `layouts/` (or `assets/`) at the same path and edit the copy.
- **The photo file name matters.** `layouts/home.html` loads `images/photo.jpg` from `assets/`. Replacing the photo means keeping that name, or changing the layout.
- **The Resume link points at `/resume.pdf`.** It returns 404 until `static/resume.pdf` exists.
- **The Facebook and LinkedIn addresses in `hugo.toml` are placeholders** (they point at each site's front page). The GitHub link points at `github.com/XDanny322`. Danny will update them.
- **External links must be full addresses** (`https://...`). A link starting with `/` is treated as a path on this site.
- **Hugo silently skips drafts.** A page with `draft = true` (or `draft: true`) is not built, with no warning. `content/_index.md` has no `draft` line, so it is published.
- **`disableKinds` in `hugo.toml` is deliberate.** Without it Hugo also builds empty `/tags/` and `/categories/` pages.
- **Expected build warnings (2):** (1) the theme's deprecated stylesheet compiler (libsass). It is fixed in the theme, not here: update the submodule once the theme moves to Dart Sass. (2) `favicon.svg` was not found. This one is deliberate: Danny wants his face in the browser tab everywhere, and browsers that support SVG icons would prefer an SVG "DL" circle over the photo-based PNG and ICO files. Do not add a `favicon.svg`. The site builds and works.
- **The theme looks for 7 icon files by exact name in `static/`** (`favicon.ico`, `favicon-32x32.png`, `favicon-16x16.png`, `apple-touch-icon.png`, `favicon.svg`, `icon-monochrome.svg`, `site.webmanifest`) and warns for each one that is missing. The four PNG and ICO files are cropped from the photo. `icon-monochrome.svg` is a plain "DL" circle, because a photo does not convert to a vector image cleanly (Safari's pinned-tab icon only uses the shape). `favicon.svg` is intentionally absent.
- **The workflow takes the site address from GitHub, not from `hugo.toml`.** The `--baseURL` flag in `.github/workflows/hugo.yaml` is filled in by GitHub's Pages setup step with whatever address the Pages settings report at build time. So after changing the custom domain or the "Enforce HTTPS" box in the Pages settings, **re-run the workflow** (Actions tab, "Run workflow", or `GH_HOST=github.com gh workflow run hugo.yaml --repo XDanny322/homepage`). Otherwise the live page keeps the old addresses baked in. This happened once: the page was built before the domain was added, so it asked for its stylesheet at `https://xdanny322.github.io/homepage/...`, which redirects to insecure `http://`, and Chrome blocked it (unstyled page, photo 404).
- **`https://www.xdanny322.com` redirects to `https://xdanny322.com` on purpose.** The custom domain in the Pages settings is the name without `www`, so GitHub redirects `www` to it. This is expected, not a fault.
- **The install-Hugo step in the workflow needs `mkdir -p`.** The GitHub build machine has no `~/.local` folder, so plain `mkdir` failed on the first run.
- **The old address `https://xdanny322.github.io/homepage/` now redirects to `https://xdanny322.com/`.** That is GitHub's normal behavior once a custom domain is set.

## How the site was stood up (one-time setup, already done)

Kept here as a record, and as a checklist if the site ever has to be rebuilt from scratch. Steps 2 to 5 are manual work in the GitHub and Cloudflare web pages, because nothing in this repo can do them.

1. **Commit email.** The repo is public and personal, so it uses the personal email, set for this repo only: `git config user.email <personal email>`. The global git setting is the work address and must not be used here.
2. **Turn on Pages.** GitHub, repo `XDanny322/homepage`, Settings, Pages, Build and deployment, Source = **GitHub Actions**. Do this before the first push, or the workflow's "Setup Pages" step fails.
3. **Verify the domain (recommended by GitHub).** Account Settings, Pages, "Add a domain", enter `xdanny322.com`. GitHub shows a TXT record; add it in Cloudflare (DNS, Records, Add record, type TXT), then click Verify. This stops other GitHub users from claiming the domain. Docs: https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site
4. **Set the custom domain on the repo.** Settings, Pages, Custom domain = `xdanny322.com`, Save. With the "GitHub Actions" source there is **no `CNAME` file** in the repo; this setting is the only place the domain lives.
5. **DNS records in Cloudflare** (DNS, Records), all set to **DNS only** (grey cloud) so GitHub can reach the domain to issue its certificate:

   | Type  | Name  | Content               |
   | ----- | ----- | --------------------- |
   | A     | `@`   | `185.199.108.153`     |
   | A     | `@`   | `185.199.109.153`     |
   | A     | `@`   | `185.199.110.153`     |
   | A     | `@`   | `185.199.111.153`     |
   | CNAME | `www` | `xdanny322.github.io` |

   The CNAME target leaves the repo name off, as GitHub's docs say. The optional IPv6 (AAAA) records were skipped to keep it simple.
6. **Wait for the certificate, then tick "Enforce HTTPS"** in the same Pages settings page. GitHub issues a free Let's Encrypt certificate that covers both `xdanny322.com` and `www.xdanny322.com`. As of 2026-10-18 it was valid until 2027-01-16, and GitHub renews it.
7. **Re-run the workflow** (see the "site address" note above) so the page is built with the final `https://xdanny322.com/` address.

Checks that confirm it works:

```bash
dig +short A xdanny322.com                       # four 185.199.x.153 addresses
curl -sI https://xdanny322.com | head -3         # HTTP/2 200
curl -sI https://www.xdanny322.com | head -3     # HTTP/2 301, location: https://xdanny322.com/
curl -s https://xdanny322.com/ | grep -oE '(href|src)=[^ >]+' | sort -u   # no "/homepage/" and no "http://" addresses
```

## Rules for adding more things on the domain later

- **GitHub Pages is not allowed for online shops or payments.** GitHub's usage rules say Pages is not a free host for an e-commerce site or online business, not for sites mainly aimed at commercial transactions or commercial software as a service, and that sites must not handle sensitive transactions like passwords or credit card numbers. Docs: https://docs.github.com/en/pages/getting-started-with-github-pages/github-pages-limits . Other limits: published site 1 GB, soft bandwidth limit 100 GB per month. Pages also only serves static files (no server-side code).
- **Another subdomain on another host** (for example `app1.xdanny322.com`): this is a separate DNS record in Cloudflare pointing at that host. It does not touch this site. GitHub's docs say nothing about it; it is standard DNS.
- **Another subdomain on another GitHub repo:** GitHub's docs say each repo can set its own custom domain, and a subdomain uses a CNAME record. That would be a `CNAME` record named `app1` pointing at `xdanny322.github.io`, plus setting the Custom domain in that other repo's Pages settings.
- **Takeover warning from GitHub's docs:** if a Pages site is disabled but its custom domain and DNS records are still in place, someone else could host a site on that name. Delete the DNS record when retiring a subdomain, and do not use wildcard (`*`) DNS records.

## Working rules for this repo

- Ask before making changes, and show the exact command and its output when diagnosing something.
- **Never run `git commit` or `git push` without Danny's explicit approval.** Commit messages use the conventional commit format.
- Keep things simple. Prefer plain HTML and plain configuration over clever abstractions, and comment on *why*, not just what.
- Verify a change by building (`hugo --gc --minify`) and checking the generated files in `public/` before calling it done.
