# Liferay Migration Studio

> **Coming soon.** Liferay Migration Studio is in development. There's nothing to download yet.

Move content from systems with an API into **Liferay DXP**. You map the source to Liferay in a web UI, check every
request with a dry run, load, then validate the result against the source. Loads can be stopped and re-run without
creating duplicates.

**Read the docs:** <https://liferay-community-apac.github.io/liferay-migration-studio/>

* [How it works](https://liferay-community-apac.github.io/liferay-migration-studio/how-it-works.html)
* [Liferay Web Content to the new CMS](https://liferay-community-apac.github.io/liferay-migration-studio/liferay-web-content-to-cms.html), tested from Liferay 2024.Q1 and 2025.Q1 to 2026.Q3
* [AI help](https://liferay-community-apac.github.io/liferay-migration-studio/ai.html)
* [Security and data](https://liferay-community-apac.github.io/liferay-migration-studio/security-and-data.html)

## This repository

The docs are Markdown pages in `docs/`, made from the
[app docs template](https://github.com/Liferay-Community-APAC/app-docs-template). They're published by the shared
Liferay Community APAC workflow whenever `docs/` changes on `main`, so they share the main site's header, footer,
styles and search.

To preview them locally, set `main_site: https://liferay-community-apac.github.io` in `docs/_config.yml` (don't
commit it), then:

```bash
cd docs && jekyll serve --baseurl /liferay-migration-studio
```

**Publishing:** in **Settings → Pages**, **Source** must be **GitHub Actions**.
