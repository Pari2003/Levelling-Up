# Deliverable 3: REST API & Integration Deep-Dive

> [!TIP]
> As an integration/support engineer, you'll spend most of your day working with APIs — integrating the platform with customer HRIS/ATS systems, debugging failed syncs, and building custom workflows. This is probably the MOST important deliverable for your interview.

---

## Part A: REST API Fundamentals

### HTTP Methods — When to Use Each

| Method | Purpose | Has Body? | Idempotent? | Example |
|:-------|:--------|:----------|:------------|:--------|
| `GET` | Read/retrieve data | No | Yes | `GET /api/candidates/123` |
| `POST` | Create new resource | Yes | No | `POST /api/candidates` with JSON body |
| `PUT` | Replace entire resource | Yes | Yes | `PUT /api/candidates/123` replaces all fields |
| `PATCH` | Update partial resource | Yes | Yes | `PATCH /api/candidates/123` updates only sent fields |
| `DELETE` | Remove resource | No | Yes | `DELETE /api/candidates/123` |

**Idempotent** = calling it multiple times has the same effect as calling it once. GET, PUT, DELETE are idempotent. POST is not (calling POST twice creates two records).

### HTTP Status Codes (MUST Memorize)

```
2xx — SUCCESS
 200 OK       → Request succeeded, response body has data
 201 Created    → Resource created successfully (after POST)
 204 No Content   → Success but no body (common for DELETE)

3xx — REDIRECTION
 301 Moved Permanently → Resource URL changed permanently
 304 Not Modified   → Cached version is still valid

4xx — CLIENT ERROR (YOUR fault / the request is wrong)
 400 Bad Request  → Malformed request, invalid JSON, missing required field
 401 Unauthorized  → No auth credentials or invalid/expired token
 403 Forbidden   → Valid auth but insufficient permissions
 404 Not Found   → Resource doesn't exist at this URL
 405 Method Not Allowed → Using POST when only GET is supported
 409 Conflict    → Resource conflict (e.g., duplicate email)
 422 Unprocessable → Valid JSON but fails business validation
 429 Too Many Requests → Rate limit exceeded

5xx — SERVER ERROR (THEIR fault / the server broke)
 500 Internal Server Error → Generic server crash
 502 Bad Gateway  → Upstream server returned invalid response
 503 Service Unavailable → Server is down or overloaded
 504 Gateway Timeout → Upstream server didn't respond in time
```

> [!IMPORTANT]
> **Interview killer question:** "A customer reports their API integration is failing with a 401 error. What do you do?"
> **Answer:** "401 means authentication failed. I'd check: (1) Is the API key/token present in the Authorization header? (2) Has it expired? (3) Is it being sent in the correct format (Bearer vs Basic)? (4) Was the key recently rotated on the customer side without updating the platform's config?"

### Request/Response Anatomy

```
REQUEST:
POST /api/v1/candidates HTTP/1.1    ← Method + Path + Version
Host: api.example.com         ← Required header
Authorization: Bearer eyJhbGci...   ← Auth token
Content-Type: application/json     ← Tells server the body format
Accept: application/json        ← Tells server what format you want back

{                   ← Request Body (JSON)
  "name": "Alice",
  "email": "alice@example.com",
  "skills": ["Python", "SQL"]
}
```

```
RESPONSE:
HTTP/1.1 201 Created          ← Status code
Content-Type: application/json     ← Body format
X-RateLimit-Remaining: 99       ← Custom header (rate limit info)

{                   ← Response Body
  "id": "cand_abc123",
  "name": "Alice",
  "email": "alice@example.com",
  "created_at": "2026-07-08T14:00:00Z"
}
```

### Authentication Patterns

```
1. API Key (Simplest)
  Header: X-API-Key: abc123def456
  OR in URL: /api/data?api_key=abc123def456 (less secure)

2. Bearer Token / JWT
  Header: Authorization: Bearer eyJhbGciOiJIUzI1NiIs...
  - Token has expiry, needs refresh
  - JWT contains encoded user info (header.payload.signature)

3. OAuth 2.0 (Enterprise standard)
  Step 1: Get authorization code
  Step 2: Exchange code for access_token + refresh_token
  Step 3: Use access_token in Authorization header
  Step 4: When expired, use refresh_token to get new access_token

4. Basic Auth
  Header: Authorization: Basic base64(username:password)
  - Legacy, avoid when possible
```

### Query Parameters vs Path Parameters

```
Path Parameters — Identify a specific resource
 GET /api/candidates/123     ← candidate ID 123
 GET /api/jobs/456/applications  ← applications for job 456

Query Parameters — Filter/sort/paginate
 GET /api/candidates?status=active&page=2&per_page=50&sort=name
 GET /api/jobs?department=engineering&location=bangalore
```

### Pagination Patterns

```json
// Offset-based (most common)
GET /api/candidates?page=1&per_page=100
Response: { "data": [...], "page": 1, "total_pages": 5, "total": 437 }

// Cursor-based (better for large datasets)
GET /api/candidates?cursor=eyJpZCI6MTAwfQ==&limit=100
Response: { "data": [...], "next_cursor": "eyJpZCI6MjAwfQ==", "has_more": true }
```

---

## Part B: API Debugging Flowchart

> When a customer says "the integration is broken" — follow this systematic process:

```
Customer reports: "Data sync is failing"
  │
  ├─→ Step 1: CHECK THE ERROR
  │  • What's the HTTP status code?
  │  • What's the error message in the response body?
  │  • Which endpoint is failing?
  │
  ├─→ Step 2: REPRODUCE
  │  • Can you reproduce with curl or Postman?
  │  • Does it fail for all records or specific ones?
  │  • When did it start failing? (recent config change?)
  │
  ├─→ Step 3: DIAGNOSE BY STATUS CODE
  │  ├── 400 → Check request body format. Missing required field?
  │  ├── 401 → Check auth token. Expired? Rotated? Wrong format?
  │  ├── 403 → Check permissions. Does the API key have access to this endpoint?
  │  ├── 404 → Check URL. Typo? Wrong API version? Resource deleted?
  │  ├── 429 → Rate limited. Check request frequency. Add backoff.
  │  ├── 500 → Server-side issue. Check server logs. Escalate if needed.
  │  └── 502/503/504 → Infrastructure issue. Service down? Check status page.
  │
  ├─→ Step 4: CHECK THE DATA
  │  • Is the payload valid JSON?
  │  • Are all required fields present?
  │  • Are data types correct (string vs int vs boolean)?
  │  • Are there encoding issues (UTF-8)?
  │
  └─→ Step 5: CHECK LOGS
    • Application logs for stack traces
    • Integration logs for request/response pairs
    • Timestamp correlation with when the error started
```

---

## Part C: Enterprise Integration Patterns

### How Platforms Integrate with Enterprise Systems

```
┌─────────────────┐   API   ┌──────────────────┐   API   ┌────────────┐
│ Customer ATS  │ ──────────→ │  Platform AI  │ ──────────→ │ Customer │
│ (Workday,   │ ←────────── │  Platform    │ ←────────── │ HRIS   │
│  Oracle, SAP) │ Bi-directional │ (Intelligence │ Bi-directional │ (BambooHR,│
└─────────────────┘  Data Sync │  Layer)    │  Data Sync │ ADP, etc.)│
                 └──────────────────┘       └────────────┘
```

**What the platform does in this architecture:**
1. **Pulls candidate data** from the customer's ATS (job postings, applications, candidate profiles)
2. **Enriches with AI** — skill matching, bias mitigation, talent intelligence
3. **Pushes results back** — ranked candidates, recommended matches, pipeline analytics
4. **Syncs employee data** from HRIS for internal mobility, talent management

**Integration Methods:**
| Method | When Used | Example |
|:-------|:----------|:--------|
| REST API | Modern systems with public APIs | Workday REST API, Oracle HCM Cloud |
| SFTP File Transfer | Legacy systems, batch data | Nightly CSV dumps of candidate data |
| Webhooks | Real-time event notifications | "Notify the platform when a new job is posted" |
| RPA (Robotic Process Automation) | Legacy systems with NO API | The platform's RPA bot logs into the system and extracts data |

### Common PSE Integration Tasks

1. **Configure data mapping** — Map fields between the platform and customer system
  - Customer's "First Name" + "Last Name" → the platform's "Full Name"
  - Customer's "Job Code" → the platform's "Requisition ID"

2. **Set up sync schedules** — How often data flows between systems
  - Real-time via webhooks for critical events (new application)
  - Batch sync every 4 hours for bulk data (employee records)

3. **Handle data transformation** — Convert data formats
  - Date: "07/08/2026" (US) → "2026-07-08" (ISO 8601)
  - Skills: Free text → structured skill taxonomy

4. **Debug sync failures** — When records don't match between systems
  - Missing records, duplicate records, field value mismatches
  - This is where your SQL + Python + API debugging skills combine

---

## Part D: 5 Troubleshooting Scenarios (Likely Interview Questions)

### Scenario 1: "Customer says candidate data isn't syncing from Workday"
**Your approach:**
1. Check the integration dashboard/logs for error messages
2. Verify the API connection — test with a simple GET request to Workday's endpoint
3. Check authentication — has the OAuth token expired?
4. Check if it's ALL candidates or specific ones (maybe a data validation error)
5. Compare record counts: How many in Workday vs how many in the platform?
6. Check the field mapping — did the customer add new required fields in Workday?

### Scenario 2: "API returns 200 OK but data looks wrong"
**Your approach:**
1. A 200 doesn't mean the data is correct — just that the request succeeded
2. Check the response body — is it returning the expected fields?
3. Compare a few specific records between source and target
4. Check data types — maybe a number is coming as a string
5. Check timezone issues — dates might be off by hours
6. Check pagination — maybe you're only getting page 1

### Scenario 3: "Integration was working yesterday, now getting 503 errors"
**Your approach:**
1. 503 = Service Unavailable. Something changed on the server side.
2. Check the customer's system status page — is their ATS/HRIS down?
3. Check if there was a deployment or maintenance window
4. Check if the API endpoint URL changed (versioning update?)
5. Try again with retry logic — might be a temporary overload
6. If persistent, escalate to the customer's IT team

### Scenario 4: "Customer wants to add custom fields to the integration"
**Your approach:**
1. Understand what field they need and where it lives in their system
2. Check if the platform's API supports custom fields / extended attributes
3. Update the field mapping configuration
4. Test with a single record first (don't push to production immediately)
5. Validate the data type and format requirements
6. Document the change and communicate to stakeholders

### Scenario 5: "Duplicate candidates appearing in the system"
**Your approach:**
1. Identify the duplicates — query the database for duplicate emails/phone numbers
2. Determine the root cause — is it a sync issue or data quality issue?
3. Common causes:
  - Missing deduplication logic in the integration
  - Same candidate applied through multiple sources
  - Data sync running twice due to cron job misconfiguration
4. SQL to find duplicates: `SELECT email, COUNT(*) FROM candidates GROUP BY email HAVING COUNT(*) > 1`
5. Fix: Implement upsert logic (UPDATE if exists, INSERT if new) based on a unique key like email
6. Clean up existing duplicates — merge records, keep the most complete one

---

## Part E: Quick API Concept Questions

| Question | Answer |
|:---------|:-------|
| "What's REST?" | An architectural style for APIs. Uses HTTP methods (GET/POST/PUT/DELETE), stateless, resource-based URLs. |
| "What's the difference between REST and GraphQL?" | REST has fixed endpoints returning fixed data. GraphQL has one endpoint where the client specifies exactly what data it needs. |
| "What's an API rate limit?" | Maximum number of requests allowed in a time window (e.g., 100 requests/minute). Prevents abuse and ensures fair usage. |
| "What's a webhook?" | A "reverse API" — instead of you calling their API, they call YOUR endpoint when an event happens (push vs pull). |
| "What's idempotent?" | An operation that produces the same result no matter how many times you call it. GET and DELETE are idempotent; POST is not. |
| "What's CORS?" | Cross-Origin Resource Sharing. Browser security that blocks API calls from a different domain unless the server explicitly allows it. |
| "How would you test an API?" | Use Postman for manual testing, write automated tests with Python `requests` or `pytest`, use curl for quick checks. |
| "What's API versioning?" | Including version in the URL (`/api/v1/users`) so old clients don't break when the API changes. |
