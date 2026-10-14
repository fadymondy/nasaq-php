<div align="center">

<img src="https://raw.githubusercontent.com/fadymondy/nasaq/main/packages/brands/assets/nasaq-mark.svg" alt="Nasaq" width="80" />

# nasaq-php

**Nasaq for Laravel: Blade components, Alpine behaviours, Livewire and FilamentPHP, with Arabic and RTL built in.**

[![CI](https://github.com/fadymondy/nasaq-php/actions/workflows/tests.yml/badge.svg)](https://github.com/fadymondy/nasaq-php/actions/workflows/tests.yml)
[![Packagist](https://img.shields.io/packagist/v/fadymondy/nasaq-php.svg)](https://packagist.org/packages/fadymondy/nasaq-php)
[![License: MIT](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![GitHub stars](https://img.shields.io/github/stars/fadymondy/nasaq-php?style=social)](https://github.com/fadymondy/nasaq-php/stargazers)

[Nasaq](https://github.com/fadymondy/nasaq) · [Docs](https://docs.nasaqui.com) · [Discussions](https://github.com/fadymondy/nasaq-php/discussions) · [Contributing](CONTRIBUTING.md)

</div>

[Nasaq](https://nasaqui.com) (نسق) for Laravel: Blade components (`<x-nq::button>`), Alpine behaviours, Livewire `wire:model` support and a FilamentPHP plugin. The markup and Tailwind classes match the React components, so a page looks the same in every stack. Arabic and RTL are native.

## Requirements

- PHP 8.2+
- Laravel 12 or 13
- Tailwind CSS v4 (or use the precompiled stylesheet)
- Optional: Livewire 3, Filament 3/4

## Install

```bash
composer require fadymondy/nasaq-php
php artisan vendor:publish --tag=nasaq-assets
```

The service provider is auto-discovered. Components are available as `<x-nq::name>`, and parts as `<x-nq::name.part>`.

## Styles

Pick one.

**Precompiled stylesheet** (no Tailwind build needed). Add it to your layout:

```blade
@nasaqStyles
```

**Your own Tailwind v4 build.** Import the Nasaq CSS and tell Tailwind to scan the views:

```css
/* resources/css/app.css */
@import "tailwindcss";
@import "@fadymondy/nasaq/nasaq.css";
@source "../../vendor/fadymondy/nasaq-php/resources/views";
```

If you published the views (`--tag=nasaq-views`), `@source "../views/vendor/nasaq"` instead.

## Alpine runtime

Interactive components (dialog, select, tabs, data-table, ...) need the Nasaq Alpine behaviours. The script registers itself on `alpine:init` and never starts Alpine, so it works with whatever starts Alpine for you.

**Plain Blade layout.** Load the Nasaq script, then Alpine (both `defer`):

```blade
<head>
    @nasaqStyles
    @nasaqScripts
    <script defer src="https://cdn.jsdelivr.net/npm/alpinejs@3/dist/cdn.min.js"></script>
</head>
```

**Livewire 3.** Livewire already bundles Alpine, so add only `@nasaqScripts`. Put it in `<head>` (deferred, so it runs before Livewire boots Alpine):

```blade
<head>
    @nasaqStyles
    @nasaqScripts
    @livewireStyles
</head>
<body>
    {{ $slot }}
    @livewireScripts
</body>
```

**Filament.** Register the plugin on a panel. It loads the stylesheet and the Alpine behaviours through Filament's asset manager:

```php
use Nasaq\Filament\NasaqPlugin;

$panel->plugin(NasaqPlugin::make());
// with your own Tailwind theme: NasaqPlugin::make()->withoutStyles()
```

Run `php artisan filament:assets` after installing.

## Usage

Blade:

```blade
<x-nq::card>
    <x-nq::card.header>
        <x-nq::card.title>Invoices</x-nq::card.title>
    </x-nq::card.header>
    <x-nq::card.content>
        <x-nq::button variant="outline">Export</x-nq::button>
    </x-nq::card.content>
</x-nq::card>
```

Livewire. Controlled components are `x-modelable`, so `wire:model` and `wire:model.live` work. The attribute goes on the component and lands on the right element (the Alpine root, or the native control for `native-select` and `field.input`):

```blade
<div>
    <x-nq::field.input wire:model.live="name" />
    <x-nq::checkbox wire:model="agree" />
    <x-nq::switch wire:model.live="alerts" />
    <x-nq::native-select wire:model="country" :options="$countries" />
    <x-nq::radio-group wire:model="contact" aria-label="Contact by">
        <label><x-nq::radio-group.radio value="email" /> Email</label>
        <label><x-nq::radio-group.radio value="sms" /> SMS</label>
    </x-nq::radio-group>
    <x-nq::slider wire:model.live="volume" label="Volume" />
    <x-nq::date-picker wire:model="date" />
    <x-nq::dialog wire:model="open"> ... </x-nq::dialog>
</div>
```

Server state is rendered first, so there is no flash before Alpine starts. Do not wrap a Nasaq component in `wire:ignore`: Livewire needs to morph it to push server state in.

Alpine without Livewire uses `x-model` the same way, and `$wire.entangle` also works when you need it:

```blade
<x-nq::switch x-model="enabled" />
<div x-data="{ enabled: $wire.entangle('alerts') }">
    <x-nq::switch x-model="enabled" />
</div>
```

Filament. Inside a page, widget or custom form field view, the components work as in any Blade view once the plugin is registered.

## RTL and Arabic

Set the app locale to `ar` and the components switch to Arabic labels and mirror to RTL (logical properties, so no extra CSS). Set `dir="rtl"` on `<html>`. Use `<x-nq::field.input ltr />` for emails, URLs and codes inside Arabic forms.

## Currency

Money defaults to USD, or SAR when the locale is Arabic. Override it with `NASAQ_CURRENCY` or `config('nasaq.currency')`. `Nasaq\Nasaq::money(1250)` formats like the other stacks.

## Components

The full list, props and live examples are at https://docs.nasaqui.com. Every component page has a Blade tab. Each component's view starts with a comment listing its props.

## MCP server for AI

Give Claude, Cursor or any MCP client the component manuals:

```bash
claude mcp add --transport http nasaq https://mcp.nasaqui.com/mcp
# or: claude mcp add nasaq -- npx -y @fadymondy/nasaq-mcp
```

## Config

```bash
php artisan vendor:publish --tag=nasaq-config   # brand, currency, cdn
php artisan vendor:publish --tag=nasaq-views    # override the Blade views
```

`NASAQ_CDN` serves the stylesheet and script from a CDN instead of `public/vendor/nasaq`.

## Testing

```bash
composer install
vendor/bin/phpunit
```

## Contributing

Contributions are welcome: bug reports, component parity fixes with the React package, Livewire and Filament improvements. Read [CONTRIBUTING.md](CONTRIBUTING.md) for setup, conventions
and the DCO sign-off, then look for
[`good first issue`](https://github.com/fadymondy/nasaq-php/labels/good%20first%20issue).

## Community

- [GitHub Discussions](https://github.com/fadymondy/nasaq-php/discussions): questions, ideas, show and tell
- [Issues](https://github.com/fadymondy/nasaq-php/issues/new/choose): bugs and feature requests
- Security reports: see [SECURITY.md](SECURITY.md)

Please follow the [Code of Conduct](CODE_OF_CONDUCT.md).

## License

MIT, © 2026 Fady Mondy. See [LICENSE](LICENSE).
