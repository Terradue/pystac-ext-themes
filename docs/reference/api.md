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

# Python API

```python
from pystac.extensions.themes import Theme, ThemeConcept, ThemesExtension
```

| Entry point | Behavior |
| --- | --- |
| `ThemesExtension.ext(obj, add_if_missing=False)` | Wrap a Catalog, Collection, or Item. |
| `ThemesExtension.summaries(collection, add_if_missing=False)` | Wrap Collection summaries. |
| `extension.apply(themes)` | Replace the object's themes with a list of `Theme` values. |
| `extension.themes` | Read, replace, or clear themes using `None`. |
| `ThemesExtension.get_schema_uri()` | Return the Themes v1.0.0 schema URL. |
| `ThemesExtension.has_extension(obj)` | Check for that URL in `stac_extensions`. |
| `ThemesExtension.add_to(obj)` | Add the schema URL if absent. |
| `ThemesExtension.remove_from(obj)` | Remove the schema declaration. |
| `Theme.to_dict()` / `Theme.from_dict(data)` | Serialize or deserialize a theme. |
| `ThemeConcept.to_dict()` / `ThemeConcept.from_dict(data)` | Serialize or deserialize a concept. |

`ext()` and `summaries()` raise `pystac.ExtensionNotImplemented` when the declaration is missing unless `add_if_missing=True`. `ext()` raises `pystac.ExtensionTypeError` for unsupported objects.

Use `ThemesExtension.ext(obj)` directly. PySTAC does not provide `item.ext.themes` or `catalog.ext.themes`, and this package does not register those shortcuts. There is no `from_item()` alias.

::: pystac.extensions.themes
    options:
      members:
        - ThemeConcept
        - Theme
        - ThemesExtension
        - CatalogThemesExtension
        - CollectionThemesExtension
        - ItemThemesExtension
        - SummariesThemesExtension
        - ThemesExtensionHooks
        - SCHEMA_URI
        - THEMES_PROP
        - THEMES_EXTENSION_HOOKS
      inherited_members: false
      show_root_heading: true
      show_signature_annotations: true
