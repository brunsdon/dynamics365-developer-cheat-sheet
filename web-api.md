# Web API

The Dynamics 365 / Dataverse Web API is used to interact with data and metadata over HTTP.

## Base URL

```
https://<org>.api.crm<n>.dynamics.com/api/data/v9.2/
```

## Common Uses

- create records
- retrieve records
- update records
- delete records
- query related data
- integrate external applications
- automate administrative or migration tasks

## Authentication

Use OAuth 2.0 with a registered Azure AD app. All requests require a bearer token.

```http
GET /api/data/v9.2/accounts HTTP/1.1
Host: <org>.api.crm6.dynamics.com
Authorization: Bearer <access_token>
OData-MaxVersion: 4.0
OData-Version: 4.0
Accept: application/json
```

Acquiring a token with the client credentials flow:

```http
POST https://login.microsoftonline.com/<tenant_id>/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials
&client_id=<app_id>
&client_secret=<secret>
&scope=https://<org>.crm6.dynamics.com/.default
```

## CRUD Operations

### Create (POST)

```http
POST /api/data/v9.2/accounts
Content-Type: application/json

{
  "name": "Contoso Ltd",
  "telephone1": "+61 2 0000 0000",
  "address1_city": "Sydney"
}
```

Response returns `201 Created` with an `OData-EntityId` header containing the new record URL.

### Retrieve (GET)

```http
GET /api/data/v9.2/accounts(<guid>)?$select=name,telephone1,emailaddress1
```

### Update (PATCH)

```http
PATCH /api/data/v9.2/accounts(<guid>)
Content-Type: application/json

{
  "telephone1": "+61 2 1111 1111"
}
```

Returns `204 No Content` on success.

### Delete (DELETE)

```http
DELETE /api/data/v9.2/accounts(<guid>)
```

## OData Query Examples

```http
# Filter and select
GET /api/data/v9.2/contacts
  ?$select=fullname,emailaddress1
  &$filter=statecode eq 0 and contains(fullname,'Smith')
  &$orderby=fullname asc
  &$top=50

# Expand a lookup (related table)
GET /api/data/v9.2/opportunities(<guid>)
  ?$select=name,estimatedvalue
  &$expand=parentaccountid($select=name,accountnumber)

# Count matching records
GET /api/data/v9.2/accounts?$filter=statecode eq 0&$count=true
```

## Paging

Dataverse returns a maximum of 5000 records per page by default.

```http
# Request next page using the token from @odata.nextLink
GET /api/data/v9.2/contacts?$select=fullname&$top=100

# Response includes:
# "@odata.nextLink": "https://.../contacts?$select=fullname&$skiptoken=..."
```

Always follow `@odata.nextLink` until it is absent.

## Upsert Pattern

```http
PATCH /api/data/v9.2/accounts(<guid>)
Content-Type: application/json
If-None-Match: *
If-Match: *

{
  "name": "Contoso Ltd"
}
```

Use `If-None-Match: *` to create only if not exists, `If-Match: *` to update only if exists, or omit both headers for upsert behaviour.

## Batch Request

Combine multiple operations into one HTTP call to reduce round-trips.

```http
POST /api/data/v9.2/$batch
Content-Type: multipart/mixed; boundary=batch_abc123

--batch_abc123
Content-Type: application/http
Content-Transfer-Encoding: binary

GET /api/data/v9.2/accounts?$select=name&$top=5 HTTP/1.1
Accept: application/json

--batch_abc123
Content-Type: application/http
Content-Transfer-Encoding: binary

PATCH /api/data/v9.2/contacts(<guid>) HTTP/1.1
Content-Type: application/json

{"jobtitle": "Senior Developer"}

--batch_abc123--
```

## Practical Guidance

- use correct logical names
- request only fields you need
- be careful with expands and large result sets
- handle paging
- design integrations for retries and resilience
- avoid chatty patterns where bulk or batched approaches are more suitable

## Common Query Concerns

Watch for:

- missing permissions
- incorrect logical field names
- misunderstanding lookup values
- formatted values versus raw values
- pagination limits
- throttling

### Formatted Values

By default, GET responses return raw values for choices and lookups. Request formatted values with a header:

```http
GET /api/data/v9.2/opportunities?$select=name,statuscode
Prefer: odata.include-annotations="OData.Community.Display.V1.FormattedValue"
```

Response will include `statuscode@OData.Community.Display.V1.FormattedValue: "Won"` alongside the raw integer.

## Integration Advice

When integrating with external systems:

- treat Dataverse as an application platform, not just a generic database
- respect business rules and server logic
- understand ownership and status transitions
- use service principals or approved app registrations where appropriate