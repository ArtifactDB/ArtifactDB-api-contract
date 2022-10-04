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
GET /projects/{project}/permissions
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
GET /projects/{project}/metadata
```

`project` should be a project name, passed through the standard URL encoding.

### Response 

On success, a 200 status code is returned with a JSON body that follows [this schema](https://ArtifactDB.github.io/ArtifactDB-api-contract/html/response/project_metadata.html).

The project version metadata is paginated, so a successful response may contain a [`Link` header](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Link) pointing to the next page.
Specifically, the next page is obtained from the link where `rel="more"` (this is the same as the `next` property).
If no such header or link exists, it can be assumed that no further pages are available.

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
GET /projects/{project}/version/{version}/metadata
```

`project` should be a project name, passed through the standard URL encoding.

`version` should be a project version, passed through the standard URL encoding.

### Response 

On success, a 200 status code is returned with a JSON body that follows [this schema](https://ArtifactDB.github.io/ArtifactDB-api-contract/html/response/project_metadata.html).

The project version metadata is paginated, so a successful response may contain a [`Link` header](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Link) pointing to the next page.
Specifically, the next page is obtained from the link where `rel="more"`. 
If no such header or link exists, it can be assumed that no further pages are available.

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

## List project versions

### Route

```
GET /projects/{project}/versions
```

`project` should be a project name, passed through the standard URL encoding.

### Response 

On success, a 200 status code is returned with a JSON body that follows [this schema](https://ArtifactDB.github.io/ArtifactDB-api-contract/html/response/project_version_listing.html).

On error, a JSON body is returned that follows [this schema](https://ArtifactDB.githubio/ArtifactDB-api-contract/html/response/error.html).

### Example

Listing project versions for `test-zircon-upload` for the test API:

```sh
curl -L https://gypsum-test.aaron-lun.workers.dev/projects/test-zircon-upload/versions
```

```json
{
  "project_id": "test-zircon-upload",
  "aggs": [
    {
      "_extra.version": "1664829775"
    },
    {
      "_extra.version": "1664840427"
    },
    {
      "_extra.version": "base"
    }
  ],
  "total": 8,
  "latest": {
    "_extra.version": "1664840427"
  }
}
```

## List projects

### Route

```
GET /projects
```

### Response 

On success, a 200 status code is returned with a JSON body that follows [this schema](https://ArtifactDB.github.io/ArtifactDB-api-contract/html/response/project_listing.html).

The project list is paginated, so a successful response may contain a [`Link` header](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Link) pointing to the next page.
Specifically, the next page is obtained from the link where `rel="more"`. 
If no such header or link exists, it can be assumed that no further pages are available.

On error, a JSON body is returned that follows [this schema](https://ArtifactDB.githubio/ArtifactDB-api-contract/html/response/error.html).

### Example

Listing all projects for the test API:

```sh
curl -L https://gypsum-test.aaron-lun.workers.dev/projects
```

```json
{
  "results": [
    {
      "project_id": "test-zircon-link",
      "aggs": [
        {
          "_extra.version": "base"
        }
      ]
    },
    {
      "project_id": "test-zircon-upload",
      "aggs": [
        {
          "_extra.version": "1664829775"
        },
        {
          "_extra.version": "1664840427"
        },
        {
          "_extra.version": "base"
        }
      ]
    }
  ],
  "count": 2
}
```

## Get version information

### Route

```
GET /projects/{project}/version/{version}/info
```

`project` should be a project name, passed through the standard URL encoding.

`version` should be a project version, passed through the standard URL encoding.

### Response 

On success, a 200 status code is returned with a JSON body that follows [this schema](https://ArtifactDB.github.io/ArtifactDB-api-contract/html/response/project_version_info.html).

On error, a JSON body is returned that follows [this schema](https://ArtifactDB.githubio/ArtifactDB-api-contract/html/response/error.html).

### Example

Requesting the project metadata for `test-zircon-upload` for the test API:

```sh
curl -L https://gypsum-test.aaron-lun.workers.dev/projects/test-zircon-upload/version/base/info
```

Yields the following 200 response:

```json
{
  "status": "ok",
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
```

## Start version upload

### Route

```
POST /projects/{project}/version/{version}/upload
```

`project` should be a project name, passed through the standard URL encoding.

`version` should be a project version, passed through the standard URL encoding.

### Request body

The request body should be JSON-encoded and follow [this schema](https://ArtifactDB.github.io/ArtifactDB-api-contract/html/request/upload_project_version.html).

### Response 

On success, a 200 status code is returned with a JSON body that follows [this schema](https://ArtifactDB.github.io/ArtifactDB-api-contract/html/response/upload_project_version.html).

On error, a JSON body is returned that follows [this schema](https://ArtifactDB.githubio/ArtifactDB-api-contract/html/response/error.html).

### Examples

We'll consider the following request body:

```json
{ 
    "filenames": [ 
        "foo.txt", 
        { 
            "check": "link", 
            "filename": "blah.txt",
            "value": { 
                "artifactdb_id": "test-zircon-upload:blah.txt@base"
            }
        },
        {
            "check": "md5", 
            "filename": "whee.txt",
            "value": { 
                "field": "md5sum",
                "md5sum": "b7fdd99fac291c4bbf958d9aee731951"
            }
        }
    ],
    "expires_in": "in 1 days"
}
```

Requesting an upload to `test_version` of the project `test-zircon-upload` in the test API:

```sh
body=$(cat request.json) # containing the request body above.
curl -X POST https://gypsum-test.aaron-lun.workers.dev/projects/test-zircon-upload/version/test_version/upload \
    -H "Content-Type: application/json" \
    -H "Authorization: Bearer ${GITHUB_PAT}" \
    -d "${body}"
```

Yields the following 200 response:

```json
{
    "presigned_urls": [
        {
            "filename": "foo.txt",
            "url": "https://gypsum-test.bfb2e522e0b245720424784fcf7c04c0.r2.cloudflarestorage.com/test-zircon-upload/test_version/foo.txt...",
            "md5sum": "cac8766a7c6a76a67aa=="
        }
    ],
    "links": [
        {
            "filename": "blah.txt",
            "url": "/link/dGVzdC16aXJjb24tdXBsb2FkOmJsYWgudHh0QHRlc3RfdmVyc2lvbg==/to/dGVzdC16aXJjb24tdXBsb2FkOmJsYWgudHh0QGJhc2U="
        },
        {
            "filename": "whee.txt",
            "url": "/link/dGVzdC16aXJjb24tdXBsb2FkOndoZWUudHh0QHRlc3RfdmVyc2lvbg==/to/dGVzdC16aXJjb24tdXBsb2FkOndoZWUudHh0QGJhc2U="
        }
    ],
    "completion_url": "/projects/test-zircon-upload/version/test_version/complete",
    "abort_url": "/projects/test-zircon-upload/version/test_version/abort"
}
```

## Complete version upload

### Route

```
PUT /projects/{project}/version/{version}/complete
```

`project` should be a project name, passed through the standard URL encoding.

`version` should be a project version, passed through the standard URL encoding.

### Request body

The request body should be JSON-encoded and follow [this schema](https://ArtifactDB.github.io/ArtifactDB-api-contract/html/request/complete_project_version.html).

### Response 

On success, a 200 status code is returned with a JSON body that follows [this schema](https://ArtifactDB.github.io/ArtifactDB-api-contract/html/response/complete_project_version.html).

On error, a JSON body is returned that follows [this schema](https://ArtifactDB.githubio/ArtifactDB-api-contract/html/response/error.html).

### Example

Completing an upload to `test_version` of the project `test-zircon-upload` in the test API:

```sh
curl -X PUT https://gypsum-test.aaron-lun.workers.dev/projects/test-zircon-upload/version/test_version/complete \
    -H "Content-Type: application/json" \
    -H "Authorization: Bearer ${GITHUB_PAT}" \
    -d '{ "read_access": "public", "owners": [ "LTLA" ], "viewers": [ "ArtifactDB-bot" ] }'
```

Yields the following 200 response:

```json
{
    "job_id": 196
}
```

## Get post-upload job status

### Route

```
GET /jobs/{jobid}
```

`jobid` should be an integer or URL-encoded string.

### Response

On success, a 200 status code is returned with a JSON body that follows [this schema](https://ArtifactDB.github.io/ArtifactDB-api-contract/html/response/jobs.html).

On error, a JSON body is returned that follows [this schema](https://ArtifactDB.githubio/ArtifactDB-api-contract/html/response/error.html).

### Example

Checking the status of a job:

```sh
curl -L https://gypsum-test.aaron-lun.workers.dev/jobs/196
```

Yields the following 200 response:

```json
{
    "status":"SUCCESS",
    "job_url":"https://github.com/ArtifactDB/gypsum-actions/issues/196"
}
```
