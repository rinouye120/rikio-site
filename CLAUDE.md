# Rikio Inouye's academic website — notes for Claude

Live at https://www.rikioinouye.org (GitHub Pages via Actions, repo `rinouye120/rikio-site`).
Every push to `main` redeploys in ~2 minutes. Architecture: see `WEBSITE_PRINCIPLES.md`. Owner guide: `UPDATING.md`.

## "Sync the site with my C.V." — the procedure

The owner keeps the site current by updating the C.V. and asking Claude to sync. The site is NOT generated
from the C.V.; you compare the two and edit the site's files by hand.

1. **Find the newest C.V.** Candidates (pick the most recently modified *non-empty* file; Dropbox copies are often
   0-byte online-only placeholders):
   - `~/Downloads/Inouye_CV.pdf`
   - `~/Princeton Dropbox/Rikio Inouye/website materials/Inouye_CV.pdf`
   - LaTeX source: `~/Princeton Dropbox/Rikio Inouye/Apps/Overleaf/Inouye_CV/Inouye_CV.tex`
   Ask the owner if unsure which is current. Check the "Last updated" line at the top of the C.V.
2. **Compare it with the site** and list every difference before editing:
   - Positions/title → `hugo.yaml` (`params.owner`), `content/_index.md`
   - Publications (new papers, status changes such as R&R → accepted → published, venues, volume/issue, co-authors,
     titles) → `content/publication/<slug>/index.md`. `status:` drives the tab: published / accepted /
     under_review / in_progress.
   - Awards, grants, talks, affiliations, experience, service, skills → `content/bio/index.md`
   - Teaching → `content/teaching/index.md`
   - New co-authors → `data/coauthors.json` (find their page: personal site → university page → LinkedIn;
     disambiguate by field and institution; leave the URL blank if unsure and tell the owner)
3. **For new or newly published papers, fill in what the C.V. lacks:** abstract, publisher/SSRN/preprint link, DOI
   (Crossref API works; OUP blocks scripts), tags that place it in a research area (`data/research_areas.json`),
   and a `weight`. Never rename an existing publication folder, since the folder name is the permanent URL.
4. **Replace the downloadable C.V.:** copy the new PDF to `static/files/Inouye_CV.pdf` and update
   `cv_updated:` in `content/bio/index.md`.
5. **Preview before publishing:** `hugo server` → http://localhost:1313/ (or a full build:
   `hugo --gc --minify --environment preview && npx pagefind --site public`). Show the owner a summary of the
   changes and get approval.
6. **Publish only after approval:** commit and push to `main`, then confirm the Actions run succeeds
   (`gh run watch`) and spot-check https://www.rikioinouye.org.

Rules: keep the owner's wording (don't paraphrase), and flag uncertainties in `<!-- NOTE FOR RIKIO: ... -->`
comments, never as visible text. The homepage intro and the bio must stay different text. Use `preview`
environment for local builds so Google Analytics (G-JSLCH7CRDN) isn't hit.
