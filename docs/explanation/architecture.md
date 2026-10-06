<!--
Copyright 2026 Terradue

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
-->

# Scope, architecture, and validation

## Thematic discovery

Themes associate STAC resources with concepts from a vocabulary or knowledge organization system. Each theme identifies a scheme and lists concepts within it. This package stores those associations; it does not resolve vocabularies or classify resources automatically.

## Package structure

The distribution is `pystac-ext-themes`; its API lives in `pystac.extensions.themes`. The wheel excludes the shared `pystac.extensions` initializer owned by PySTAC.

`ThemesExtension` combines PySTAC's `PropertiesExtension` and `ExtensionManagementMixin`. Its concrete wrappers reference Item `properties` or Catalog/Collection `extra_fields`. Assigning themes updates the underlying STAC object immediately. Reading themes creates new `Theme` and `ThemeConcept` values, so edits to those values must be assigned back.

`SummariesThemesExtension` handles Collection summaries separately. No automatic aggregation from child Items occurs. Assets and item asset definitions are unsupported.

Use the class entry point `ThemesExtension.ext(obj)`. This package does not modify PySTAC's built-in extension accessors. `THEMES_EXTENSION_HOOKS` declares the supported schema and object types, but importing the module does not register hooks with PySTAC.

## Schema identity

The extension declares this versioned schema URI:

```text
https://stac-extensions.github.io/themes/v1.0.0/schema.json
```

Use `ThemesExtension.get_schema_uri()` when you need it in code. `has_extension()` checks exact membership in `stac_extensions`.

## Validation boundaries

Python annotations describe accepted values but do not enforce them at runtime. Serialization and assignment do not check schema constraints, resolve concept URLs, or verify membership in a vocabulary. Deserialization expects the required `scheme`, `concepts`, and concept `id` keys.

A missing theme field reads as `None`. Clearing it retains the extension declaration, which may leave an object that needs further editing before schema validation succeeds. Use PySTAC validation separately, as shown in the [how-to guide](../how-to/use-extension.md#validate-an-item).
