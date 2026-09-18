## Welcome to the Seafood Globalization Lab Website Repository

This website is built with [Quarto](https://quarto.org), an open source scientific and technical publishing system.

Note: the `_utils` directory contains helper scripts originally written when migrating from Wowchemy / Hugo Academic to Quarto.


## Notes to self

### Adding publications
use `just newpub` for creation of new directory and input prompts for new record.

### Adding new people
use `just newperson` for creation of new directory and input prompts for new record.

### Deployment

- Push to `main` — a GitHub Actions workflow (`.github/workflows/publish.yml`) restores the R environment (`renv`), renders the site with Quarto, and publishes the result to the `gh-pages` branch.
- GitHub Pages is configured (Settings → Pages) to serve from the `gh-pages` branch, root directory.
- `_site/` is a build artifact and is no longer committed to `main` (see `.gitignore`); it's produced fresh by CI on every push.

