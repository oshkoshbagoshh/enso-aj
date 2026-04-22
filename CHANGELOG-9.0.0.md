# Laravel Enso 9.0.0 Draft

## 9.0.0

This release focuses on bringing the Laravel Enso ecosystem up to date across the full stack: Laravel 13 and PHP 8.3+ on the backend, and a modern Vue 3.5 / Vite 7 / Pinia runtime on the frontend.

It also includes a complete UI redesign pass across the active Enso UI ecosystem. The goal was not only to refresh the look and feel, but also to simplify shared ownership, replace legacy styling layers with native Bulma contracts, and standardize how applications integrate routes, stores, filters, themes, and localisation.

In parallel, we revamped the public documentation platform at `docs.laravel-enso.com`, upgraded it to VitePress 2, synchronized the package documentation surface across the ecosystem, and brought the published docs back in line with the current Enso runtime and upgrade model.

Taken as a whole, Enso 9.0.0 is one of the largest frontend/backend releases in the history of Laravel Enso.

As part of this release reset, we also intend to close the current backlog of GitHub issues and merge requests and ask contributors to open new issues or merge requests if their reports are still relevant on top of the Enso 9 line.

### Laravel 13 Compatibility

Laravel Enso 9.0.0 aligns the ecosystem with Laravel 13 and raises the minimum PHP baseline to 8.3.

### Front-end

#### ui

- completed a major UI redesign across the active Enso UI packages
- realigned shared components, tables, filters, forms, menus, settings, and shell layouts around a cleaner Bulma-first visual language
- updated the ecosystem with a clear goal of bringing the Enso frontend stack and package contracts to a current, maintainable baseline
- normalized shared styling ownership so UI policy lives in the shared UI layer instead of being scattered across package-local overrides

#### frontend runtime

- migrated the frontend runtime from the legacy Vue CLI / Webpack / Vuex model to Vite and Pinia
- standardized the new frontend baseline around:
  - Node 20
  - Vue 3.5
  - Pinia 3
  - Vite 7
  - `@vitejs/plugin-vue` 6
  - `vue-router` 4.6
  - Bulma 1
  - Axios 1
- removed Vuex and `vuex-router-sync` from the active runtime flow
- stabilized `yarn dev --force` and `yarn build` on the new toolchain
- replaced legacy `require.context`-driven assumptions with a Vite-compatible runtime model for package integration
- introduced a clearer split for compiled frontend resources and expanded lazy loading across frontend components
- reduced shipped frontend assets and chunk sizes significantly through the new bundle split and lazy-loaded component surface

#### icons and shared UI dependencies

- modernized Font Awesome integration across the shared UI ecosystem
- upgraded the shared icon stack to the current Font Awesome 7 line
- moved shared components away from ad-hoc icon registries and toward direct icon-definition imports where runtime metadata is not involved
- kept a smaller explicit runtime icon registry only for metadata-driven icons that still come from backend state
- upgraded and restyled shared UI dependencies such as `v-tooltip` to fit the new Bulma-first runtime and styling contract
- standardized `animate.css` usage on the v4 `animate__*` convention across the shared UI surface
- added the dedicated `@enso-ui/pagination` package to give pagination its own reusable public UI surface

#### loader and skeletons

- refined the general application loader behavior and moved it into its own dedicated store
- added a reusable skeleton component to complement loading states across charts, forms, and tables

#### filters and transport

- standardized frontend request serialization for complex filter payloads
- added a global Axios `paramsSerializer` for nested GET parameters
- preserved backward compatibility where possible while making request transport explicit and consistent
- normalized Enso table boolean filters by serializing booleans as `1 / 0`
- prepared the remaining filter-related packages for the stricter transport model

#### files

- implemented frontend pagination controls for the file browser
- added page-size selection and paginated navigation to the files UI
- aligned the file browser with the new reusable pagination surface instead of keeping it on a flat unpaginated listing

#### state management and bootstrap

- introduced a clear Pinia-first store convention across the ecosystem
- standardized bootstrapable stores on a `set(payload)` convention
- centralized frontend bootstrap through:
  - `client/src/js/pinia/loadState.js`
  - `client/src/js/pinia/bootstrapStores.js`
- clarified store ownership across the shared packages instead of routing everything through `@enso-ui/ui` internals

#### routes and package integration

- aligned frontend package integration with a public-package-surface model
- local applications are expected to consume published route modules and package-owned stores instead of private `@enso-ui/ui/src/...` internals
- route and store discovery now follow the new bootstrap flow instead of the older implicit runtime model

#### themes

- replaced the older shared theming approach with a Bulma-native theming model
- moved theme ownership into `@enso-ui/themes`
- consolidated runtime shell and layout tokens into the active Bulma override layer
- removed dead theme entrypoints and legacy theme indirection
- introduced `light`, `dark`, and `system` theme behavior in the current runtime model

#### localisation

- simplified localisation ownership and file flow
- added `php artisan enso:localisation:scan` to statically discover translation keys from backend and frontend sources
- made the new localisation workflow report, deduplicate, and sync discovered keys into local JSON language files
- clarified language-file ownership and reduced the legacy mixed package/app localisation flow
- removed Arabic from the built-in shared localisation seed/fallback set in the current release

#### package ecosystem cleanup

- realigned active `@enso-ui/*` package interdependencies around explicit ownership
- moved shared package-to-package relationships out of `dependencies` and into `peerDependencies + devDependencies` where appropriate
- adopted this pattern to stop hidden nested runtime copies, make package ownership explicit, and force the host application to own the shared frontend runtime surface directly
- treated the application root as the single place where shared runtime dependencies must be installed and version-aligned
- cleaned package manifests and reduced legacy build/runtime duplication across the active frontend ecosystem
- added or completed frontend companion packages where the current release surface requires them
- started building the first meaningful layer of frontend test coverage across the active UI package surface

#### documentation

- revamped `docs.laravel-enso.com` as part of the Enso 9 release wave
- upgraded the documentation site to VitePress 2
- synchronized package documentation across the active ecosystem
- brought the published docs and migration guidance in line with the current Enso runtime, package surface, and upgrade flow

#### audits

- added the frontend `@enso-ui/audits` companion package
- introduced audit index pages and diff presenters for rendering row-level change payloads in the UI
- integrated audit rows with backend table definitions and user context so historical changes can be inspected consistently from the system route group
- positioned audits as the replacement path for the older history-tracker flow

### Back-end

#### core

- required Laravel 13 and PHP 8.3
- introduced the new store-based `ProvidesState` contract for frontend bootstrap
- replaced mutation-targeted state payloads with grouped store-targeted payloads
- updated shared state building so multiple providers targeting the same store are merged before reaching the frontend
- refactored filters for the new frontend transport and state contract

#### frontend/backend contract

- replaced the old backend/frontend bootstrap contract based on `mutation()` with a store-based contract built for Pinia
- aligned Enso backend packages to emit `{ store, state }` payloads instead of Vuex mutation payloads
- prepared backend packages that expose filters or shared frontend state for the new frontend runtime

#### files

- implemented backend pagination support for file browser endpoints
- validated the requested pagination size against the configured `paginate` options
- aligned file browsing responses with paginated metadata so the new frontend controls can consume them consistently

#### localisation

- improved localisation ownership and backend support for the new frontend flow
- added backend support for the new localisation scan command and key reporting workflow
- improved deduplication and ownership of generated localisation keys

#### audits

- added the `laravel-enso/audits` backend companion package
- exposed the audit table definition, diff payloads, and route group consumed by the new frontend audit pages
- expanded the ecosystem support for browsing historical record changes through a dedicated audits flow
- positioned audits as the replacement path for the older `history-tracker` package

#### ecosystem

- normalized dependency metadata and license files across the active Enso backend package set
- migrated supported packages from PHPUnit annotations to PHPUnit attributes
- realigned package constraints and companion package contracts for the Enso 9 release wave
- added or completed frontend companion package support where required by the new runtime
- increased backend test coverage substantially across the active Enso package set

### Upgrade Steps

To upgrade an existing Enso application to 9.0.0, use the following sequence.

#### 1. Update the platform baseline

- update PHP to at least 8.3. Our recommendation is to move to the latest 8.5.x release to benefit from the latest bug fixes and performance improvements.
- run frontend install and build steps on Node 20

#### 2. Update backend dependencies

In `composer.json`, update the platform and Enso package set to the Laravel 13 / Enso 9 compatible lines.

Align `composer.json` to the actual local application baseline used for the upgrade.

For the Enso 9 rollout documented here, that baseline came from the upgraded SolarLink application.

Required packages:

```json
{
  "require": {
    "php": "^8.3",
    "cmixin/business-time": "^1.12",
    "composer/ca-bundle": "^1.4",
    "filippo-toso/pdf-watermarker": "^1.0",
    "laravel-enso/audits": "^1.0",
    "laravel-enso/cache-chain": "^2.0",
    "laravel-enso/calendar": "^3.5",
    "laravel-enso/cargo-partner": "^1.5",
    "laravel-enso/categories": "^3.5",
    "laravel-enso/cnp-validator": "^4.0",
    "laravel-enso/comments": "^4.6",
    "laravel-enso/commercial": "^5.18",
    "laravel-enso/core": "^12.2",
    "laravel-enso/currencies": "^1.6",
    "laravel-enso/data-import": "^6.10",
    "laravel-enso/discounts": "^4.0",
    "laravel-enso/emag": "^4.11",
    "laravel-enso/excel": "^3.0",
    "laravel-enso/financials": "^4.8",
    "laravel-enso/frisbo": "^2.5",
    "laravel-enso/how-to": "^5.5",
    "laravel-enso/interactions": "^1.8",
    "laravel-enso/inventory": "^5.6",
    "laravel-enso/monitored-emails": "^1.0",
    "laravel-enso/pdf": "^2.0",
    "laravel-enso/products": "^4.15",
    "laravel-enso/questionnaires": "^3.0",
    "laravel-enso/sale-channels": "^2.6",
    "laravel-enso/smart-bill": "^1.5",
    "laravel-enso/sms-advert": "^1.6",
    "laravel-enso/tasks": "^2.5",
    "laravel-enso/ticketing": "^1.8",
    "laravel-enso/tutorials": "^5.4",
    "laravel-enso/virtual-call-center": "^1.0",
    "laravel-enso/vouchers": "^2.5",
    "laravel-enso/webshop": "^4.9",
    "laravel-enso/typesense-webshop": "^1.4",
    "laravel-enso/webshop-commercial": "^2.3",
    "laravel/framework": "^13.0",
    "laravel/horizon": "^5.0",
    "laravel/pulse": "^1.2",
    "laravel/sanctum": "^4.0",
    "laravel/tinker": "^3.0",
    "laravel/ui": "^4.0",
    "openspout/openspout": "^4.26",
    "php-http/message-factory": "^1.1",
    "sentry/sentry-laravel": "^4.0.0",
    "simplesoftwareio/simple-qrcode": "^4.2",
    "symfony/http-client": "^8.0",
    "symfony/mailgun-mailer": "^6.0"
  },
  "require-dev": {
    "barryvdh/laravel-debugbar": "^4.2",
    "brianium/paratest": "^7.4",
    "fakerphp/faker": "^1.9.1",
    "filp/whoops": "^2.1.0",
    "friendsofphp/php-cs-fixer": "^3.91",
    "laravel-enso/cli": "^5.5",
    "laravel/boost": "^2.3",
    "mockery/mockery": "^1.6",
    "nunomaduro/collision": "^8.6",
    "nunomaduro/phpinsights": "^2.13",
    "phpstan/phpstan": "^2.1",
    "phpunit/phpunit": "^12.0",
    "predis/predis": "^3.2"
  }
}
```

Then install the updated dependency set:

```bash
composer install
```

If your application still declares framework-level compatibility explicitly outside the Enso core package set, align that to the Laravel 13-compatible release line as part of the same pass.

If you are also creating or rebuilding the local MySQL database for the upgrade, prefer:

- `utf8mb4` with `utf8mb4_0900_ai_ci` on MySQL 8+
- `utf8mb4` with `utf8mb4_unicode_ci` when MariaDB compatibility is required

Also review published local Enso config files when they exist.

In particular:

- update `config/files.php` to include the `paginate` key used by the files browser, for example:

```php
'paginate' => [20, 40, 60, 80, 100],
```

#### 3. Update frontend dependencies

In `client/package.json`, align the application to the actual local application baseline used for the upgrade.

For the Enso 9 rollout documented here, that baseline came from the upgraded SolarLink application:

```json
{
  "engines": {
    "node": ">=20 <21"
  },
  "dependencies": {
    "@enso-ui/accessories": "^5.0.3",
    "@enso-ui/addresses": "^3.2.4",
    "@enso-ui/audits": "^1.0.2",
    "@enso-ui/auth": "^3.1.9",
    "@enso-ui/bookmarks": "^3.1.4",
    "@enso-ui/calendar": "^5.1.3",
    "@enso-ui/card": "^4.0.3",
    "@enso-ui/cargo-partner": "git+https://general:gldt-PQdLvAJzsx7EoHpfrAvM@git.xtelecom.ro/enso-ui/cargo-partner#^1.2.4",
    "@enso-ui/categories": "^4.3.3",
    "@enso-ui/charts": "^5.0.3",
    "@enso-ui/checkbox": "^4.0.3",
    "@enso-ui/clipboard": "^3.1.1",
    "@enso-ui/comments": "^4.2.5",
    "@enso-ui/commercial": "git+https://general:gKsjv8r7QhNF4LsmTm9q@git.xtelecom.ro/enso-ui/commercial#^8.0.8",
    "@enso-ui/companies": "^5.1.3",
    "@enso-ui/confirmation": "^3.1.1",
    "@enso-ui/currencies": "^5.2.3",
    "@enso-ui/data-import": "^5.6.3",
    "@enso-ui/date": "^2.1.0",
    "@enso-ui/datepicker": "^3.1.1",
    "@enso-ui/directives": "^3.1.1",
    "@enso-ui/discounts": "git+https://general:14xmfMae5zwTK4a_3ytC@git.xtelecom.ro/enso-ui/discounts#^5.2.3",
    "@enso-ui/divider": "^3.1.1",
    "@enso-ui/documents": "^4.2.3",
    "@enso-ui/dropdown": "^4.1.3",
    "@enso-ui/dropdown-indicator": "^3.1.1",
    "@enso-ui/eav": "git+https://general:sWZosuSgerhkTxvRGaz1@git.xtelecom.ro/enso-ui/eav#^2.1.3",
    "@enso-ui/emag": "git+https://general:sMtJwLxsCcJFyBYt1Z_F@git.xtelecom.ro/enso-ui/emag#^6.3.4",
    "@enso-ui/enums": "^2.1.1",
    "@enso-ui/facebook": "^3.1.3",
    "@enso-ui/files": "^5.4.5",
    "@enso-ui/filters": "^3.3.4",
    "@enso-ui/financials": "git+https://general:C3GHyrq-p1Lt-ntsVAyX@git.xtelecom.ro/enso-ui/financials#^5.3.3",
    "@enso-ui/forms": "^4.1.5",
    "@enso-ui/frisbo": "git+https://general:SyabX8yTa4xxwyxXWFdj@git.xtelecom.ro/enso-ui/frisbo#^3.2.3",
    "@enso-ui/google": "^3.1.3",
    "@enso-ui/how-to": "^5.2.4",
    "@enso-ui/interactions": "git+https://general:gldt-6MFRHUjjJ8cfnspV5m6K@git.xtelecom.ro/enso-ui/interactions#^1.6.8",
    "@enso-ui/inventory": "git+https://general:jduz_zQy6j_SSqkZAFNW@git.xtelecom.ro/enso-ui/inventory#^5.2.3",
    "@enso-ui/io": "^3.3.5",
    "@enso-ui/laravel-validation": "^3.1.0",
    "@enso-ui/loader": "^4.1.3",
    "@enso-ui/localisation": "^5.2.8",
    "@enso-ui/logs": "^5.2.3",
    "@enso-ui/measurement-units": "^5.2.3",
    "@enso-ui/menus": "^5.2.8",
    "@enso-ui/mixins": "^5.1.1",
    "@enso-ui/modal": "^3.2.3",
    "@enso-ui/money": "^3.1.1",
    "@enso-ui/monitored-emails": "^1.3.3",
    "@enso-ui/newsletters": "git+https://general:67MqXGFFW5GeY2aHYiZU@git.xtelecom.ro/enso-ui/newsletters#^1.2.3",
    "@enso-ui/notifications": "^3.2.8",
    "@enso-ui/orderable-trees": "^3.1.3",
    "@enso-ui/packaging-units": "^4.2.3",
    "@enso-ui/pagination": "^1.0.0",
    "@enso-ui/people": "^5.2.3",
    "@enso-ui/permissions": "^5.1.3",
    "@enso-ui/progress-bar": "^2.2.1",
    "@enso-ui/progress-circle": "^3.1.1",
    "@enso-ui/product-lots": "git+https://general:SrwUskak9dz2eHcasVRH@git.xtelecom.ro/enso-ui/product-lots#^3.2.3",
    "@enso-ui/products": "git+https://general:1Y3BdsoRDLzxm-eC1VSs@git.xtelecom.ro/enso-ui/products#^5.4.7",
    "@enso-ui/questionnaires": "git+https://general:gldt-7mjUYEzPQo9QyAqaMzEJ@git.xtelecom.ro/enso-ui/questionnaires#^1.1.5",
    "@enso-ui/quick-view": "^3.2.3",
    "@enso-ui/rating": "^2.1.1",
    "@enso-ui/roles": "^5.1.3",
    "@enso-ui/route-mapper": "^1.1.0",
    "@enso-ui/sale-channels": "git+https://general:5S2aFykFxaAwRdFVT5z_@git.xtelecom.ro/enso-ui/sale-channels#^3.2.3",
    "@enso-ui/scroll-to-top": "^4.1.3",
    "@enso-ui/search-mode": "^3.1.1",
    "@enso-ui/select": "^4.1.4",
    "@enso-ui/sentry": "^1.1.0",
    "@enso-ui/services": "^5.3.4",
    "@enso-ui/smart-bill": "git+https://general:RLzpytBxNcwGjNBbzAkb@git.xtelecom.ro/enso-ui/smart-bill#^1.2.3",
    "@enso-ui/sms-advert": "git+https://general:J6zBT_DMEtRts4wRWiRt@git.xtelecom.ro/enso-ui/sms-advert#^1.2.3",
    "@enso-ui/strings": "^2.1.0",
    "@enso-ui/switch": "^2.1.1",
    "@enso-ui/tables": "^4.1.4",
    "@enso-ui/tabs": "^3.1.1",
    "@enso-ui/tasks": "^5.1.4",
    "@enso-ui/textarea": "^2.1.1",
    "@enso-ui/themes": "^3.3.14",
    "@enso-ui/ticketing": "git+https://general:gldt-yR_y-bLW66fsnQF1RYiy@git.xtelecom.ro/enso-ui/ticketing#^1.6.1",
    "@enso-ui/toastr": "^3.1.3",
    "@enso-ui/transitions": "^2.1.1",
    "@enso-ui/tutorials": "^5.2.3",
    "@enso-ui/typeahead": "^4.1.3",
    "@enso-ui/typesense": "git+https://github.com/enso-ui/typesense.git#^2.3.2",
    "@enso-ui/ui": "^7.1.17",
    "@enso-ui/uploader": "^3.1.1",
    "@enso-ui/user-groups": "^3.1.3",
    "@enso-ui/users": "^3.1.5",
    "@enso-ui/virtual-call-center": "git+https://general:9X_Ln6RustAXoio3qbJC@git.xtelecom.ro/enso-ui/virtual-call-center#^1.2.3",
    "@enso-ui/vouchers": "git+https://general:gldt-t-KjXWBri5dKygE8U2SN@git.xtelecom.ro/enso-ui/vouchers#^3.2.3",
    "@enso-ui/webshop": "git+https://general:7sad5s2xPu__--STcoH2@git.xtelecom.ro/enso-ui/webshop#^4.1.3",
    "@enso-ui/wysiwyg": "^3.1.1",
    "@fortawesome/fontawesome-svg-core": "^7.2.0",
    "@fortawesome/free-brands-svg-icons": "^7.2.0",
    "@fortawesome/free-regular-svg-icons": "^7.2.0",
    "@fortawesome/free-solid-svg-icons": "^7.2.0",
    "@fortawesome/pro-duotone-svg-icons": "^7.2.0",
    "@fortawesome/pro-light-svg-icons": "^7.2.0",
    "@fortawesome/pro-regular-svg-icons": "^7.2.0",
    "@fortawesome/pro-solid-svg-icons": "^7.2.0",
    "@fortawesome/vue-fontawesome": "3.1.3",
    "@googlemaps/js-api-loader": "^2.0.2",
    "@googlemaps/markerclusterer": "^2.0.15",
    "@sentry/browser": "6.19.7",
    "@sentry/tracing": "6.19.7",
    "@sentry/vue": "6.19.7",
    "@vue-flow/background": "^1.3.2",
    "@vue-flow/core": "^1.48.2",
    "@vueuse/core": "^14.2.1",
    "@zxing/library": "^0.21.3",
    "animate.css": "^4.1.1",
    "axios": "^1.13.6",
    "bulma": "^1.0.4",
    "dagre": "^0.8.5",
    "date-fns": "^4.1.0",
    "laravel-echo": "1.19.0",
    "lodash": "^4.17.23",
    "pdfjs-dist": "2.5.207",
    "pdfvuer": "^2.0.1",
    "pinia": "^3.0.3",
    "pusher-js": "^8.4.3",
    "quagga": "^0.12.1",
    "quill": "^2.0.3",
    "quill-mention": "^6.1.1",
    "textarea-caret": "^3.1.0",
    "v-tooltip": "^4.0.0-beta.2",
    "vue": "^3.5.30",
    "vue-collapsed": "^1.3.5",
    "vue-router": "^4.6.4",
    "vue-signature-pad": "^3.0.2",
    "vue3-barcode-qrcode-reader": "^1.0.16",
    "vuedraggable": "^4.1.0"
  },
  "devDependencies": {
    "@babel/core": "^7.28.0",
    "@babel/eslint-parser": "^7.28.0",
    "@vitejs/plugin-vue": "^6.0.5",
    "@vue/compiler-sfc": "^3.5.30",
    "@vue/eslint-config-airbnb": "^8.0.0",
    "autoprefixer": "^10.4.27",
    "eslint": "^8.57.1",
    "eslint-import-resolver-alias": "^1.1.2",
    "eslint-plugin-import": "^2.32.0",
    "eslint-plugin-vue": "^9.33.0",
    "globals": "^16.4.0",
    "patch-package": "^8.0.1",
    "postcss": "^8.4.49",
    "sass": "^1.98.0",
    "sirv": "^3.0.2",
    "vite": "^7.3.1",
    "vite-plugin-static-copy": "^3.4.0",
    "vue-eslint-parser": "^9.4.3"
  },
  "resolutions": {
    "@enso-ui/cargo-partner": "git+https://general:gldt-PQdLvAJzsx7EoHpfrAvM@git.xtelecom.ro/enso-ui/cargo-partner#^1.2.4",
    "@enso-ui/commercial": "git+https://general:gKsjv8r7QhNF4LsmTm9q@git.xtelecom.ro/enso-ui/commercial#^8.0.8",
    "@enso-ui/discounts": "git+https://general:14xmfMae5zwTK4a_3ytC@git.xtelecom.ro/enso-ui/discounts#^5.2.3",
    "@enso-ui/eav": "git+https://general:sWZosuSgerhkTxvRGaz1@git.xtelecom.ro/enso-ui/eav#^2.1.3",
    "@enso-ui/emag": "git+https://general:sMtJwLxsCcJFyBYt1Z_F@git.xtelecom.ro/enso-ui/emag#^6.3.4",
    "@enso-ui/financials": "git+https://general:C3GHyrq-p1Lt-ntsVAyX@git.xtelecom.ro/enso-ui/financials#^5.3.3",
    "@enso-ui/frisbo": "git+https://general:SyabX8yTa4xxwyxXWFdj@git.xtelecom.ro/enso-ui/frisbo#^3.2.3",
    "@enso-ui/interactions": "git+https://general:gldt-6MFRHUjjJ8cfnspV5m6K@git.xtelecom.ro/enso-ui/interactions#^1.6.8",
    "@enso-ui/inventory": "git+https://general:jduz_zQy6j_SSqkZAFNW@git.xtelecom.ro/enso-ui/inventory#^5.2.3",
    "@enso-ui/newsletters": "git+https://general:67MqXGFFW5GeY2aHYiZU@git.xtelecom.ro/enso-ui/newsletters#^1.2.3",
    "@enso-ui/product-lots": "git+https://general:SrwUskak9dz2eHcasVRH@git.xtelecom.ro/enso-ui/product-lots#^3.2.3",
    "@enso-ui/products": "git+https://general:1Y3BdsoRDLzxm-eC1VSs@git.xtelecom.ro/enso-ui/products#^5.4.7",
    "@enso-ui/questionnaires": "git+https://general:gldt-7mjUYEzPQo9QyAqaMzEJ@git.xtelecom.ro/enso-ui/questionnaires#^1.1.5",
    "@enso-ui/sale-channels": "git+https://general:5S2aFykFxaAwRdFVT5z_@git.xtelecom.ro/enso-ui/sale-channels#^3.2.3",
    "@enso-ui/smart-bill": "git+https://general:RLzpytBxNcwGjNBbzAkb@git.xtelecom.ro/enso-ui/smart-bill#^1.2.3",
    "@enso-ui/sms-advert": "git+https://general:J6zBT_DMEtRts4wRWiRt@git.xtelecom.ro/enso-ui/sms-advert#^1.2.3",
    "@enso-ui/ticketing": "git+https://general:gldt-yR_y-bLW66fsnQF1RYiy@git.xtelecom.ro/enso-ui/ticketing#^1.6.1",
    "@enso-ui/typesense": "git+https://github.com/enso-ui/typesense.git#^2.3.2",
    "@enso-ui/virtual-call-center": "git+https://general:9X_Ln6RustAXoio3qbJC@git.xtelecom.ro/enso-ui/virtual-call-center#^1.2.3",
    "@enso-ui/vouchers": "git+https://general:gldt-t-KjXWBri5dKygE8U2SN@git.xtelecom.ro/enso-ui/vouchers#^3.2.3",
    "@enso-ui/webshop": "git+https://general:7sad5s2xPu__--STcoH2@git.xtelecom.ro/enso-ui/webshop#^4.1.3"
  }
}
```

Also:

- remove Vuex-centered runtime dependencies from the active app flow
- update `@enso-ui/*` packages to the versions compatible with the new runtime
- keep required shared `@enso-ui/*` peers at the app root instead of reintroducing package-local dependency links
- keep shared runtime packages at the app root so packages do not reinstall their own nested frontend runtimes

This peer-dependency cleanup is intentional.

The goal is to:

- avoid nested copies of the same runtime package under individual `@enso-ui/*` packages
- keep the dependency graph explicit at the host-app level
- reduce build/runtime conflicts caused by mixed copies of `vue`, `pinia`, `axios`, `bulma`, `pusher-js`, and Font Awesome packages
- make `yarn install`, `patch-package`, and Vite resolution more predictable

At the project root, make sure the shared runtime and peer surface is declared explicitly.

Important examples from the Enso 9 rollout:

- framework/runtime peers such as `vue`, `vue-router`, `pinia`, and `bulma`
- shared transport/runtime packages such as `axios`, `pusher-js`, and `laravel-echo`
- shared icon/runtime packages such as `@fortawesome/fontawesome-svg-core`, `@fortawesome/free-solid-svg-icons`, `@fortawesome/free-regular-svg-icons`, `@fortawesome/free-brands-svg-icons`, and the Pro Font Awesome variants when the application uses them
- Enso shared surface packages that must exist once at the root, such as `@enso-ui/ui`, plus package peers required by the app's enabled modules
- support packages that now need explicit root declaration, such as `pdfjs-dist` for `pdfvuer` and `globals` for lint tooling

Representative package-to-peer examples:

- `@enso-ui/ui` expects shared root peers such as `vue`, `vue-router`, `pinia`, `axios`, `bulma`, `laravel-echo`, `pusher-js`, `v-tooltip`, Font Awesome packages, `@sentry/*`, and the core shared `@enso-ui/*` surface it coordinates
- `@enso-ui/files` expects peers such as `@enso-ui/ui`, `@enso-ui/pagination`, `@enso-ui/filters`, `@enso-ui/dropdown`, `@enso-ui/uploader`, `pinia`, and `vue`
- `@enso-ui/forms` expects peers such as `@enso-ui/datepicker`, `@enso-ui/select`, `@enso-ui/switch`, `@enso-ui/wysiwyg`, `bulma`, `pinia`, and `vue`
- `@enso-ui/tables` expects peers such as `@enso-ui/date`, `@enso-ui/datepicker`, `@enso-ui/dropdown`, `@enso-ui/enums`, `@enso-ui/loader`, `@enso-ui/search-mode`, `@enso-ui/select`, `@enso-ui/switch`, and `vue`
- `@enso-ui/audits` expects peers such as `@enso-ui/tables`, `@enso-ui/ui`, `@enso-ui/users`, `pinia`, and `vue`
- `@enso-ui/tasks` expects peers such as `@enso-ui/filters`, `@enso-ui/forms`, `@enso-ui/select`, `@enso-ui/tables`, `@enso-ui/ui`, `@enso-ui/users`, and `vue`
- `@enso-ui/themes` expects peers such as `@enso-ui/localisation`, `@enso-ui/ui`, `bulma`, `pinia`, and `vue`

If `yarn install` warns about missing `@enso-ui/*` peers, fix that at the application root instead of moving those packages back into package-local `dependencies`.

Then install the updated frontend dependency set:

```bash
yarn install
```

If the application still carries legacy Yarn overrides from the previous toolchain, remove them during this pass.

In particular:

- remove forced `resolutions` entries that pin `minimatch` outside the version range requested by the current ESLint stack
- regenerate the lockfile after removing such overrides so `yarn install` no longer emits compatibility warnings for `minimatch`

#### 3a. Upgrade Font Awesome and shared animation usage

When upgrading an existing application from the older frontend line, treat Font Awesome and `animate.css` as explicit migration work, not as incidental dependency bumps.

For Font Awesome:

- move the application root to the Font Awesome 7 packages declared above
- stop using per-component `library.add(...)` patterns for normal static icons
- import icon definitions directly and bind them in templates with patterns such as `:icon="faX"`
- in `Options API` components, expose imported icon definitions through `data()` or `computed` before using them in templates
- keep the global runtime registry in `client/src/js/app.js` only for icons that still come from backend state, menu metadata, notification payloads, or backend-driven table/button templates
- do not move backend-driven icon names into local component imports unless the UI config itself also moves local

Important rename and compatibility examples from the Enso 9 rollout:

- `times` moved toward `xmark`
- `question-circle` moved toward `circle-info`
- `exclamation-triangle` moved toward `triangle-exclamation`
- `pencil` and `trash-alt` moved toward `pen` and `trash-can`
- `undo` now maps to the modern `faRotateLeft` export in shared form actions

Practical rule:

- static component icons should become direct icon-definition imports
- metadata-driven icons should remain in the shared runtime registry

Package exposure rule:

- when a shared `@enso-ui/*` package emits icon names through backend metadata, menu payloads, notification payloads, or table / form button templates, the package must expose those icons through a canonical `src/icons.js`
- do not keep package icon registries only under paths such as `src/bulma/icons.js` if the application bootstrap scans `@enso-ui/**/src/icons.js`
- treat missing package-owned `src/icons.js` files as owner-package defects and fix them upstream instead of compensating by growing the host app icon registry blindly

Also verify the local bootstrap for Font Awesome:

- keep root-level Font Awesome packages installed so Vite resolves one shared runtime
- avoid mixed nested Font Awesome copies under package-local `node_modules`
- if production-only icon failures appear, inspect root Vite resolution before reintroducing ad-hoc global icon registration

For `animate.css`:

- standardize local usage on the v4 `animate__*` class naming convention
- replace legacy animation class names that still use the pre-v4 syntax

For `v-tooltip`:

- the shared tooltip styling was aligned more closely with the new Bulma-first visual contract
- as a starting point, remove custom local tooltip styling and reintroduce overrides only if they are still clearly needed after the upgrade

#### 4. Reapply only app-local patches

After dependency updates:

- reapply only patches that are truly app-local
- do not keep long-term shared runtime or styling fixes in local patches if the owning `@enso-ui/*` package already exposes the correct surface

If patches were skipped during install, run:

```bash
yarn postinstall
```

#### 5. Migrate frontend stores to Pinia

Local stores should now live under:

- `client/src/js/pinia/*`

Bootstrap should now flow through:

- `client/src/js/pinia/loadState.js`
- `client/src/js/pinia/bootstrapStores.js`

Representative migration pattern:

```js
// before (Vuex)
export const mutations = {
    set: (state, { meta, user, routes, impersonating }) => {
        state.meta = meta;
        state.user = user;
        state.routes = routes;
        state.impersonating = impersonating;
    },
};
```

```js
// after (Pinia)
export const app = defineStore('app', {
    actions: {
        set({ meta, user, routes, impersonating }) {
            this.meta = meta;
            this.user = user;
            this.routes = routes;
            this.impersonating = impersonating;
        },
    },
});
```

Important convention changes:

- use Pinia stores, not Vuex modules
- use package-owned stores directly
- expose `set(payload)` for bootstrapable stores
- stop importing package bootstrap through private `@enso-ui/ui/src/...` paths when the owning package exports it directly

#### 6. Update route and store integration

Local applications should update their route and store bootstrap to match the new package-owned integration model.

In practice:

- update `vite.config.js` for the Vite-based package resolution and build flow
- align `optimizeDeps.include` with the real runtime surface consumed by your app and shared packages; in the upgraded Enso application, this specifically had to include `highlight.js`, `tiny-emitter/instance`, and `vuedraggable` so Vite prebundles their CJS / UMD entrypoints instead of serving raw modules that fail default-import interop
- install app-level peer dependencies required by the shared UI surface; for the upgraded Enso application this included `laravel-echo`, which is required by `@enso-ui/ui` as a peer dependency and must exist in the host app when websocket features are enabled
- update `client/src/js/app.js` for the current runtime bootstrap, icon policy, and global integration surface
- update `client/src/js/enso.js` to remove old compat globals and align the main frontend bootstrap
- update `client/src/js/pinia/loadState.js` and `client/src/js/pinia/bootstrapStores.js` to the new Pinia bootstrap flow
- update local route bootstrap and package route imports to consume the published modules exposed by the owning packages
- update `client/src/sass/app.scss` so it only keeps app-local branding and Bulma token overrides; do not move auth layout styling into the host app
- update `client/src/sass/enso.scss` so it wraps local styles around the shared Enso UI entrypoint instead of reintroducing legacy shared styling layers; at minimum it must import `@enso-ui/ui/src/bulma/styles/enso` and then the local `app.scss`, otherwise guest/auth pages will miss the shared Bulma / Enso base styles and render with broken form layout
- remove remaining assumptions that route and state ownership live implicitly inside `@enso-ui/ui`
- if your application has published Enso config overrides, align local button classes in `config/forms.php` and `config/tables.php` with the new `is-dark` default action style for a more uniform look

#### 7. Migrate backend state providers

Backend state providers should no longer implement a mutation-based contract.

Replace:

```php
ProvidesState::mutation(): string
ProvidesState::state(): mixed
```

with:

```php
ProvidesState::store(): string
ProvidesState::state(): array
```

Expected payload shape:

```php
[
    'store' => 'app',
    'state' => [
        'meta' => [...],
        'user' => [...],
        'routes' => [...],
    ],
]
```

Multiple providers targeting the same store should be merged backend-side before the payload reaches Pinia.

#### 8. Review filter endpoints for the new serializer

The new global Axios serializer now:

- serializes nested objects with bracket notation
- serializes booleans as `1 / 0`
- skips empty arrays
- sends `null` values as empty values

This is especially important for filters.

Backend endpoints must stop relying on:

- JSON strings inside query params
- implicit truthiness of string booleans
- assumptions that nested request keys are always present

Prefer:

- `$request->boolean(...)` when appropriate
- explicit nullable checks for nested structures
- array/object access instead of `json_decode()` for serialized filter payloads

#### 9. Verify build and runtime behavior

Run:

```bash
yarn dev --force
yarn build
```

If needed, clear generated assets before rebuilding:

```bash
rm -rf public/assets public/.vite
yarn build
```

Then validate:

- authentication flow
- left menu and settings panel
- tables and filters
- forms
- uploads and documents
- websocket consumers
- major business pages
- dark theme / light theme / system theme behavior

### Optional Steps

- continue moving any remaining shared fixes from local patches into the owning `@enso-ui/*` packages
- continue cleaning package ownership so applications consume public package surfaces instead of private internals
- continue endpoint-by-endpoint hardening for custom backend filters that still assume legacy query conventions
- continue the shared theme/token cleanup now that the Bulma-native foundation is in place
