# Changelog

Breaking changes are listed first in each entry. Applying an update rebuilds the `Flarum` service from this repo; your forum data stays on the `/data` volume and in MySQL, and Flarum runs its database migrations on start.

## 2.0.2 (2026-11-21)

- First release of this template: Flarum 2.0.2 (`flarum/core` v2.0.2) on `crazymax/flarum:2.0.2`, pinned by digest `sha256:cff0216da14932588bf63956c4d3633777e7dc21956a07d1a8f0682efcf8df63`.
- Railway's MySQL service, wired over the private network.
- First admin created from `FLARUM_ADMIN_USER` / `FLARUM_ADMIN_PASSWORD` (generated), instead of the image's default `flarum`/`flarum` login.
- nginx listens on Railway's `PORT`; Flarum's scheduler runs in the same container every minute.
