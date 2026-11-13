---
title: A Whole Liferay Site to the New CMS
nav_order: 4
description: "Migrating a whole Liferay DXP 7.3+ site into the new CMS of Liferay DXP 2026.Q3: people, authors, folders, comments, permissions, templates, blogs, knowledge base, wiki, message boards, and from 7.4 on drafts, history, custom fields, forms, objects and pages."
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

### Every Scenario, From 7.4

A third test seeded a 7.4 Update 92 site with the harder cases real sites hold, and migrated it into a new Space and
a new site on the same target. Every part loaded with no errors and no differences, running it again changed nothing,
and a separate check of the target, item by item, passed all 49 checks:

| From the site | In the new CMS and site |
|---|---|
| A draft never published, an article waiting for approval, an expired article | Draft entries, each saying what it was |
| A scheduled article, one due for review | The same display date and review date |
| An article edited by three people; a file with three versions | The latest version, and each older one by its author |
| An article in five languages, one in two | Every translation |
| A structure with a group of fields, a map location, a repeatable group with photos, a page link | A CMS structure with the same values |
| Links to other articles, pages and documents, and to an article that was deleted | Links to the new site and the Space's documents; the broken one reported |
| Custom fields on articles, users and documents | Fields on the structures and on users; on documents when the target has the same custom field |
| A document type with its metadata, and its documents | A CMS file type with the same fields, and its entries |
| A 15 MB file, and files named `Résumé – final (v2) #1 & 50%.pdf` or `日本語のファイル.txt` | The same files and names |
| Two user groups, site roles given directly and through a group | The same user groups; the Space's members and Space roles |
| A Liferay Form and its entries by four people | A new object for the form, its entries by their authors |
| Two custom objects with a choice list and a relationship | The same objects, entries and relationship |
| Content pages, a hidden page, a child page, a page of widgets, a menu, a display page template | The same pages, menu and display page template on a new site |
| A blog with a cover image, a description and ratings | The CMS Blog with its cover image, description and ratings |

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

From Liferay 7.4 on, also:

| From the site | To the new CMS | |
|---|---|---|
| Drafts, pending and expired articles | Draft entries | `legacyStatus` says which they were. The CMS takes only future expiration dates. |
| Scheduled articles, review dates | The entry's display date and review date | |
| Older versions of articles and files | **Legacy Version**, a new object | Each by its author, with its status, date and content. |
| Custom fields | Fields on the CMS structures; on the *User* object | Documents keep theirs when the target has the same custom field on documents. |
| Document types and their metadata | A CMS file type with the same fields | Each typed document becomes its entry, pointing to the file. |
| User groups | User groups | With their members. One with the same name is reused. |
| The site's members and site roles | The Space's members and Space roles | Site Administrator becomes Asset Library Administrator, and so on; a custom site role gets a Space role of the same name. Roles given through a user group are given to the person. |
| Liferay Forms | A new object for each form | Its entries by their authors; choices as their labels. |
| Custom objects | The same objects | With their fields, choice lists, relationships and entries, by their authors. |
| Pages, navigation menus, display page templates | A new site, connected to the Space | Content pages with their fragments, pages of widgets with their widgets, the page tree and hidden pages. Display page templates are created for the new structures, without their old field mappings. |
| Blogs' cover images, descriptions and ratings | The CMS Blog's cover image, and fields added to CMS Blogs | |
| Links in articles | Links to the new site and the Space's documents | A link to something that wasn't migrated is kept and reported. |

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

- **Site role assignments and user groups from 7.3.** Liferay 7.3's API doesn't list them. Regular roles and their
  users are migrated. From 7.4 on, both are migrated.
- **Passwords.** Migrated users sign in with your single sign-on, or reset their password.
- **From 7.3: version history, drafts, custom fields, forms, objects and pages.** They need 7.4's APIs.
- **Pages' look and feel and permissions**, and page elements that show collections or forms (they're reported).
- **Files attached to custom objects** (they're reported).
- **Original dates** as the item's own: Liferay sets them at import. They're kept in `legacyDateCreated` and
  `legacyDateModified`.

## Before You Start

- Use a target account that can create users, roles, organizations and object definitions.
- Turn off email notifications on the target until the migration is done, so users aren't emailed about every
  migrated item.
- Do a dry run, and review the new structures it would create.
- For pages, turn on the Site Pages API in the target (a beta feature in 2026.Q3: feature flag `LPD-35443`).
- For documents' custom fields, create the same custom fields on documents in the target first.
