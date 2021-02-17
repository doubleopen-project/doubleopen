# SPDX to ORT Evaluator

To use ORT's Evaluator for SPDX documents, we'd need to convert SPDX 2.2 to ORT's data
model. In addition to ORT's own data model ORT includes a model for
[SpdxDocument](https://github.com/oss-review-toolkit/ort/blob/40961ed08e88701533067fbde410f9ac073ff9a6/spdx-utils/src/main/kotlin/model/SpdxDocument.kt#L36-L36)
, for which ORT supports deserialization from JSON, so the conversion should be done
utilizing these, perhaps as a part of the ORT Helper CLI?

## SPDX from Yocto

![SPDX Format](https://raw.githubusercontent.com/doubleopen-project/meta-doubleopen/main/spdx.mmd.svg)

## Analyzer Result

### Projects

Create a project describing the software project built using Yocto.

```json
{
  "id" : "NPM::test:1.0.0",
  "definition_file_path" : "",
  "declared_licenses" : [ "ISC" ],
  "declared_licenses_processed" : {
    "spdx_expression" : "ISC"
  },
  "vcs" : {
    "type" : "",
    "url" : "",
    "revision" : "",
    "path" : ""
  },
  "vcs_processed" : {
    "type" : "",
    "url" : "",
    "revision" : "",
    "path" : ""
  },
  "homepage_url" : "",
  "scopes" : [ {
    "name" : "dependencies",
    "dependencies" : [ {
      "id" : "Yocto::package_1:0.12.0"
    }, {
      "id" : "NPM::json-stringify-safe:5.0.1"
    } ]
  }, {
    "name" : "devDependencies",
    "dependencies" : [ ]
  } ]
}
```

ORT fields needed:

- id
  - Probably from the document's document name, `Yocto::project:1.0.0`. Need to parse
    the version and name to their own fields from document name.
    - We currently don't store the root project as a package in SPDX. If we did, we could
      use the name and version from it.
- scopes
  - Sub-packages of recipes that are not packaged into the final image could be separated
    from deployed packages here. Can scope only be used for excludes, or can scope be
    taken into account in the policy? Maybe one scope for the Yocto recipes, one scope
    for sub-packages that are deployed with the image and one scope for sub-packages that
    are not deployed with the image?

### Packages

Create a package to describe all packages built with Yocto. Maybe include sub-packages that are
included in the final image (relationship PACKAGE_OF between the sub-package and the image in the
SPDX) in one scope, those that are not included in the image (no such relationship) in other scope
and the source packages corresponding to whole recipes in one scope.

#### ORT

```json
{
  "package" : {
    "id" : "Yocto::package_1:0.12.0",
    "purl" : "pkg:yocto/package_1@0.12.0",
    "declared_licenses" : [ "Apache-2.0" ],
    "declared_licenses_processed" : {
      "spdx_expression" : "Apache-2.0"
    },
    "description" : "",
    "homepage_url" : "",
    "binary_artifact" : {
      "url" : "",
      "hash" : {
        "value" : "",
        "algorithm" : ""
      }
    },
    "source_artifact" : {
      "url" : "",
      "hash" : {
        "value" : "",
        "algorithm" : ""
      }
    },
    "vcs" : {
      "type" : "",
      "url" : "",
      "revision" : "",
      "path" : ""
    },
    "vcs_processed" : {
      "type" : "",
      "url" : "",
      "revision" : "",
      "path" : ""
    }
  },
  "curations" : [ ]
}
```

ORT fields needed:

- id
  - Build from SPDX's package information's package name and version. Add Yocto or some
    other prefix.
- declared_licenses and declared_licenses_processed
  - We save the declared license from
  Yocto in the SPDX. According to
  [ProcessedDeclaredLicense](https://github.com/oss-review-toolkit/ort/blob/9d14320d2d5bba0d86cdf68189f6c987acbc9c7e/model/src/main/kotlin/Project.kt#L60-L63)
  only AND expressions are supported here.
- scopes
  - Sub-packages of recipes that are not packaged into the final image could be separated
    from deployed packages here. Can scope only be used for excludes, or can scope be
    taken into account in the policy? Maybe one scope for the Yocto recipes, one scope
    for sub-packages that are deployed with the image and one scope for sub-packages that
    are not deployed with the image?

## Scanner Result

We're saving the license data from Fossology to the SPDX, so this should be saved as
scanner result.

### Result for Package

Save license and copyright information of the packages from the SPDX document as ORT's
scanner result.

Two types of files need to be saved from SPDX. The files packaged in sub-packages for deployment
may be binaries or other files. Other files should be added to ORT directly, while for binaries we
need to add the corresponding source files.

We're saving both the scanner findings from Fossology and the concluded license experssion
for each file in the SPDX document. For files' licensing info, ORT's [LicenseFinding](https://github.com/oss-review-toolkit/ort/blob/bd88d4bba90c3ed444555fe651954f15d58a3a8b/model/src/main/kotlin/LicenseFinding.kt#L31-L41)
does not seem to allow distinction between scanner results and manual conclusions.

Options:

1. If no conclusion has been made for the file, save scanner results as individual
   LicenseFindings. If conclusion for the file exists, create LicenseFinding for the
   SPDX expression. Does not allow the Evaluator to differentiate between scanner results
   and human conclusions.
2. Save scanner findings as LicenseFindings. If conclusions exist, generate curation for
   the file. Can Evaluator access the scanner findings with this approach? If not, doesn't
   seem to provide value over option 1.

#### ORT

```json
{
  "id" : "Yocto::package_1:0.12.0",
  "results" : [ {
    "provenance" : {
      "download_time" : "2021-02-15T14:46:41.042910Z",
      "vcs_info" : {
        "type" : "",
        "url" : "",
        "revision" : "",
        "resolved_revision" : "",
        "path" : ""
      },
      "original_vcs_info" : {
        "type" : "",
        "url" : "",
        "revision" : "",
        "path" : ""
      }
    },
    "scanner" : {
      "name" : "",
      "version" : "",
      "configuration" : ""
    },
    "summary" : {
      "start_time" : "2021-02-15T14:46:41.042910Z",
      "end_time" : "2021-02-15T14:46:41.042910Z",
      "file_count" : 5,
      "package_verification_code" : "",
      "licenses" : [ {
        "license" : "Apache-2.0",
        "location" : {
          "path" : "LICENSE",
          "start_line" : 1,
          "end_line" : 28
        }
      }, {
        "license" : "Apache-2.0",
        "location" : {
          "path" : "package.json",
          "start_line" : 20,
          "end_line" : 20
        }
      } ],
      "copyrights" : [ {
        "statement" : "Copyright (c) Isaac Z. Schlueter and Contributors",
        "location" : {
          "path" : "LICENSE",
          "start_line" : 3,
          "end_line" : 3
        }
      } ]
    }
  } ]
}
```

## ORT's policy

[Policy logic is written in Kotlin Script.](https://github.com/oss-review-toolkit/ort/blob/master/examples/rules.kts)

[License classifications are written in YAML.](https://github.com/oss-review-toolkit/ort/blob/master/examples/license-classifications.yml)

## Multiple levels of policy

TODO

## Reporting

TODO
