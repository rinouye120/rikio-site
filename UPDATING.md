# Updating your website

Every change you make on GitHub (in the `main` branch) rebuilds and republishes the site automatically in about two minutes. You never need to "deploy". You can edit any file directly on github.com: open the file, click the pencil icon, make your change, and click **Commit changes**.

If something goes wrong, the build fails and the old site stays up. Check the **Actions** tab on GitHub to see the error.

---

## ⚠️ First, please review the People page

The People page (`/authors/`) is **generated automatically from your co-authors**, and the link for each person was **found by web search**. Web searches can pick the wrong person or an out-of-date page. Please check every name and link in `data/coauthors.json` and fix or remove anything wrong. Diana Zixuan Wang has no link yet because no public page could be confirmed.

Also search the content folder for `NOTE FOR RIKIO`. These are hidden comments (they don't show on the site) that flag things to confirm: title differences between the old site and your C.V., an author order, a photo caption, and a few spelling corrections.

---

## The easy way: update your C.V., then ask Claude to sync

Update your C.V. as usual (Overleaf, then save the PDF to Downloads or your "website materials" folder). Then open Claude Code in this folder and say **"sync the site with my new C.V."** Claude will:
1. compare the new C.V. with the site and list every difference,
2. update the pages, adding new papers with abstracts and links and moving papers between tabs as their status changes,
3. replace the downloadable C.V. PDF,
4. show you a preview, and publish only after you approve.

The steps Claude follows are in `CLAUDE.md`. The sections below are for making changes yourself.

## Add a new paper

1. In `content/publication/`, create a new folder. Its name becomes the permanent web address, so use short lowercase words with hyphens, e.g. `content/publication/my-new-paper/`. **Never rename a folder once the site is live.** If the title changes, edit the title inside the file instead.
2. Inside it, create `index.md`. The easiest way is to copy an existing one (e.g. `content/publication/democratic-solidarity/index.md`) and edit it:

```yaml
---
title: "Paper Title"
date: 2026-10-01
weight: 5                    # order among items from the same year (lower = first)
authors: ["Rikio Inouye", "Co-Author Name"]
publication_types: ["journal_article"]   # journal_article, report, book, book_chapter, data
status: published            # published, accepted, under_review, in_progress
publication: "*Journal Name* 71(1): 1–20"
venue: "Journal Name"        # plain journal name (used by Google Scholar)
doi: "10.xxxx/xxxxx"         # optional
abstract: "Paste the abstract here."
links:
  - name: "Publisher's Version"
    url: "https://doi.org/10.xxxx/xxxxx"
  - name: "Article (PDF)"
    url: "files/my-new-paper.pdf"    # a PDF you put in static/files/ (no leading slash)
tags: ["race", "religion", "public opinion"]
---
```

3. **Which tab it appears under** is decided by `status` (Articles, Submitted Manuscripts, Research in Progress) or by `publication_types: ["data"]` (Replication Data). You can override this in `data/writings_legacy_map.json`.
4. **Which research area it appears under on the homepage** is decided by its `tags`. A paper joins an area if it has one of that area's tags. The areas and their tags are listed in `data/research_areas.json`:
   - Race and Religion in IR: `race`, `religion`
   - Democratic Solidarity and Backsliding: `democratic solidarity`, `democratic backsliding`
   - Public Opinion about International Security and Cooperation: `alliances`, `foreign aid`, `migration`, `great-power rivalry`
5. **"See Also" links are automatic.** Papers that share co-authors, tags, or title words link to each other. To force a link, add `related_papers: ["other-folder-name"]`.
6. **Co-authors:** if a new co-author appears, they're added to the People page automatically. Add their web page to `data/coauthors.json`.
7. **Cover image (optional):** put `featured.jpg` in the paper's folder. Add `image_credit: "Cover image from Oxford University Press"` if it's from a publisher.

### When a paper moves from "under review" to "published"
Edit its `index.md`: change `status:` to `published`, change `publication_types:` to `["journal_article"]`, update `publication:` and `venue:`, and add the publisher link and `doi:`. The folder name stays the same.

## Add replication data
Create a folder like `content/publication/replication-my-paper/` (copy `replication-preserve-pressure-protect-peel/`), set `publication_types: ["data"]`, `dataverse_url:`, and `related_paper: "my-paper"`. The paper and dataset link to each other automatically.

## Add a talk
Create `content/talk/_index.md` (with `title: "Presentations"`) once, then add talks as folders like papers, with `publication_types: ["presentation"]`. A Presentations tab appears on the Research page automatically. (Right now talks are listed by year on the Bio & C.V. page.)

## Update your bio, C.V., or teaching
- Homepage intro: `content/_index.md`
- Bio & C.V. page: `content/bio/index.md`. Change `cv_updated:` when you update it.
- C.V. PDF: replace `static/files/Inouye_CV.pdf` (keep the same file name).
- Teaching: `content/teaching/index.md`
- Your title, email, photo, and profile links (Google Scholar, ORCID, …): the `owner:` section of `hugo.yaml`
- Photo: replace `static/images/rikio-inouye.jpg` (square, about 720×720).

## Short links / old addresses
`data/redirects.yaml` lists short addresses that forward elsewhere (e.g. `/cv/` → Bio & C.V.). Add a line to create a new one.

## Change a button or label
All button and label text is in `i18n/en.yaml`.

## Footer credit and invisible marker
In `hugo.yaml`, `params.mysite.credit: false` removes the "Created using GaryKing.org/mysite" footer line. `params.mysite.discovery: false` removes the invisible generator tag and homepage structured data.

## Preview changes on your own computer (optional)
Install Hugo (`brew install hugo`) and Node.js, then in this folder run `npm ci` once and `hugo server`. Open http://localhost:1313/. Search only works in a full build: `npm run build`.
