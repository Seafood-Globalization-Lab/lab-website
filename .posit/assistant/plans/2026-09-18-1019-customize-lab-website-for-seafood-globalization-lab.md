# Plan: Customize Masiello Group website template for the Seafood Globalization Lab

## Goals (this pass)
1. Restructure site navigation/pages: keep Home, People, Publications, Contact; add new Teaching and Prospective Students pages (placed in navbar before Contact); remove Research, NSF DMREF, Codes, and News sections entirely.
2. Strip out all Masiello-Group-specific content (people, publications, branding, contact details, links) so what remains are clean, reusable templates — without yet filling in Seafood Globalization Lab-specific content (that's a follow-up pass, pending details from the user).

## Scope note
This plan produces a *de-branded template* site with the right page structure. It does not invent new content for the Seafood Globalization Lab (bios, publication list, teaching info, PI contact details) since we don't have that information yet — placeholders will be left where real content is needed. A follow-up conversation can fill those in.

---

## Part 1 — Page/navigation restructuring

### 1a. Remove obsolete sections
- Delete directories: `research/`, `dmref/`, `codes/`, `news/`.
- Remove any references to these from `_quarto.yml`:
  - Navbar entries for `research/index.qmd`, `publications` stays, `dmref/index.qmd` ("NSF DMREF"), `codes/index.qmd` ("Codes"), `news/index.qmd`.
  - The `sidebar:` block (currently only used for the `dmref` section) — remove entirely since no remaining page uses a sidebar.
- Search remaining files (home page, contact, people templates) for any links pointing to `research/`, `dmref/`, `codes/`, or `news/` and remove/update them (e.g., `index.qmd` currently references research areas).

### 1b. Reorder / finalize navbar
New navbar left-to-right:
1. Home (implicit, via logo/title link)
2. People (`people/index.qmd`)
3. Publications (`publications/index.qmd`)
4. Teaching (`teaching/index.qmd`) — **new**
5. Prospective Students (`prospective-students/index.qmd`) — **new**
6. Contact (`contact.qmd`)

### 1c. Create new pages
- **`teaching/index.qmd`**: new minimal page with a title/heading and placeholder body text (e.g., "Course list and materials coming soon" or a simple table structure for course name / term / description) — structured so it's easy for the user to fill in later. No Masiello content to strip since it's new.
- **`prospective-students/index.qmd`**: new minimal page with a title/heading and placeholder sections for things labs commonly include (e.g., "How to apply," "What we look for," "Funding/openings"), left as placeholder prose for the user to customize.
- Both pages follow the same YAML frontmatter conventions as existing top-level pages (title, page-layout, etc. — will match `contact.qmd`'s structure for consistency).

### 1d. Update home page (`index.qmd`)
- Remove references/links to deleted sections (e.g., any "Research" teaser grid or links into `research/`).
- Leave core welcome layout structure intact as a template; flag Masiello-specific text for removal in Part 2.

---

## Part 2 — Remove Masiello-Group-specific content

### 2a. People page
- `people/index.qmd`: keep the listing template/logic (grouping by `people_group`, layout), but it will naturally show nothing once individual person directories are removed.
- Delete all 29 individual person directories under `people/` (e.g., `people/masiello-david/`, and all others), since the user asked that all specific people be removed.
- Keep `people/_template.qmd` as the template for adding new members.
- Update `_utils/newperson.sh` if it hardcodes any Masiello-specific defaults (will check during implementation).

### 2b. Publications page
- Delete all 101 individual publication directories under `publications/` (all specific entries), keeping only:
  - `publications/index.qmd` (listing page/template logic)
  - `publications/_template/` (template for new entries)
  - `publications/_metadata.yml`
- Update `_utils/newpub.sh` if it hardcodes Masiello-specific defaults.

### 2c. Contact page (`contact.qmd`)
- Remove David J. Masiello's name, email (`masiello@uw.edu`), phone number, UW Chemistry department address, and any Masiello-specific affiliation text.
- Replace with generic placeholder fields (e.g., `[PI Name]`, `[email]`, `[address]`) or leave blank placeholders clearly marked for the user to fill in with Seafood Globalization Lab details.

### 2d. Site-wide branding (`_quarto.yml`)
- `title: "Masiello Group"` → `"Seafood Globalization Lab"`.
- `logo-alt: "Masiello Research Group"` → `"Seafood Globalization Lab"`.
- `site-url` (currently `faculty.washington.edu/masiello/`) → `https://seafood-globalization-lab.github.io/lab-website/` (GitHub Pages URL for the confirmed org/repo `Seafood-Globalization-Lab/lab-website`).
- `repo-url` (currently `MasielloGroup/MasielloGroupWebsite`) → `https://github.com/Seafood-Globalization-Lab/lab-website`.
- `google-analytics: "UA-177339812-1"` → remove the key entirely (no replacement ID available yet).
- `logo: images/logo.drawio.png` → remove the key; flag that a new logo image is needed (won't fabricate one).
- `favicon: images/favicon.drawio.png` → remove the key; flag that a new favicon is needed.
- Footer copyright / GitHub link referencing `MasielloGroup` repo → update to `https://github.com/Seafood-Globalization-Lab/lab-website`.

### 2e. Images
- Remove Masiello/DMREF-specific images no longer referenced after cleanup: `images/logo.drawio.png`, `images/favicon.drawio.png`, `images/dmref-logo.png`, `images/washington.png`, `images/rice.png`, `images/temple.png` (these were only used by dmref/codes pages being deleted).
- Keep generic background images (`background*.jpg`, `chalkboard_sm.jpg`, `flare*.jpg`, `cmm5.jpg`) as reusable template assets (confirmed: keep).
- Note: site will temporarily have no logo/favicon until the user supplies new ones — `_quarto.yml` will just omit those keys rather than pointing to missing files.

### 2f. Project/metadata files
- `QuartoMasielloGroupWebsite.Rproj` → rename to `lab-website.Rproj` (confirmed).
- `README.md` → update any Masiello-specific text/links (repo URL, deployment target); rewritten "Deployment" section covered in 2g.
- `.github/workflows/scp-to-server.yml` → remove.

### 2g. Update publishing workflow — switch to GitHub Pages
Replace the current "render locally, commit `_site/`, SCP to a server" workflow (README.md lines 16–20, `.github/workflows/scp-to-server.yml`, removed per 2f) with a GitHub Actions–driven GitHub Pages deploy.

- **New workflow file** `.github/workflows/publish.yml`:
  - Trigger: `push` to `main` only (confirmed: no separate preview/`updates` branch — simple `main` → `gh-pages` flow). Also add `workflow_dispatch` for manual re-runs.
  - Steps:
    1. `actions/checkout@v4`
    2. `r-lib/actions/setup-r@v2` + `r-lib/actions/setup-renv@v2` — **confirmed necessary**: `contact.qmd` contains a live R code chunk (renders a `leaflet` map), so R + the packages in `renv.lock` must be available at render time in CI.
    3. `quarto-dev/quarto-actions/setup@v2` to install the Quarto CLI.
    4. `quarto-dev/quarto-actions/publish@v2` with `target: gh-pages`, which renders the site and pushes the result to a `gh-pages` branch (replaces manual `quarto render` + committing `_site/`).
  - Permissions: `contents: write` (needed for the action to push to `gh-pages`).
- **Stop committing `_site/` to `main`**: add `/_site/` to `.gitignore` and remove the currently-tracked `_site/` directory from version control, since GitHub Pages will now be served from the `gh-pages` branch that the Action manages.
- **`_quarto.yml`**: `site-url` set to `https://seafood-globalization-lab.github.io/lab-website/` (per 2d, using confirmed org/repo `Seafood-Globalization-Lab/lab-website`).
- **Repo settings (manual step for the user, outside this plan's file edits)**: after the first successful workflow run creates the `gh-pages` branch, go to GitHub repo Settings → Pages and set source to "Deploy from a branch" → `gh-pages` / `root`.
- **README.md**: rewrite the "Deployment" section to describe the new flow — push to `main` → GitHub Action restores R/renv, renders with Quarto, and publishes to `gh-pages` → served via GitHub Pages. Remove references to the `updates` branch and manual SCP process (confirmed: no preview branch going forward).

### 2h. Miscellaneous sweep
- After the above, run a repo-wide search for the strings "Masiello", "masiello", "MasielloGroup", "David J. Masiello" to catch anything missed (e.g., in `styles/`, `_ejs/` templates, `AGENTS.md` broader-context note is fine to leave as historical record, but check other stray references).

---

## Deliverables / verification
- `quarto render` should succeed with no broken links after changes (will check that deleted-page references are fully removed, e.g. no dangling links to `research/`, `dmref/`, `codes/`, `news/`).
- Provide the user a summary list of every placeholder left behind (contact info, PI name, logo, favicon, site URL, analytics ID, teaching content, prospective-students content) so they know what still needs real content.

## Open questions — resolved
All prior open questions have been settled with the user:
- Site title / `logo-alt`: **"Seafood Globalization Lab"**.
- Generic background images: **keep** as reusable template assets.
- `.Rproj` filename: **`lab-website.Rproj`**.
- Google Analytics: **remove the key** for now (no ID available yet).
- GitHub org/repo: **`Seafood-Globalization-Lab/lab-website`** — used to set `repo-url` and `site-url`.
- R at render time: **confirmed needed** — `contact.qmd` has a live `leaflet` map R chunk, so the publish workflow keeps the `setup-r`/`setup-renv` steps.
- Preview branch: **dropped** — workflow simplified to `main` → `gh-pages` only, no `updates` branch.

## Remaining open item (still needs a logo/favicon)
- No new logo or favicon image has been supplied yet. `_quarto.yml` will simply omit the `logo`/`favicon` keys until the user provides replacement image files; this is a known gap to flag in the final summary, not something to block implementation.
