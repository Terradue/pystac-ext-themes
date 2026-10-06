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

# Themes PySTAC extension

[![PyPI - Version](https://img.shields.io/pypi/v/pystac-ext-themes.svg)](https://pypi.org/project/pystac-ext-themes)
[![PyPI - Python Version](https://img.shields.io/pypi/pyversions/pystac-ext-themes.svg)](https://pypi.org/project/pystac-ext-themes)
[![GitHub Actions Workflow Status](https://img.shields.io/github/actions/workflow/status/terradue/pystac-ext-themes/package.yaml?branch=develop&event=push&label=build&logo=githubactions)](https://github.com/terradue/pystac-ext-themes/actions/workflows/package.yaml?query=branch%3Adevelop)
[![Code coverage](https://img.shields.io/codecov/c/github/terradue/pystac-ext-themes/develop?logo=codecov)](https://app.codecov.io/gh/terradue/pystac-ext-themes/tree/develop)

PySTAC implementation of the STAC Themes Extension for Catalogs, Collections, Items, and Collection summaries. Group concepts under a vocabulary URI to describe thematic classifications.

## Install and use

Requires Python 3.10 or later and PySTAC `>=1,<2`.

```bash
python -m pip install pystac-ext-themes
```

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

Use `ThemesExtension.ext(obj)` to access themes. The package does not register an `obj.ext.themes` shortcut. Setters and serialization do not perform schema validation.

## Documentation

Read the [project documentation](https://terradue.github.io/pystac-ext-themes/) for Python examples, the field reference, and API documentation.

To preview it locally:

```bash
python -m pip install . "mkdocs<2" mkdocs-material "mkdocstrings[python]"
mkdocs serve
```

## Contribute

Submit a [GitHub issue](https://github.com/Terradue/pystac-ext-themes/issues) if you have comments or suggestions.

### Local quality checks

Install [Hatch](https://hatch.pypa.io/) and [Taskfiles](https://taskfile.dev/docs/guide) then install the Git hook:

```console
task quality:pre-commit:install
```

Every commit runs Ruff (including the configured McCabe complexity limit),
Ruff formatting, strict mypy checks, Bandit, and the pytest suite.

Run the complete hook explicitly with:

```console
task
```

## License

[![Apache License, Version 2.0](https://img.shields.io/badge/license-Apache%20License%202.0-blue)](https://www.apache.org/licenses/LICENSE-2.0)
