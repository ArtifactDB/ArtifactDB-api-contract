# ArtifactDB API contract

## Overview

This document describes the API contract for ArtifactDB instances.
By providing an explicit listing of endpoints, developers can create clients (e.g., [**zircon**](https://github.com/ArtifactDB/zircon-R)) that will interoperate with a range of ArtifactDB APIs.

## Get file metadata

### Route

```
GET /files/{id}/metadata
```

`id` should be an ArtifactDB identifier of the form `<PROJECT>:<PATH>@<VERSION>`.
`id` should be passed through the standard URL encoding.

### Query parameters

`follow_link` may be `true`, in which case the ArtifactDB API should return metadata after following any [redirection schemas](https://github.com/ArtifactDB/BiocObjectSchemas/tree/master/raw/redirection/v1.json).

### Response 

On success, a 200 status code is returned with a JSON body that follows [this schema](https://ArtifactDB.github.io/ArtifactDB-api-contract/html/response/file_metadata.html).

On error, a JSON body is returned that follows [this schema](https://ArtifactDB.githubio/ArtifactDB-api-contract/html/response/error.html).

### Example

Requesting the metadata for `test-zircon-upload:blah.txt@base` for the test API:

```sh
curl -L https://gypsum-test.aaron-lun.workers.dev/files/test-zircon-upload%3Ablah.txt%40base/metadata
```

Yields the following 200 response:

```json
{
  "$schema": "generic_file/v1.json",
  "generic_file": {
    "format": "text"
  },
  "md5sum": "0eb827652a5c272e1c82002f1c972018",
  "path": "blah.txt",
  "_extra": {
    "$schema": "generic_file/v1.json",
    "id": "test-zircon-upload:blah.txt@base",
    "project_id": "test-zircon-upload",
    "version": "base",
    "metapath": "blah.txt",
    "meta_indexed": "2022-10-01T19:45:51.913Z",
    "meta_uploaded": "2022-10-01T19:45:34.442Z",
    "uploaded": "2022-10-01T19:45:34.442Z",
    "permissions": {
      "scope": "project",
      "read_access": "public",
      "write_access": "owners",
      "owners": [
        "ArtifactDB-bot"
      ],
      "viewers": []
    }
  }
}
```

## Get file contents

### Route

```
GET /files/{id}/metadata
```

`id` should be an ArtifactDB identifier of the form `<PROJECT>:<PATH>@<VERSION>`.
`id` should be passed through the standard URL encoding.

### Response 

On success, a 302 status code is returned that redirects to a presigned URL.

On error, a JSON body is returned that follows [this schema](https://ArtifactDB.githubio/ArtifactDB-api-contract/html/response/error.html).

### Example

Requesting the contents of `test-zircon-upload:blah.txt@base` for the test API:

```sh
curl -v https://gypsum-test.aaron-lun.workers.dev/files/test-zircon-upload%3Ablah.txt%40base
```

Gives the following 302 response (abbreviated):

```
< HTTP/1.1 302 Found
< Date: Mon, 03 Oct 2022 16:34:56 GMT
< Content-Length: 0
< Connection: keep-alive
< Location: https://gypsum-test.bfb2e522e0b245720424784fcf7c04c0.r2.cloudflarestorage.com/test-zircon-upload/base/blah.txt?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=ae0f6ee0f014deff554f709698940ab3%2F20221003%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20221003T163456Z&X-Amz-Expires=120&X-Amz-Signature=4290ec4a3856137c024066524b93e3d49f6b0a384aa13fc1d9d1a89ad7407491&X-Amz-SignedHeaders=host
< Report-To: {"endpoints":[{"url":"https:\/\/a.nel.cloudflare.com\/report\/v3?s=wYKjthA4wgVGA3HfmcEiOPRqilhYHd2DKuYJ6TU0pcs%2FtaRIqFog2FEaLLCRzS7a%2BmIZuPUGViNkZNsZedhc5UMnpI6JkLEbENNktcfzR%2BQ%2FAhSxALGioSRZd%2BUOc4fWA6aZXZ5rqcG6koGzqdoDBayo7Kw%3D"}],"group":"cf-nel","max_age":604800}
< NEL: {"success_fraction":0,"report_to":"cf-nel","max_age":604800}
< Server: cloudflare
< CF-RAY: 7547168e6bd29682-SJC
< alt-svc: h3=":443"; ma=86400, h3-29=":443"; ma=86400
```

## Get project permissions

### Route

```
GET /project/{project}/permissions
```

`project` should be a project name, passed through the standard URL encoding.

### Response 

On success, a 200 status code is returned with a JSON body that follows [this schema](https://ArtifactDB.github.io/ArtifactDB-api-contract/html/response/permissions.html).

On error, a JSON body is returned that follows [this schema](https://ArtifactDB.githubio/ArtifactDB-api-contract/html/response/error.html).

### Example

Requesting the permissions for `test-zircon-upload` for the test API:

```sh
curl -L https://gypsum-test.aaron-lun.workers.dev/projects/test-zircon-upload/permissions
```

Yields the following 200 response:

```json
{
  "scope": "project",
  "read_access": "public",
  "write_access": "owners",
  "owners": [
    "ArtifactDB-bot"
  ],
  "viewers": []
}
```

## Get project metadata

### Route

```
GET /project/{project}/metadata
```

`project` should be a project name, passed through the standard URL encoding.

### Response 

On success, a 200 status code is returned with a JSON body that follows [this schema](https://ArtifactDB.github.io/ArtifactDB-api-contract/html/response/project_metadata.html).

On error, a JSON body is returned that follows [this schema](https://ArtifactDB.githubio/ArtifactDB-api-contract/html/response/error.html).

### Example

Requesting the project metadata for `test-zircon-upload` for the test API:

```sh
curl -L https://gypsum-test.aaron-lun.workers.dev/projects/test-zircon-upload/metadata
```

Yields the following 200 response (per-file details omitted for brevity):

```json
{
  "results": [
    {
      "$schema": "generic_file/v1.json",
      "path": "blah.txt",
      "_extra": {}
    },
    {
      "$schema": "generic_file/v1.json",
      "path": "foo/bar.txt",
      "_extra": {}
    },
    {
      "$schema": "generic_file/v1.json",
      "path": "whee.txt",
      "_extra": {}
    }
  ],
  "count": 12,
  "total": 12
}
```

## Get project version metadata

### Route

```
GET /project/{project}/version/{version}/metadata
```

`project` should be a project name, passed through the standard URL encoding.

`version` should be a project version, passed through the standard URL encoding.

### Response 

On success, a 200 status code is returned with a JSON body that follows [this schema](https://ArtifactDB.github.io/ArtifactDB-api-contract/html/response/project_metadata.html).

On error, a JSON body is returned that follows [this schema](https://ArtifactDB.githubio/ArtifactDB-api-contract/html/response/error.html).

### Example

Requesting the project metadata for `test-zircon-upload` for the test API:

```sh
curl -L https://gypsum-test.aaron-lun.workers.dev/projects/test-zircon-upload/version/base/metadata
```

Yields the following 200 response (per-file metadata omitted for brevity):

```json
{
  "results": [
    {
      "$schema": "generic_file/v1.json",
      "path": "blah.txt",
      "_extra": {}
    },
    {
      "$schema": "generic_file/v1.json",
      "path": "foo/bar.txt",
      "_extra": {}
    },
    {
      "$schema": "generic_file/v1.json",
      "path": "whee.txt",
      "_extra": {}
    }
  ],
  "count": 12,
  "total": 12
}
```
