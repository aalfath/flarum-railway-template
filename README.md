[![Deploy on Railway](https://railway.com/button.svg)](https://railway.com/new/template/flarum-1?utm_medium=integration&utm_source=button&utm_campaign=flarum)

# Flarum on Railway

One-click [Flarum](https://flarum.org) forum for [Railway](https://railway.com): Flarum **2.0.2** with Railway's MySQL, every piece of state on a volume, a generated admin password, a health check, and update notices when this repo bumps the version.

## What the template deploys

| Service | Source | What it does | Volume |
|---|---|---|---|
| `Flarum` | this repo (`Dockerfile`) | Flarum 2.0.2 on nginx + PHP-FPM 8.5, public HTTPS domain, scheduler (cron) every minute | `/data` (uploads, avatars, compiled assets, extension list, storage) |
| `MySQL` | Railway's MySQL | Forum database, private network only | `/var/lib/mysql` |

The image is [`crazymax/flarum:2.0.2`](https://github.com/crazy-max/docker-flarum) pinned by digest (Flarum publishes no official image). This repo adds a small wrapper in `rootfs/`:

- `railway-env`: sets `FLARUM_BASE_URL` to `https://` + the service's Railway domain, and refuses to start without an admin password.
- The image's installer creates the first admin from `FLARUM_ADMIN_USER` / `FLARUM_ADMIN_PASSWORD` instead of its built-in `flarum`/`flarum` login (patched in the `Dockerfile`, build fails if upstream changes).
- `railway-post`: makes nginx listen on `PORT` and enables Flarum's scheduler in the same container.

## Variables

| Service | Variable | Default | Notes |
|---|---|---|---|
| Flarum | `FLARUM_ADMIN_USER` | `admin` | First admin's username (used on first install only) |
| Flarum | `FLARUM_ADMIN_PASSWORD` | generated, 24 characters | First admin's password; read it in the service's Variables tab |
| Flarum | `DB_HOST`, `DB_PORT`, `DB_NAME`, `DB_USER`, `DB_PASSWORD` | references to `MySQL` | Private network |
| Flarum | `PORT` | `8000` | nginx port behind the Railway domain |

Optional, set on the `Flarum` service if you need them: `FLARUM_BASE_URL` (a custom domain, for example `https://forum.example.com`), `FLARUM_ADMIN_EMAIL`, `FLARUM_FORUM_TITLE` (first install only), `UPLOAD_MAX_SIZE` (default `32M`), `MEMORY_LIMIT` (default `256M`), `FLARUM_DEBUG`. Email (SMTP) is set in Flarum's admin panel under **Email**.

## After deploy

1. Open the `Flarum` service's domain. The first boot installs Flarum into MySQL (about a minute).
2. Log in with `FLARUM_ADMIN_USER` and the `FLARUM_ADMIN_PASSWORD` value from the Variables tab.
3. In **Administration**, set the forum title, configure **Email** (SMTP) so sign-up confirmations go out, and enable the extensions you want.

Extensions installed with `extension require vendor/package` (Railway's service shell) are listed in `/data/extensions/list` and reinstalled on every start.

## Updates

A merge to this repo's `main` (a new pinned Flarum version) shows as an "update available" notice in your Railway project. Read [CHANGELOG.md](CHANGELOG.md) first, back up the MySQL database, then apply it. Flarum runs `php flarum migrate` on every start.
