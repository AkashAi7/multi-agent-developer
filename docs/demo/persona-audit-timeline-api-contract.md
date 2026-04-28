# Persona Audit Timeline API Contract

## 1. Endpoint contract

**Endpoint**

`GET /api/v1/persona-audit-events`

**Purpose**

Return recent-first audit timeline entries for persona changes visible to explicitly authorized operations roles. This is the only read endpoint because the UI needs one list view with bounded filtering and pagination, and the backend must enforce authorization, retention, masking, and audit-access logging in one place.

**Behavior**

- Deny by default unless the caller matches an approved operations role and tenant or scope rule.
- Return only records within the last 90 days after server-side retention filtering.
- Sort by `occurredAt` descending, then `eventId` descending for stable recent-first pagination.
- Support bounded pagination with a server-issued continuation token.
- Apply the approved masking matrix in the backend before returning data.
- Emit mandatory audit-access telemetry for both allowed and denied requests.

**Query parameters**

- `personaId` optional string; filters to one persona.
- `from` optional RFC 3339 timestamp; inclusive lower bound.
- `to` optional RFC 3339 timestamp; inclusive upper bound.
- `pageSize` optional integer; default `50`, max `100`.
- `cursor` optional opaque string for continuation.

**Assumptions**

- Authentication is already established upstream.
- Authorization is enforced by the backend API, not the client.
- Tenant or scope is derived from caller context and optionally narrowed by request filters, never widened by them.

## 2. Request schema

**HTTP request**

```http
GET /api/v1/persona-audit-events?personaId={string}&from={date-time}&to={date-time}&pageSize={1-100}&cursor={opaque}
X-Correlation-Id: {string}
```

**Validation rules**

- `personaId` must reference a persona within the caller's allowed tenant or scope.
- `from` and `to` must be valid timestamps.
- If both are provided, `from <= to`.
- Requested range must be truncated server-side to the allowed 90-day retention window.
- `pageSize` outside `1..100` is rejected.
- `cursor` is mutually exclusive with changing other filters from the original paged request.
- Missing `X-Correlation-Id` may be server-generated, but one must exist in telemetry and response metadata.

## 3. Response schema

**200 OK**

```json
{
  "data": [
    {
      "eventId": "evt_01JV7Y7N4P6V2Q8J9K3M4N5P6Q",
      "personaId": "prs_12345",
      "personaDisplayName": "Support Assistant",
      "occurredAt": "2026-04-20T16:42:11Z",
      "actor": {
        "actorType": "user",
        "displayName": "a***@contoso.com",
        "identifierMasked": "aad:8f***21"
      },
      "changeSummary": {
        "source": "structured-diff",
        "text": "Instructions and escalation policy updated",
        "fieldsChanged": [
          "instructions",
          "escalationPolicy"
        ]
      },
      "changeRisk": "moderate",
      "traceability": {
        "changeRequestId": "crq_789",
        "deploymentId": "dep_456",
        "sourceRecordRef": "audit:01JV7Y7..."
      },
      "tenantScope": {
        "tenantIdMasked": "tn***42",
        "scopeIdMasked": "ops***eu"
      }
    }
  ],
  "page": {
    "pageSize": 50,
    "nextCursor": "opaque-token",
    "hasMore": true
  },
  "appliedFilters": {
    "personaId": "prs_12345",
    "from": "2026-03-01T00:00:00Z",
    "to": "2026-04-20T23:59:59Z",
    "retentionStart": "2026-01-21T00:00:00Z",
    "retentionTruncated": false
  },
  "meta": {
    "correlationId": "3db0ef38-0a67-4f58-9a1e-0dba1f7df4b9",
    "resultCount": 1,
    "generatedAt": "2026-04-20T16:42:12Z"
  }
}
```

**Field notes**

- `changeSummary` must be derived from structured diffs or a curated read model. Raw activity-log text is not allowed unless normalized into this contract.
- `actor.displayName`, `actor.identifierMasked`, tenant or scope fields, free text, and any changed values must follow the approved masking matrix.
- `traceability` is included because the security handoff marked record-level traceability metadata as a likely requirement; exact required subfields remain open.
- Empty state uses `200 OK` with `data: []`, `hasMore: false`, and the applied filters echoed back.

## 4. Error contract

**Error envelope**

```json
{
  "error": {
    "code": "AUDIT_ACCESS_DENIED",
    "message": "The caller is not authorized to access persona audit history.",
    "details": [
      {
        "field": "personaId",
        "issue": "out-of-scope"
      }
    ],
    "correlationId": "3db0ef38-0a67-4f58-9a1e-0dba1f7df4b9",
    "retryable": false
  }
}
```

**Status codes**

- `200 OK` successful read, including empty result sets.
- `400 Bad Request` invalid filter shape, invalid date range, invalid page size, or malformed cursor.
- `401 Unauthorized` no valid authenticated caller context.
- `403 Forbidden` authenticated caller lacks an approved role, claim, tenant, or scope.
- `404 Not Found` optional if the product chooses to hide persona existence; otherwise prefer `403` for out-of-scope persona access.
- `409 Conflict` cursor does not match the active filter set.
- `429 Too Many Requests` rate limited.
- `500 Internal Server Error` unexpected backend failure.

## 5. Compatibility and security notes

- Versioning starts at `/v1`; additive response fields are backward-compatible, but removing or renaming fields requires a new version.
- Use one read endpoint unless product later needs record detail expansion that cannot be represented in the list contract.
- Cursor pagination is preferred over offset pagination to preserve recent-first ordering under concurrent writes.
- Backend must log audit access for both successful and denied requests with at least caller identity, authorization outcome, normalized filters, result count, retention truncation, correlation ID, tenant or scope, and anomaly indicators.
- Backend-only masking means clients must never receive unmasked identifiers, free text, indirect identifiers, or changed values that violate the approved masking matrix.
- Implementation sign-off is blocked until allowed roles or claims and tenant or scope rules are finalized.
- Storage must prove bounded 90-day recent-first query support at the target latency for up to `100` records per page.
- If mandatory traceability metadata cannot be guaranteed per record, the API should fail closed for incomplete records or omit them under a documented policy approved by security.

## 6. Downstream handoff artifact

**Implementation-facing handoff**

- Build one backend-owned read API at `GET /api/v1/persona-audit-events` with deny-by-default authorization.
- Enforce recent-first cursor pagination, `pageSize <= 100`, and server-side 90-day retention truncation.
- Generate `changeSummary` from structured diffs or a curated read model only.
- Apply the approved masking matrix before serialization and before access-log persistence.
- Log mandatory audit-access telemetry on every call, including denied attempts.
- Confirm exact roles or claims, tenant or scope enforcement, change-summary source, storage query capability, and required traceability fields before implementation sign-off.