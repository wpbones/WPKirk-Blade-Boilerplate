# WPKirk-Blade-Boilerplate

Focused demo of **Blade templating** in WP Bones — Laravel-style `@foreach`, `{{ }}`, partials
and inheritance, powered by [BladeOne](https://github.com/EFTEC/BladeOne) (a single-file,
standalone port of Laravel's Blade). Use this when you want expressive PHP templates instead of
raw `<?php foreach ?>` in your admin/front-end views.

## What this demos

`DashboardController@index` passes a `$books` array to the view, which renders it with
Blade syntax:

```blade
@foreach ($books as $book)
  <p>Title: {{ $book['title'] }}</p>
@endforeach
```

**Key files to read first:**

| File | What to look at |
| --- | --- |
| `resources/views/dashboard/index.blade.php` | Blade syntax in action: `@foreach`, `{{ }}`, inline helpers |
| `plugin/Http/Controllers/Dashboard/DashboardController.php` | Controller passes data via `WPKirk()->view('dashboard.index', [...])` |
| `config/menus.php` | Standard menu config wiring to the controller |

## Smoke test (manual, ~30s)

With the plugin active:

1. Log in to `wp-admin` and open **WP Kirk Menu → Main View**.
2. The **"Blade Output"** section should list 3 books (Gatsby, Mockingbird, 1984) — each with
   title, author, year.
3. The Blade template source is shown further down the page via the `wpkirk_code()` helper.
4. `wp-content/debug.log` should be clean.

If the books list is missing: check the `.cache/` folder (BladeOne compiles templates there);
if PHP errors appear about `WPKirk\Http\Controllers\Controller`, verify `composer dump-autoload`
ran.

## Use as a template

```sh
# 1. clone from the GitHub template
gh repo create my-blade-plugin --template wpbones/WPKirk-Blade-Boilerplate --public --clone
cd my-blade-plugin

# 2. rename the PHP namespace + plugin slug
composer install
php bones rename "My Blade Plugin"

# 3. build + activate
yarn install && yarn build
wp plugin activate my-blade-plugin
```

Put your Blade templates under `resources/views/` (dot-separated paths map to subfolders:
`admin.settings` → `resources/views/admin/settings.blade.php`). Layouts, partials and
directives all work — see the [BladeOne docs](https://github.com/EFTEC/BladeOne/wiki).

## Framework surface exercised

This boilerplate is the **regression bed** for the Blade templating path:

- `WPKirk\WPBones\View\View::__construct` + BladeOne initialization (cache folder at `.cache/`)
- `View::render()` branching between `.blade.php` and plain `.php` filenames
- `WPKirk()->view('dashboard.index', $data)` passing the `plugin` instance + data to the view
- Blade cache directory creation + compilation
