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

# Work with themes

These examples continue from the Item and `theme` created in the [tutorial](../tutorials/first-steps.md).

## Update or clear themes

```python
extension = ThemesExtension.ext(item)
themes = extension.themes
assert themes is not None
themes[0].concepts[0].title = "Climate science"
extension.themes = themes
assert item.properties["themes"][0]["concepts"][0]["title"] == "Climate science"

extension.themes = None
assert "themes" not in item.properties
```

Reading themes creates detached values. Assign them back after editing. Setting `None` removes the field but retains the extension declaration.

## Catalogs, Collections, and summaries

```python
catalog = pystac.Catalog(id="catalog", description="Thematic resources")
ThemesExtension.ext(catalog, add_if_missing=True).apply([theme])
assert catalog.extra_fields["themes"] == [theme.to_dict()]

collection = pystac.Collection(
    id="collection",
    description="Climate resources",
    extent=pystac.Extent(
        pystac.SpatialExtent([[-180, -90, 180, 90]]),
        pystac.TemporalExtent([[item.datetime, None]]),
    ),
    license="proprietary",
)
ThemesExtension.ext(collection, add_if_missing=True).apply([theme])
summaries = ThemesExtension.summaries(collection)
summaries.themes = [theme]
assert collection.extra_fields["themes"] == [theme.to_dict()]
assert collection.summaries.to_dict()["themes"] == [theme.to_dict()]

summaries.themes = None
assert "themes" not in collection.summaries.to_dict()
```

A Collection's own themes and its summaries are independent. The package does not aggregate themes from child Items automatically.

## Manage extension membership

```python
ThemesExtension.add_to(item)
assert ThemesExtension.has_extension(item)
ThemesExtension.ext(item).themes = None
ThemesExtension.remove_from(item)
assert not ThemesExtension.has_extension(item)
```

Clear theme metadata explicitly before removing the declaration when you want both removed.

## Validate an Item

Install PySTAC's validation dependencies:

```bash
python -m pip install "pystac[validation]>=1,<2"
```

Populate themes before validating:

```python
ThemesExtension.ext(item, add_if_missing=True).apply([theme])
item.validate()
```

Validation may fetch schemas over the network. `apply()`, property assignment, and serialization do not invoke validation. See [validation boundaries](../explanation/architecture.md#validation-boundaries).
