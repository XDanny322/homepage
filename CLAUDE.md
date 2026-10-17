# CLAUDE.md

Guidance for Claude Code when working in this repo.

## What this repo is

The source for Danny Lai's **personal one-page website**, published at https://xdanny322.com. It is a Hugo site, hosted on GitHub Pages, with Cloudflare in front for DNS and HTTPS.

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
| `content/_index.md` | The introduction text shown on the home page                                              |
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
- **Subfolder hosting.** Before the custom domain is active, GitHub serves the site from `https://xdanny322.github.io/homepage/`. The workflow passes `--baseURL` from GitHub's Pages setup step, so `hugo.toml` should not need changing. I built the site with that subfolder `baseURL` locally and the links and photo path came out right, but the workflow itself has not been run yet.

## Working rules for this repo

- Ask before making changes, and show the exact command and its output when diagnosing something.
- **Never run `git commit` or `git push` without Danny's explicit approval.** Commit messages use the conventional commit format.
- Keep things simple. Prefer plain HTML and plain configuration over clever abstractions, and comment on *why*, not just what.
- Verify a change by building (`hugo --gc --minify`) and checking the generated files in `public/` before calling it done.
