---
title: How It Works
nav_order: 2
description: "How Liferay Migration Studio connects, extracts, maps, orders, dry-runs, loads and validates a migration into Liferay DXP."
---

# How it works
{: .no_toc }

{: .important }
**Preview.** Liferay Migration Studio is in development. Details can change before release.

1. TOC
{:toc}

![How a migration flows: sources, extract, map, load into Liferay DXP, validate](how-a-migration-flows.svg)

A migration is a **project**: where the data comes from, where it goes in Liferay, and how each
field maps. You build it in the web UI, or start from a ready-made project for a common migration
and adjust it.

## 1. Connect

A **connection** is a system's address and how to sign in to it: Basic, OAuth 2, an API key or a
bearer token. Passwords and keys are read from environment variables on the server, so a project
can be shared or exported without them.

Sources can be Liferay itself (2024.Q1 and later), other systems with a REST API, or CSV files.
The target is **Liferay DXP 2026.Q1 or later**.

## 2. Extract

Studio reads every source record and stages it on disk before anything is written. It handles
paged APIs, nested collections (for example the categories of each vocabulary), trees, and file
downloads.

* Extraction saves its progress after every page, so it resumes where it stopped.
* At cutover, a **delta** extract reads only what changed since the last one.
* A rate limit protects a production source.

## 3. Map

A **mapping** sends one kind of source record (articles, say) to one kind of Liferay record
(CMS entries). Each mapping has field rules:

* copy a field, set a fixed value, or build one from a template;
* transform it: dates, text, lists, value lookups, localized values;
* look up a record another mapping migrated, such as an article's categories, and use its new ID.

**Preview** shows the exact request a mapping would send for real source records, before any load.

## 4. Run order, worked out for you

Mappings that look up other mappings run after them. Studio works the order out from the
mappings themselves: vocabularies before categories, folders before documents, documents before
the articles whose text links to them. Within a tree such as nested categories, parents are
written before their children.

![The run order, worked out from the mappings](run-order.png){: .screenshot }

You can add a dependency by dragging from one mapping to another, or pin an order. Studio warns
if a pin contradicts a dependency.

## 5. Dry run, then load

A **dry run** builds every request the load would send and writes nothing, so you can review it.

The **load** writes in parallel chunks and can be stopped and resumed. Every migrated record
gets a stable **external reference code**, so:

* running a load again updates records instead of duplicating them;
* records that haven't changed since the last load aren't sent again;
* a delta load at cutover writes only what changed.

Loads go into **Liferay DXP** only. Studio checks the target's edition before loading and
refuses Liferay Community Edition.

## 6. Validate

Validation compares the migration with the source:

* record counts, and which records weren't loaded and why;
* a **spot check**: a sample of migrated records is read back from Liferay and compared field
  by field with what was sent;
* **dependencies**: records whose related records weren't migrated, such as a forum message whose
  author wasn't imported.

Each problem points at the records involved. With an AI model connected, Studio can also explain
the causes: see [AI help](ai.html).

## Ways to drive it

* The **web UI**, for everything above.
* The **command line**, for scripts and scheduled cutover runs.
* An **MCP server**, so Claude or another AI assistant can check status, start dry runs and read
  reports. See [AI help](ai.html).
