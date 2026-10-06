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

# Simplify EarthCODE theme metadata

The [EarthCODE PySTAC tutorial's helper functions](https://esa-earthcode.github.io/tutorials/osc-pr-pystac/#define-helper-functions-add-new-variables-theme-and-eo-missions) include an `add_themes` helper that checks theme IDs against the Open Science Catalog, adds `related` links, and constructs the `themes` metadata by hand.

`Theme`, `ThemeConcept`, and `ThemesExtension` provide a typed, reusable metadata API and automatic extension declaration. Catalog checks and links remain application responsibilities.

## Prerequisites

[Install this package](../how-to/install.md), then follow the EarthCODE tutorial to load the OSC catalog and create a `pystac.Collection`. The examples below use its existing `collection` and `themes` catalog variables.

## Replace the metadata assignment

For IDs that your application has already checked, replace the manual theme dictionaries and `collection.extra_fields.update(...)` call with:

```python
from pystac.extensions.themes import Theme, ThemeConcept, ThemesExtension

themes_to_add = ["land"]

ThemesExtension.ext(collection, add_if_missing=True).apply(
    [
        Theme(
            scheme="https://github.com/stac-extensions/osc#theme",
            concepts=[ThemeConcept(id=theme_id)],
        )
        for theme_id in themes_to_add
    ]
)
```

This preserves one `Theme` per ID, each containing one concept. `apply()` replaces the Collection's `themes` list. `add_if_missing=True` adds the Themes schema URI to `stac_extensions` if absent, so you can omit the manually listed Themes URI in the Collection constructor while retaining other extension declarations.

## Keep catalog checks and related links

Use this complete replacement helper when integrating with the OSC catalog:

```python
from collections.abc import Sequence

import pystac
from pystac.extensions.themes import Theme, ThemeConcept, ThemesExtension

OSC_THEME_SCHEME = "https://github.com/stac-extensions/osc#theme"


def add_themes(
    collection: pystac.Collection,
    themes_to_add: Sequence[str],
    themes_catalog: pystac.Catalog,
) -> None:
    """Replace theme metadata and append related links on a Collection.

    Args:
        collection: Collection to update in place.
        themes_to_add: IDs of direct children of the OSC themes catalog.
        themes_catalog: Catalog containing the allowed themes.

    Raises:
        ValueError: If a theme is unknown or has no self link. The Collection
            remains unchanged in either case.
    """
    theme_metadata = []
    links = []
    for theme_id in themes_to_add:
        theme_catalog = themes_catalog.get_child(theme_id)
        if theme_catalog is None:
            raise ValueError(f"Unknown OSC theme: {theme_id}")

        href = theme_catalog.get_self_href()
        if href is None:
            raise ValueError(f"OSC theme has no self link: {theme_id}")

        theme_metadata.append(
            Theme(
                scheme=OSC_THEME_SCHEME,
                concepts=[ThemeConcept(id=theme_id)],
            )
        )
        links.append(
            pystac.Link(
                rel="related",
                target=href,
                media_type="application/json",
                title=f"Theme: {theme_catalog.title}",
            )
        )

    ThemesExtension.ext(collection, add_if_missing=True).apply(theme_metadata)
    collection.add_links(links)
```

Pass the `themes` catalog loaded earlier in the EarthCODE tutorial explicitly:

```python
if themes is None:
    raise ValueError("The OSC catalog has no themes catalog")

add_themes(collection, ["land"], themes)
```

The helper resolves each requested child and checks its self link before changing the Collection. Repeated calls replace metadata but append links; use it once per newly created Collection, or manage existing theme links when updating one.

## Inspect the result

```python
serialized = collection.to_dict()
assert ThemesExtension.get_schema_uri() in serialized["stac_extensions"]
assert serialized["themes"] == [
    {
        "scheme": OSC_THEME_SCHEME,
        "concepts": [{"id": "land"}],
    }
]

stored_themes = ThemesExtension.ext(collection).themes
assert stored_themes is not None
assert stored_themes[0].concepts[0].id == "land"
```

The extension serializes the metadata and declares the schema; it does not validate OSC membership or create links. Continue with the EarthCODE tutorial's validation and saving steps, and see [validation guidance](../how-to/use-extension.md) for schema validation requirements.
