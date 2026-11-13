---
title: A Whole Liferay Site to the New CMS
nav_order: 4
description: "Migrating a whole Liferay DXP 7.3+ site into the new CMS of Liferay DXP 2026.Q3: people, authors, folders, comments, permissions, templates, blogs, knowledge base, wiki and message boards."
---

# A Whole Liferay Site to the New CMS
{: .no_toc }

{: .important }
**Preview.** Liferay Migration Studio is in development. Details can change before release.

1. TOC
{:toc}

This ready-made migration, **Whole Liferay site (7.3+) → 2026.Q3 CMS**, moves everything
[Liferay Web Content to the New CMS](liferay-web-content-to-cms.html) moves, and the rest of the site around it:
the people, each item's original author, folders, comments, permissions, templates, blogs, knowledge base, wiki and
message boards.

Where the new CMS has a place for something, it goes there. Where it hasn't, the migration creates one: three new CMS
content structures and two plain objects, so nothing that has an API is left behind.

## Tested

On real Liferay, from `liferay/dxp:7.3.10-u36` (DXP 7.3) to `liferay/dxp:2026.q3.9`, with a site holding five users
in three levels of organizations, custom roles, and content written by those users:

| From the site | Loaded | Read back and compared |
|---|---|---|
| Organizations, custom roles, users and their roles | 4, 2, 6 and 2 | no differences |
| Articles, by their authors, in their folders | 213 of 213 | 60, no differences |
| Article permissions | 213 of 213 | |
| Comments on articles and blogs, and replies | 31 of 31 | no differences |
| Blogs | 5 of 5 | no differences |
| Knowledge base: folders, articles (nested), attachments | 2, 6 and 1 | no differences |
| Wiki: pages (with child pages), attachments | 5 and 1 | no differences |
| Message boards: categories, threads, replies (nested, accepted answers) | 3, 6 and 12 | no differences |
| Templates | 1 of 1 | no differences |

There were no errors, every item kept its author, and running the load again changed nothing.

The same test from `liferay/dxp:7.4.13-u92` (DXP 7.4 Update 92), into a new Space on the same target, loaded 210 of
210 articles, 30 comments and replies, 5 blogs, the knowledge base, wiki, message boards and template with no errors and
no differences. The target already had the organizations, roles and users from the 7.3 test, so they were reused by
name and email instead of created again.

## What's Migrated

| From the site | To the new CMS | |
|---|---|---|
| Organizations | Organizations | The tree is kept. An organization the target already has (same name) is reused. |
| Custom roles | Roles | Regular and site roles, by name. Liferay's own roles already exist, and so may yours: a role the target already has (same name) is reused. |
| The site's users | Users | With their organizations and regular roles. An account the target already has (same email) is reused. |
| Web content folders | CMS folders in the Space | Articles go in their folder. |
| Articles | CMS entries | As [Web Content to the New CMS](liferay-web-content-to-cms.html), plus their **original author**, rating (average and count) and related content. |
| Article permissions | The entry's permissions | Roles by name, *Site Member* becomes *Asset Library Member*. View, update, delete and permissions. |
| Blogs | CMS Blogs | Title, subtitle, content (images rewritten), display date, keywords, categories, author. In a *Blogs* folder. |
| Knowledge base | **Knowledge Base Article**, a new CMS structure | Folders become CMS folders; child articles point to their parent; attachments go to the Space's documents. |
| Wiki | **Wiki Page**, a new CMS structure | Each wiki becomes a CMS folder; child pages point to their parent; the wiki markup is kept as it is; attachments go to the Space's documents. |
| Message boards | **Discussion**, a new CMS structure | Categories become CMS folders. Question, answered and locked flags, and views. |
| Comments and message board replies | **Legacy Comment**, a new object | Each points to its entry and to the comment it replies to; accepted answers are marked. |
| Web content templates | **Legacy Template**, a new object | The FTL source and its structure, so it can be rebuilt as a display page template or fragment. |

Every migrated item also keeps its original ID, friendly URL, author, dates and rating in fields starting with
`legacy`.

### Why Comments Are a New Object

Liferay 2026.Q3's API doesn't accept comments on CMS entries yet: every comment request answers *Unsupported
operation*, for custom structures and CMS Blogs alike. So comments and replies are kept in their own object, with
the entry they belong to and their author, ready to be moved into CMS comments when the API accepts them.

## How Authors Are Kept

Liferay's regular API records the account that runs the migration as the author of everything it creates. Articles,
blogs, the new entries and comments are therefore loaded with Liferay's **batch import**, which keeps each one's
original author. Users are imported first, so every author exists before their content.

## Not Migrated

- **Site role assignments and user groups.** Liferay 7.3's API doesn't list them. Regular roles and their users
  are migrated.
- **Passwords.** Migrated users sign in with your single sign-on, or reset their password.
- **Blog descriptions and ratings.** CMS Blogs have no fields for them.
- **Version history and drafts.** Only the latest approved version.
- **Pages, page templates and navigation menus.**
- **Original dates** as the item's own: Liferay sets them at import. They're kept in `legacyDateCreated` and
  `legacyDateModified`.

## Before You Start

- Use a target account that can create users, roles, organizations and object definitions.
- Turn off email notifications on the target until the migration is done, so users aren't emailed about every
  migrated item.
- Do a dry run, and review the new structures it would create.
