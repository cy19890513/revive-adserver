# revive-adserver

Revive Adserver — the world's most popular free, open-source ad serving system —
imported here module by module across 10 pull requests, starting with the
architecture and framework and moving into the details.

- **Upstream:** https://github.com/revive-adserver/revive-adserver
- **License:** GNU General Public License v2 (see `LICENSE.txt`).
  This repository is a module-by-module import of the upstream codebase; all
  copyright remains with the Revive Adserver team and contributors.

## Architecture

```
Browser / publisher page
        │  ad tag (adx.js, adjs.php, adview.php, adclick.php, ...)
        ▼
┌─────────────────── www/delivery ───────────────────┐
│  Ad delivery engine: banner selection, capping,    │
│  targeting, logging (lib/max, lib/OA delivery)     │
└──────────────┬───────────────────────┬─────────────┘
               │                       │
               ▼                       ▼
   ┌───────────────────┐   ┌───────────────────────┐
   │  www/admin        │   │  www/api (XML-RPC)    │
   │  Management UI:   │   │  Programmatic access  │
   │  inventory, stats │   │  to all entities      │
   └────────┬──────────┘   └───────────┬───────────┘
            │                          │
            ▼                          ▼
   ┌──────────────────────────────────────────────┐
   │  lib/OA · lib/OX · lib/RV · lib/max          │
   │  Core framework: DAL, services, auth, config  │
   └──────────────────┬───────────────────────────┘
                      ▼
   ┌──────────────────────────────────────────────┐
   │  etc/tables_core.xml (+ etc/changes upgrades)│
   │  MySQL / MariaDB / PostgreSQL schema         │
   └──────────────────────────────────────────────┘

   maintenance/ ......... scheduled tasks (priority, stats)
   plugins/ ............. plugin framework + bundled plugins
```

**Request flow:** a publisher page loads an invocation tag → the delivery
endpoint (`www/delivery/*`) selects a banner through the framework's delivery
logic → the impression/click is logged → advertisers manage inventory and read
statistics through `www/admin`, or programmatically through `www/api`.

## The 10 PRs

| PR | Title | Contents |
|----|-------|----------|
| 1 | project architecture, bootstrap and documentation | README, GPL-2.0, composer.json, `init*.php` bootstrap, entry points |
| 2 | core framework classes (OA, OX, RV, Max) | `lib/OA`, `lib/OX`, `lib/RV`, `lib/max` |
| 3 | database schema and configuration | `etc/tables_core.xml`, upgrade chain, config templates |
| 4 | ad delivery engine | `www/delivery`, `adview.php`, `adclick.php`, `adx.js`, … |
| 5 | admin UI — inventory management | advertisers, campaigns, banners, zones, channels |
| 6 | admin UI — system, reporting and settings | dashboard, stats, users, settings, installer |
| 7 | XML-RPC API | `www/api` |
| 8 | maintenance engine and developer scripts | `maintenance/`, `scripts/` |
| 9 | plugin system and bundled plugins | `plugins/`, `plugins_repo/` |
| 10 | bundled vendor libraries and test suite | Zend, Smarty, `tests/`, … |

## Requirements

- PHP >= 8.1 with mysqli/pgsql, gd, xml extensions
- MySQL/MariaDB or PostgreSQL
- A web server (Apache/Nginx) and cron for `maintenance/maintenance.php`

## Install (standard upstream flow)

1. `composer install`
2. Point the web root at this directory and open `www/admin/install.php`
3. Follow the installer, then add the maintenance script to cron

## License

GNU General Public License v2 — see `LICENSE.txt`.
