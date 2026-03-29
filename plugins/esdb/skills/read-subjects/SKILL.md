---
name: read-subjects
description: Read subjects from an EventSourcingDB instance. Use when the user wants to list, browse, or discover which subjects exist in the event store.
allowed-tools: Bash, AskUserQuestion
---

# Read Subjects

List subjects that have received events in an EventSourcingDB instance.

## Configuration

Read configuration from environment variables:

```bash
echo "ESDB_URL: ${ESDB_URL:-http://localhost:3000}"
echo "ESDB_API_TOKEN: ${ESDB_API_TOKEN:-(not set)}"
```

- Use `ESDB_URL` if set, otherwise default to `http://localhost:3000`.
- If `ESDB_API_TOKEN` is not set, use AskUserQuestion to ask the user for the API token.

## Request

This endpoint returns NDJSON. Use `--no-buffer` and filter out heartbeats:

```bash
curl -s --no-buffer -X POST \
  -H "authorization: Bearer ${ESDB_API_TOKEN}" \
  -H "content-type: application/json" \
  -d '<REQUEST_BODY>' \
  "${ESDB_URL:-http://localhost:3000}/api/v1/read-subjects" \
  | grep -v '"type":"heartbeat"'
```

### Request Body

```json
{
  "baseSubject": "/"
}
```

### Example

```bash
curl -s --no-buffer -X POST \
  -H "authorization: Bearer ${ESDB_API_TOKEN}" \
  -H "content-type: application/json" \
  -d '{"baseSubject": "/"}' \
  "${ESDB_URL:-http://localhost:3000}/api/v1/read-subjects" \
  | grep -v '"type":"heartbeat"'
```

## Response

NDJSON stream with one line per subject:

```json
{"type":"subject","payload":{"subject":"/books/42"}}
```

## Conventions

- Subjects always start with `/` (e.g., `/books/42`, `/readers/23`).
