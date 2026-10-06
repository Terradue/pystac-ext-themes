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

# Create an Item with themes

[Install the package](../how-to/install.md), then run this complete example. The vocabulary and concept URIs are illustrative.

```python
import json
from datetime import datetime, timezone

import pystac
from pystac.extensions.themes import Theme, ThemeConcept, ThemesExtension

item = pystac.Item(
    id="example-climate-resource",
    geometry=None,
    bbox=None,
    datetime=datetime(2026, 1, 1, tzinfo=timezone.utc),
    properties={},
)
theme = Theme(
    scheme="https://example.com/themes",
    concepts=[
        ThemeConcept(
            id="climate",
            title="Climate",
            description="Climate-related resources",
            url="https://example.com/concepts/climate",
        )
    ],
)
extension = ThemesExtension.ext(item, add_if_missing=True)
extension.apply([theme])

serialized = item.to_dict()
assert ThemesExtension.get_schema_uri() in serialized["stac_extensions"]
assert serialized["properties"]["themes"] == [theme.to_dict()]

restored_item = pystac.Item.from_dict(json.loads(json.dumps(serialized)))
restored_themes = ThemesExtension.ext(restored_item).themes
assert restored_themes is not None
assert restored_themes[0].concepts[0].id == "climate"
```

`add_if_missing=True` adds the schema URI to `stac_extensions`. `apply()` replaces the theme list in Item properties. Use `ThemesExtension.ext()` again to access an existing object's themes.

Serialization preserves metadata without validating it. Continue with [updates, Collection summaries, and validation](../how-to/use-extension.md).
