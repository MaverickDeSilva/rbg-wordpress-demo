# Royal Borough of Greenwich – WordPress demo (COMP1643)

A student coursework prototype that redesigns part of the Royal Borough of Greenwich website in WordPress. It covers the bins and recycling area, a fly-tipping report form, a missed-bin form, and a staff workflow for handling reports.

> **This is not the official Royal Borough of Greenwich website.** It is a university coursework demo built and run locally with LocalWP.

This repository is a **site backup**. It holds the database, the uploaded media and fonts, and the list of plugins needed to rebuild the site. WordPress core and the plugins are not stored here. You install them from wordpress.org at the versions listed below.

## What's in the repo

| Path | Contents |
|---|---|
| `database/rbg-demo.sql` | MySQL dump of the whole site: pages, posts, reports, forms, menus, theme styles, users and plugin settings |
| `wp-content/uploads/` | Images (news photos, logo), the self-hosted Kantumruy Pro font files, and Forminator test uploads |
| `README.md` | This setup guide |

**Removed for safety before publishing:** every user password hash, login sessions, calendar feed tokens, and cached transients. One test form entry had personal details and now uses placeholders. `wp-config.php` is not included.

## Stack

| Component | Version |
|---|---|
| WordPress | 7.1.5 |
| Theme | Twenty Twenty-Five 1.5 (block theme, customised through Global Styles) |
| PHP | 8.2.29 |
| Web server | nginx 1.26.1 |
| Database | MySQL 8.4.0 |
| Local dev tool | LocalWP 10.2.0 |

### Plugins

| Plugin | Slug | Version | Used for |
|---|---|---|---|
| Forminator | `forminator` | 1.58.6 | Fly-tip and missed-bin forms; fly-tip submissions create a pending `report` post |
| Custom Post Type UI | `custom-post-type-ui` | 1.19.3 | The `report` post type for fly-tip reports |
| PublishPress Planner | `publishpress` | 4.9.0 | Content Board and email notifications for workflow changes |
| PublishPress Statuses | `publishpress-statuses` | 1.3.8 | Custom statuses: Assigned, In progress, Resolved |
| Yoast SEO | `wordpress-seo` | 28.9 | Meta titles and descriptions, XML sitemap, schema, llms.txt |
| User Switching | `user-switching` | 1.12.2 | Switching between staff accounts to demo the workflow |

## Restore the site in LocalWP

1. **Create a site.** In LocalWP, click **+** → *Create a new site* → name it `RBG Demo` → *Custom* environment: PHP 8.2, nginx, MySQL 8.4. Any admin username and password will do; the import replaces them.
2. **Open a shell.** Right-click the site → **Open site shell**. The commands below run there.
3. **Match WordPress and install the plugins** (installed but not activated; the database switches them on):

   ```
   wp core update --version=7.1.5 --force
   wp plugin install custom-post-type-ui --version=1.19.3
   wp plugin install forminator --version=1.58.6
   wp plugin install publishpress --version=4.9.0
   wp plugin install publishpress-statuses --version=1.3.8
   wp plugin install user-switching --version=1.12.2
   wp plugin install wordpress-seo --version=28.9
   ```

4. **Copy the uploads.** Copy this repo's `wp-content/uploads` folder over the site's `app/public/wp-content/uploads` folder.
5. **Import the database.** From the shell, pointing at wherever you cloned this repo:

   ```
   wp db import "C:\path\to\rbg-wordpress-demo\database\rbg-demo.sql"
   ```

   Or use the site's **Database** tab → *Open Adminer* → *Import*.
6. **Fix the address if needed.** The backup uses `http://localhost:10004`. If your new site has a different address, replace it:

   ```
   wp search-replace "http://localhost:10004" "http://your-site-address" --skip-columns=guid
   ```

7. **Set passwords.** The backup has no passwords, so set one for each account you want to use:

   ```
   wp user update akindu --user_pass="choose-a-password"
   wp user update sam.street morgan.manager chris.contributor web.team --user_pass="choose-a-password"
   ```

   You can also turn on LocalWP's **One-click admin** for the `akindu` account, or use *Lost your password?* on the login page and open the reset email in LocalWP's Mailpit.
8. **Tidy up.** Run `wp rewrite flush`, then open the site. If Yoast asks to optimise SEO data, let it run.

## Accounts

| Username | Role | Demo purpose |
|---|---|---|
| `akindu` | Administrator | Site owner |
| `sam.street` | Editor | Street services officer who handles fly-tip reports |
| `morgan.manager` | Editor | Manager who reviews and resolves reports |
| `chris.contributor` | Contributor | Writes news posts that need approval |
| `web.team` | Editor | Web team who approve and publish news |

All emails use made-up `@rbg-demo.test` addresses, so notifications only show up in Mailpit.

## Site map

- **Home**
- **Recycling and rubbish** → Bins and collections → Find your bin collection day, Report a missed bin
- **Parking, transport and streets** → Report issues on a street → Report a fly-tip → Report received
- **News** (4 published posts)
- **Contact us**, **Accessibility statement**, **Privacy notice**, **Cookies**

### User journeys

1. **Report a fly-tip:** Home → Parking, transport and streets → Report issues on a street → Report a fly-tip → form → Report received (with an FT- reference).
2. **Report a missed bin:** Home → Recycling and rubbish → Bins and collections → Report a missed bin → form → confirmation (with an MB- reference).

### Staff workflows

1. **Fly-tip reports:** a submission creates a *Pending* report → *Assigned* → *In progress* → *Resolved*, with email notifications at each step (PublishPress Planner).
2. **News approval:** a contributor submits a post for review → web team approves and publishes.

---

Built for the COMP1643 coursework at the University of Greenwich, 2026–27. Not affiliated with or endorsed by the Royal Borough of Greenwich.
