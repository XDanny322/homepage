# homepage

Source for my personal one-page website, [xdanny322.com](https://xdanny322.com).

The page shows my name, a photo, a short introduction, and links to my resume, Facebook, LinkedIn, and GitHub.

## How it works

- Built with [Hugo](https://gohugo.io/) using the [Hermit-V2](https://github.com/1bl4z3r/hermit-V2) theme (a git submodule in `themes/hermit-v2`).
- Hosted on GitHub Pages. A push to `main` runs [`.github/workflows/hugo.yaml`](.github/workflows/hugo.yaml), which builds and deploys the site.
- Cloudflare sits in front for DNS and HTTPS.

## Where things are

| Path                  | What it is                                                         |
| --------------------- | ------------------------------------------------------------------ |
| `content/_index.md`   | The introduction text                                              |
| `hugo.toml`           | Site title, address, and the list of links                         |
| `layouts/home.html`   | The home page layout (photo, text, links, no footer)               |
| `assets/images/`      | The photo                                                          |
| `static/`             | Files published as-is, such as `resume.pdf`                        |
| `themes/hermit-v2/`   | The theme                                                          |

## Run it locally

```bash
git clone --recurse-submodules git@github.com:XDanny322/homepage.git
cd homepage
hugo server
```

Then open <http://localhost:1313>. The theme needs the extended build of Hugo, version 0.146.0 or newer.

## To do

- Add `static/resume.pdf` (the Resume link returns "not found" until then).
