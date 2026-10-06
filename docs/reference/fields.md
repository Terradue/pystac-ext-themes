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

# Theme fields

The extension uses the unprefixed JSON field `themes`. Each entry groups concepts from one scheme. The package declares the [Themes v1.0.0 schema](https://stac-extensions.github.io/themes/v1.0.0/schema.json).

| JSON field | Python attribute | Python type | Meaning |
| --- | --- | --- | --- |
| `themes` | `ThemesExtension.themes` | `list[Theme] \| None` | The object's thematic classifications. |
| `scheme` | `Theme.scheme` | `str` | URI identifying the vocabulary or knowledge organization system. |
| `concepts` | `Theme.concepts` | `list[ThemeConcept]` | Concepts selected from that scheme. |
| `id` | `ThemeConcept.id` | `str` | Identifier of a concept within the scheme. |
| `title` | `ThemeConcept.title` | `str \| None` | Optional human-readable title. |
| `description` | `ThemeConcept.description` | `str \| None` | Optional description of the concept. |
| `url` | `ThemeConcept.url` | `str \| None` | Optional link describing the concept. |

`ThemeConcept.to_dict()` omits optional values set to `None`. `Theme.from_dict()` and `ThemeConcept.from_dict()` require their mandatory keys; they do not perform schema validation or preserve unrecognized fields.

## Storage

Items store `themes` inside `properties`. Catalogs and Collections store it at the top level through `extra_fields`. `ThemesExtension.summaries(collection)` separately accesses `collection.summaries["themes"]`.

A missing or JSON-null theme field reads as `None`. Assigning `None` removes the field; assigning `[]` stores an empty list. These storage behaviors do not establish schema validity.

Getters deserialize new Python objects. After changing a returned theme or concept, assign the list back to the wrapper to persist the edit. Assignments serialize values immediately and preserve unrelated STAC properties.
