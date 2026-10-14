# AGENTS.md

Notes for AI coding agents working in this repository.

- **What it is:** the Laravel package for [Nasaq](https://github.com/fadymondy/nasaq): Blade components under
  `resources/views/components/<name>/`, Alpine behaviours, Livewire support and a FilamentPHP plugin (`src/`).
  The markup and Tailwind classes must match the React components in the Nasaq monorepo (`packages/web`);
  the monorepo also carries this package under `packages/php`.
- **Checks:** `composer install && vendor/bin/phpunit`. CI runs PHP 8.2 to 8.4 against Laravel 12 and 13.
- **Conventions:** each component view starts with a comment listing its props. Logical properties only
  (`ms-*`, `pe-*`, `text-start`), tokens rather than raw colours, Arabic labels via the locale, never
  colour-only state.
- **Generated files:** `resources/dist/` is built output; `examples/rendered/` holds snapshots.
- **Secrets:** none belong in this repo. Never commit `.env` files or tokens.
