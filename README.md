# tirfood

A personal data-science / analytics portfolio built with [Quarto](https://quarto.org/),
deployed to GitHub Pages. Posts can be written in either **Python (`.qmd`)** or
**R / Rmarkdown (`.Rmd`)** — Quarto executes the code on render and inlines
the output (tables, figures) alongside the prose.

Live site: <https://tiaradwptr.github.io/tirfood>

---

## Project layout

```
.
├── _quarto.yml              # site config (theme, navbar, footer, output dir)
├── index.qmd                # landing page — auto-lists posts/
├── about.qmd                # about page
├── styles.css               # site-wide CSS overrides
├── posts/
│   ├── _metadata.yml        # shared post settings (freeze, banner)
│   ├── hello-python/
│   │   └── index.qmd        # example Python post
│   └── hello-r/
│       └── index.Rmd        # example R post
├── requirements.txt         # Python deps used during render
├── .github/workflows/
│   └── publish.yml          # CI: render + publish to gh-pages on push
└── _site/                   # build output (gitignored)
```

## Working locally

Quarto is installed at `~/.local/quarto/bin/quarto` (symlinked to
`~/.local/bin/quarto`). With `~/.local/bin` on your `PATH`, the `quarto`
command is available globally.

**First-time setup — Python deps:**

```sh
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

**First-time setup — R deps (only if you write `.Rmd` posts):**

```r
install.packages(c("tibble", "dplyr", "ggplot2", "rmarkdown", "knitr"))
```

**Every working session — activate the venv first:**

```sh
source .venv/bin/activate    # ← required, or Quarto falls back to system python (no jupyter)
quarto preview               # live-preview the site, rebuilds on save
quarto render                # one-shot render to ./_site
```

> **Gotcha:** if you skip `source .venv/bin/activate`, Quarto will hit
> `ModuleNotFoundError: No module named 'nbformat'` because it's invoking
> `/usr/bin/python3` instead of the venv's Python. Alternatively, set
> `QUARTO_PYTHON=$(pwd)/.venv/bin/python` for a one-off command without
> activating.

## Adding a new post

1. Create a folder under `posts/` — the folder name becomes the URL slug.
2. Add an `index.qmd` (Python or plain markdown) or `index.Rmd` (R) with a
   YAML header — title, subtitle, date, categories, optional `image`.
3. Run `quarto preview` to see it live.
4. Commit and push — the GitHub Action will rebuild and publish.

## Deploying to GitHub Pages

**One-time setup:**

1. Push this repo to GitHub: `git remote add origin … && git push -u origin master`
2. On GitHub → **Settings → Pages**, set the source to **Deploy from a branch**
   and pick the `gh-pages` branch (the workflow creates this branch on its
   first successful run, so you may need to push first and then come back here).
3. Done. Every push to `main`/`master` triggers
   [.github/workflows/publish.yml](.github/workflows/publish.yml), which
   renders the site and pushes to `gh-pages`.

**Manual one-off publish from your laptop:**

```sh
quarto publish gh-pages
```

This builds locally and pushes to the `gh-pages` branch directly — handy
if CI is unavailable.

## Notes on `freeze`

`execute: freeze: auto` is enabled in `_quarto.yml`. The first time you
render a post, its outputs are cached under `_freeze/`. Subsequent renders
re-execute only the chunks you've changed. **Commit `_freeze/`** so CI
doesn't need to re-run expensive computations (model training, big SQL
queries) on every push.
