# Website architecture and principles

A playbook for anyone (human or AI) maintaining this site. Read this before changing templates.

## Stack
- **Hugo extended 0.167.0** (pinned in `.github/workflows/deploy.yml`). Hugo Blox `blox-tailwind` v0.10.0 needs ≥ 0.148.2.
- **Hugo Blox** (`blox-tailwind` v0.10.0) is imported as a Go module and **vendored** in `_vendor/` (commit it). `go.mod` and `go.sum` are committed.
- **Tailwind CSS v4** is compiled by Blox through `css.TailwindCSS`. This needs `@tailwindcss/cli`, `tailwindcss`, and `@tailwindcss/typography` from `package.json`, plus `^tailwindcss$` in `security.exec.allow`.
- **Pagefind** builds the static search index in CI, after Hugo, against `public/`.
- **GitHub Pages** site served at the custom domain `https://www.rikioinouye.org/` (repo `rinouye120/rikio-site`; custom domain set in repo Settings → Pages, DNS at Squarespace Domains), deployed by GitHub Actions on every push to `main`.

## What comes from Blox and what's ours
We use Blox for the `<head>` (`site_head`: Tailwind pipeline, SEO/OG tags, JSON-LD, `assets/css/custom.css` inclusion) and its extensibility hooks. We override these presentation templates (project files win over `_vendor/`):

| File | Purpose |
|---|---|
| `layouts/baseof.html` | Shell: skip link, navbar, `<main data-pagefind-body>`, footer, search modal, mobile-menu script |
| `layouts/_partials/components/headers/navbar.html` | Brand (owner's name, links home) left; nav right; search icon; hamburger ≤ 900px. Carries class `page-header` because Blox's `hb-nav.js` measures it |
| `layouts/_partials/components/search-modal.html` | Vanilla-JS Pagefind modal: lazy-loads `{{ "pagefind/pagefind.js" \| relURL }}`, Cmd/Ctrl-K, Esc, arrow keys, Google `site:` fallback |
| `layouts/_partials/libraries.html` | Emptied: Blox's Alpine.js and search bundle aren't needed |
| `layouts/_partials/site_footer.html` | Dark footer; "Created using GaryKing.org/mysite" credit gated by `params.mysite.credit` |
| `layouts/_partials/hooks/head-end/mysite.html` | Favicons, generator meta + homepage `Person` JSON-LD (gated by `params.mysite.discovery`) |
| `layouts/_partials/hooks/head-end/scholarly-meta.html` | Google Scholar `citation_*` tags + ScholarlyArticle/Dataset JSON-LD on writings |
| `layouts/landing/list.html` | Homepage: photo left / text right hero; research-area accordions (`id="research-areas"`, `scroll-margin-top`); also publishes redirects |
| `layouts/publication/list.html` | Dense, server-rendered Writings list; JS adds tabs, filters, sort, BibTeX export, hash state |
| `layouts/_partials/pub_single.html` | Detail page body shared by `publication/`, `talk/`, `software/` singles |
| `layouts/_partials/related_finder.html` | Automatic "See Also" (algorithm documented at the top of the file) |
| `layouts/_partials/pub_meta.html` | One place that derives tab, kind label, year label, and research areas for a page |
| `layouts/_partials/pub_bibtex.html` | One place that builds BibTeX (used by detail pages and the list export) |
| `layouts/_partials/pub_authors.html` | Author list; owner emphasized; co-authors linked from `data/coauthors.json` |
| `layouts/authors/list.html` | People page: names-only, alphabetized, linked (Presentation B: solo researcher) |
| `layouts/bio/single.html`, `contact/single.html`, `single.html`, `list.html`, `404.html` | Other pages |
| `layouts/robots.txt`, `layouts/home.json`, `static/llms.txt` | Crawler/AI visibility |
| `layouts/_shortcodes/staticrel.html` | Subpath-safe links to `static/` files from Markdown |

Paths follow Hugo ≥ 0.146 conventions: `layouts/_partials/…` and `layouts/_shortcodes/…` (not `partials/`, `shortcodes/`, or `_default/`). An override in the wrong folder is silently ignored.

## Data files (`data/`)
- `research_areas.json`: the three areas, each with subcategory tags. It drives homepage accordions, the area filter, and See Also siblings.
- `coauthors.json`: exact author name → external URL (website → university page → LinkedIn). **Auto-found by web search; the owner must review it.**
- `writings_legacy_map.json`: slug → tab (mirrors the old site's sections: Peer Reviewed Articles / Submitted Manuscripts / Research in Progress).
- `featured_publications.yaml`: shown first within each homepage research area.
- `redirects.yaml`: short/legacy URLs (`/cv/`, `/research/`, `/home/`, from the old Google Site) published as meta-refresh pages by `_partials/redirects.html`.

## Rules
1. **Subpath safety.** Never write a root-relative `/x` in templates or content. Use `"x" | relURL` in templates, `{{</* staticrel "x" */>}}` in Markdown, and `files/x.pdf` (no leading slash) in front-matter `links:`.
2. **baseURL is hard-coded https** in `hugo.yaml`. CI does not pass `--baseURL`, because `configure-pages` can yield `http://`.
3. **Folder name = permanent slug** (`permalinks` use `:contentbasename`). Never rename a folder once it's live.
4. **Single source of truth.** Title, authors, venue, and abstract are defined once in front matter. The homepage intro (`content/_index.md`) and the bio (`content/bio/index.md`) are different text. All labels live in `i18n/en.yaml`.
5. **No JS-only content.** Every writing is in the static HTML. JS only filters.
6. **Tabs must filter.** Every `.tab-btn` has a click handler and every `.pub-item` has a `data-tab`. The hash format is `#<tab>?q=…&area=…&year=…&type=…&sort=…`; tab clicks use `pushState`, so the back button works.
7. **No internal notes on the page.** Flags for the owner go in `<!-- NOTE FOR RIKIO: … -->` comments only.
8. **Always light mode**, warm palette (tokens at the top of `assets/css/custom.css`), Stanford cardinal `#8c1515` as a sparing accent. The visual model is horiuchi.org (serif headings, split heading/content sections, hairline cards).
9. **Tag pages are disabled** (`disableKinds: [taxonomy, term]`). Keywords link to the filtered Research list instead.
10. **Accessibility:** skip link, visible gold focus ring, semantic headings (one H1 per page), `alt` on every image, explicit image dimensions, `prefers-reduced-motion` respected.

## Verification checklist after changes
- `hugo --gc --minify && npx pagefind --site public` builds with no errors.
- Click every Research tab. The list and count change, and the URL hash updates.
- `/robots.txt`, `/sitemap.xml` (all `https://`), and `/llms.txt` load. A publication page contains `citation_title` and `application/ld+json`.
- The brand name links home. "Research Areas" lands on the section heading. The mobile menu (375px) is full-width, with the hamburger on the right.
