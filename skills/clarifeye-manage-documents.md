---
name: Ingest and manage knowledge-store documents
description: Bulk-upload source documents to a Clarifeye knowledge store, poll until they are queryable, and filter them by tag or metadata.
api: openapi/clarifeye-openapi-original.yml
operations: [bulkUploadDocuments, getDocumentIndexingStatus, listDocumentsWithoutFile, filterDocumentsByTag, filterDocumentsByMetadata, deleteDocument]
---

# Ingest and manage knowledge-store documents

Use this to load a project's (knowledge store's) corpus and confirm it is indexed.

## Auth
Send `Authorization: Bearer <token>` (or `Authorization: Token <token>`) on every
request. Base URL is region-specific: `https://eu.app.clarifeye.ai/api/v1` or
`https://us.app.clarifeye.ai/api/v1`. All paths are scoped to `/projects/{project_id}/`.

## Steps
1. **Upload** — `bulkUploadDocuments` (`POST /projects/{project_id}/documents/bulk-upload/`)
   to upload multiple documents in one call. Optionally skip the automatic parsing
   pipeline.
2. **Poll indexing** — `getDocumentIndexingStatus`
   (`GET /projects/{project_id}/documents/indexing-status/`) and repeat until each
   `(document, extractor node)` pair reaches `active` (queryable by AI) or `deleted`.
3. **List** — `listDocumentsWithoutFile`
   (`GET /projects/{project_id}/documents/get-without-file/`) for fast metadata-only
   listing (page-number pagination: read `results`, follow `next`).
4. **Filter** — `filterDocumentsByTag` (`POST .../documents/filter-by-tag/`) or
   `filterDocumentsByMetadata` (`POST .../documents/filter-by-metadata/`). Pass tag
   values as a JSON array to avoid delimiter issues.
5. **Delete** — `deleteDocument` (`DELETE .../documents/{document_id}/`). Deletion is
   asynchronous: the document is marked `deleting` and removed in the background.

## Rules
- Pagination is page-number: `{count, next, previous, results}`; follow `next` URLs.
- Errors return `{"error": "..."}`; 400 validation returns a `{field: [messages]}` map.
  A 403 means admin/permission is required. See errors/clarifeye-problem-types.yml.
