# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

Jekyll 4.4 static site for the Living Fluids Group (Brato Chakrabarti, ICTS). The README says it was adapted from the Allan Lab (Munich) template. There are no gem plugins (no jekyll-scholar, no collections). Content is YAML in `_data/` that hand-written Liquid in `_pages/` renders.

## Build and deploy

```bash
bundle install
bundle exec jekyll serve     # http://localhost:4000, live-reloads (restart after _config.yml edits)
bundle exec jekyll build     # same build CI runs; output to _site/ (gitignored)
```

- These commands were checked locally with Ruby 3.4.1 and Bundler 2.6.8. Both serve and build succeed and every nav page returns 200.
- Expect a large number of Sass deprecation warnings from the vendored Bootstrap 3 SCSS. They are pre-existing and harmless. Treat a build as broken only if Liquid or YAML errors appear.
- There are no tests or linters. To verify a change, run `bundle exec jekyll build` and look at the page in `serve`.
- Deploy: pushing to `main` runs `.github/workflows/jekyll.yml` (Ruby 3.4.1, `bundle exec jekyll build`, upload `_site`, `actions/deploy-pages`). The custom domain is in `CNAME`. Anything committed is live after the push.

## Layouts, includes, pages

- `_layouts/default.html` is the only full HTML shell: `head.html` + `header.html` + scroll progress bar + `container-fluid` + `footer.html`. Every other layout extends `default` and only wraps `{{ content }}` in a `<div id=... class="col-sm-12">`. Pages use `gridlay`. `homelay` (home only) adds a `col-sm-8` main column and a `col-sm-4` sidebar with `news.html` and `gallery.html`. `page` is used by `allnews.html` and `pdf_viewer.html`. `piclay`, `post`, `publications`, `research`, `team`, `textlay` are unused template leftovers.
- Pages live in `_pages/` (pulled in through `include: [_pages]` in `_config.yml`). Front-matter convention:
  ```yaml
  ---
  title: "Team"
  layout: gridlay
  sitemap: false
  permalink: /team/
  ---
  ```
  Permalinks end in `/` except `/allnews.html`, `/pdf_viewer.html`, `/404.html`. Nav links are hard-coded in `_includes/header.html` and marked active with `page.url contains '/<name>'`, so a new top-level page needs a nav entry there.
- Styling lives mostly in per-page `<style>` blocks and inline `style=` attributes. Global CSS is `css/main.scss` → `_sass/_bootstrap.scss` (Bootstrap 3 SCSS). `head.html` also loads Bootstrap 5.3 CSS/JS from jsDelivr, and `footer.html` loads jQuery 1.11 + the local Bootstrap 3 JS, so both versions are present. Collapse toggles use Bootstrap 5 `data-bs-*` attributes.
- kramdown runs with `parse_block_html: true`, so markdown renders inside HTML blocks (`markdown="0"` turns it off). `_plugins/markdown.rb` adds `{% markdown file %}` for `_includes/` files. It runs because CI builds with Actions, not the classic Pages builder.

## Data files

| File | Rendered by | Notes |
|---|---|---|
| `_data/publist.yml` | `_pages/publications.md` | see below |
| `_data/news.yml` | `_includes/news.html` (newest 5), `_pages/allnews.html` (all) | **file order = display order**, add at top. Fields: `date` (`July 2026`), `text` (markdown), `image` (in `images/news/`), optional `anchor`, `allnews_text`, `allnews_image` (shown only on allnews). Link to an item with `/allnews.html#<anchor>`. |
| `_data/pi.yml`, `phd_students.yml`, `visiting.yml` | `_pages/team.md` | photos in `images/teampic/`. Student fields: `name`, `photo`, `info`, `advisor` (e.g. `(Co-advised by Prof. X)` or empty), `email` (`user[at]icts.res.in`), `website`. Visiting: `name` only, newest first. |
| `_data/gallery.yml` | `_includes/gallery.html` | `image:` filenames in `images/gallery/` |

### Publication entry (`_data/publist.yml`)

Entries are kept newest-first in the file. The page sorts by `date` (descending), groups by `year` (with `In Review` first, then years descending), and numbers the papers counting down in that display order. The rules for *what* to list and where the data comes from are in "Updating publications" below. Exact format:

Published paper (abstract shortened):

```yaml
- title: "Hydrodynamic instabilities in driven chiral suspensions"
  authors: "Chahal, Seema and Chakrabarti, Brato"
  year: 2026
  date: "2026-02-26"
  display: "Physical Review E Letters"
  doi: "10.1103/9kfb-c51g"
  thumbnail: "/images/papers/17.png"
  pdf: "/papers/17.pdf"
  abstract: "Active Stokesian suspensions are conventionally understood to ..."
```

Under review:

```yaml
- title: "Growing, Buckling, and Swirling: motility from polymerization"
  authors: "Kumar D, Naveen and Shelley, Michael J and Chakrabarti, Brato"
  year: In Review
  date: "2026-07-11"
  display: "ArXiv"
  arxiv: "2607.10446"
  thumbnail: "/images/papers/21.png"
  abstract: "..."
```

- `authors`: `Last, First` joined by ` and ` (BibTeX style). The page converts it to `First Last, …, and First Last`. A name with no comma is printed as is. Nobody is bolded on the site.
- `year`: an integer, or the string `In Review` for preprints. `date` (`"YYYY-MM-DD"`, quoted) controls the order within a group.
- `display`: shown in italics followed by `, {{year}}`. For a published paper it's the journal name only. Volume and pages are deliberately left off the website and go in the CV. For a preprint it's `"ArXiv"`.
- `doi`: the bare DOI (`"10.1103/84g7-fmcv"`), because the page builds `http://dx.doi.org/{{doi}}`. Only published papers have one. Preprints use `arxiv: "<id>"`, which renders an ARXIV button linking to `arxiv.org/abs/<id>`. Published papers don't carry `arxiv`. Remove it when a preprint is published.
- `thumbnail`: `/images/papers/NN.png`. `pdf`: `/papers/NN.pdf`, for published papers only. See "Updating publications" for how to choose NN.
- Optional fields: `highlight` (markdown, shown with a ★; set `highlight_star: false` to drop the star) and `media_coverage` (a site path such as `/allnews.html#<anchor>`, which renders a "See the media coverage!" link).
- The ABSTRACT toggle's HTML id is `abs-` plus the slugified `doi`, or the `arxiv` id when there's no DOI. Every entry with an abstract needs one of the two.
- The PhD thesis block below the loop is hard-coded HTML in `publications.md`.

## CV

`cv/CV_website.tex` is a hand-maintained LaTeX file (class `cv/res.cls`) and is **not** generated from `_data/`. When you add a paper, update `publist.yml` and the CV separately. Build it with pdfTeX (TeX Live 2024 here):

```bash
cd cv && latexmk -pdf CV_website.tex
```

`cv/CV_website.pdf` is linked from `_data/pi.yml` (`cv: /cv/CV_website.pdf`) and must be committed for the site to serve it. The latexmk auxiliary files (`.aux`, `.log`, `.fls`, `.fdb_latexmk`, `.synctex.gz`, `.dvi`) are committed too, so every compile changes them.

Sections are `\section{\color{Mahogany}{\large{TITLE}}}` followed by `\hrule\hrule` or `\noindent\rule{\textwidth}{0.6pt}` with `\vspace*` tweaks. Most lists are `enumerate`, newest first. A publication entry looks like this:

```latex
\item  Seema Chahal and \textbf{Brato Chakrabarti} ``Hydrodynamic instabilities in driven chiral suspensions", \href{https://doi.org/10.1103/9kfb-c51g}{\textit{Physical Review E Letters}}, \textbf{113} (2), L023101 (2026). 
```

- Authors are written `First Last` in the paper's order, with **`\textbf{Brato Chakrabarti}`** bolded and `$^*$` marking equal contribution. The title is in ``` ``...'' ``` quotes. `\href{<full doi url>}{\textit{Journal}}` is followed by `\textbf{volume}`, the page or article number and `(year)`.
- Preprints: `\href{https://arxiv.org/abs/XXXX.XXXXX}{arXiv:XXXX.XXXXX} (year)`.
- The CV's "Visiting Students" list leaves out ICTS rotation students on purpose, even though `_data/visiting.yml` lists them. Don't sync it.
- Conference talks put the presenter in `\underline{...}` and the talk title in `\textit{...}` or `` `...' ``. Invited talks are `` \item `Title', Venue, Place, Month, Year. `` with the newest first.

## Not part of the live site design

- `livingfluids/` is a default `jekyll new` scaffold, and `test` is a leftover snippet from the original template (LMU, quantum materials). Both are listed under `exclude:` in `_config.yml`, along with `README.md` and `CLAUDE.md`. Add any new repo-only file there too, or Jekyll will publish it.
- `downloads/` holds the template's PDFs (still published).
- `_sass/bootstrap/_variables copy.scss` is a duplicate of `_variables.scss`.

Leave these alone unless asked.

## Updating publications (page + CV)

These are Brato's rules. Follow them for any change to `_data/publist.yml`, `papers/`, `images/papers/` or the CV publication list.

**Workflow.** Show the proposed changes before editing anything: the YAML entries, the CV `\item`, the news item, and the files to be added or downloaded. Wait for approval. Each time, also check that every journal paper published since January 2026 has a matching news item. Earlier papers don't need one.

**Sources**
- Published papers: confirm each DOI against ORCID through the public API, not the web page (it's rendered by JavaScript):
  ```bash
  curl -s -H 'Accept: application/json' https://pub.orcid.org/v3.0/0000-0001-6993-0589/works
  ```
  A DOI starting with `10.48550/arXiv` (arXiv) or `10.1101/` (bioRxiv) is a preprint, not a journal paper. Ignore "Author Correction" works.
- Under-review papers: the arXiv author feed, `https://arxiv.org/a/0000-0001-6993-0589.atom` (readable version: `.html`).
- Match papers across sources by DOI or arXiv ID first, then by title. Titles often change between preprint and publication (e.g. arXiv "Self-organized flows in phase-synchronizing active fluids" became the PRL "Collective motion and pattern formation in phase-synchronizing active fluids"). If a match is uncertain, ask.
- If ORCID and the site disagree on a DOI, flag it. Don't overwrite it silently.
- Known gaps (October 2026):
  - ORCID is missing 7 published papers: PRL 2023, Soft Matter 2020, PRL 2019, both PRFluids 2019, PNAS 2018 and JFS 2016. Verify those with Crossref (`https://api.crossref.org/works/<doi>`) and say that ORCID didn't confirm them.
  - ORCID's bioRxiv preprint "Breathe in, breathe out: Bacterial density determines collective…" (10.1101/2025.04.02.646741) is the preprint of the PRR aerotaxis paper (confirmed by Brato). It needs no separate entry.
  - The arXiv feed only lists papers claimed by Brato's arXiv account, and it lacks 2607.17717 ("Emergent odd response…"). Don't drop an under-review item just because the feed doesn't list it; flag it.

**Listing**
- Under review: authors, title, and a link to the arXiv abstract page (via `arxiv:`).
- Published: the journal name in `display`, the DOI link, and a link to the local PDF. The full citation, with volume and pages from Crossref, goes in the CV only.
- When a preprint is published, update its entry in place: change `year`, `date`, `display` and `doi`, add `pdf`, remove `arxiv`, and keep the thumbnail. Never leave a duplicate.
- Every journal publication gets a news item at the top of `_data/news.yml`, matching the existing wording:
  ```yaml
  - date: <Month YYYY>
    text: Our paper "<title>" is now published in <Journal>!
    image: 
  ``` Make the same change to the CV item (`\href{https://doi.org/<doi>}{\textit{Journal}}, \textbf{vol}, article (year)`).

**PDFs**
- Download the published version into `papers/` as `NN.pdf` (two digits). Use the paper's number on the page if that file name is free, otherwise the next unused number.
- Check the title page (`pdftotext -l 1 papers/NN.pdf - | head`) matches the paper before linking it.
- If the download is blocked or paywalled, stop and ask Brato to place the file. Never substitute the arXiv version without saying so.

**Thumbnails**
- Every new item gets `images/papers/NN.png`, where NN is its number on the page.
- The existing thumbnails are a cropped figure panel from the paper (a schematic or simulation snapshot with no axis labels, text or caption), saved at native resolution. Widths range from 192 to 3545 px with no fixed size, since the CSS scales them to at most 150×140. Most are PNG; three older ones are JPEG.
- Propose which figure and crop to use and show it to Brato before saving.

**Numbering**
- Paper numbers are positional: the page counts down in display order (In Review first, then years descending, by date within a group). Adding a preprint, or publishing one out of submission order, can shift numbers.
- After any change, build and check every item against its image file:
  ```bash
  bundle exec jekyll build && grep -o 'images/papers/[0-9]*\|paper-title">[0-9]*' _site/publications/index.html | paste - - \
    | awk '{split($1,a,"/"); split($2,b,">"); if (a[3]+0 != b[2]+0) print "MISMATCH image", a[3], "paper", b[2]}'
  ```
  No output means every image matches. Report mismatches, and don't renumber existing items or images without asking.

**Known inconsistency, left as is:** `images/papers/04.jpeg` is unused.
