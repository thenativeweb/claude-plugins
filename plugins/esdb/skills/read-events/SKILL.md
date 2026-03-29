---
name: read-events
description: Read events from an EventSourcingDB instance. Use when the user wants to fetch, list, or view events for a subject, with optional filtering, ordering, and bounds.
allowed-tools: Bash, AskUserQuestion
---

# Read Events

Read events for a subject from an EventSourcingDB instance.

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
  "${ESDB_URL:-http://localhost:3000}/api/v1/read-events" \
  | grep -v '"type":"heartbeat"'
```

### Request Body

```json
{
  "subject": "/<subject-path>",
  "options": {
    "recursive": false,
    "order": "chronological",
    "lowerBound": {
      "id": "<event-id>",
      "type": "inclusive"
    },
    "upperBound": {
      "id": "<event-id>",
      "type": "exclusive"
    },
    "fromLatestEvent": {
      "subject": "/<subject-path>",
      "type": "<event-type>",
      "ifEventIsMissing": "read-everything"
    }
  }
}
```

Options (all optional except `recursive` in `options`):

- **`recursive`** (boolean): Include sub-subjects.
- **`order`**: `"chronological"` (default) or `"antichronological"`.
- **`lowerBound`**: Filter events from this ID. `type`: `"inclusive"` or `"exclusive"`.
- **`upperBound`**: Filter events up to this ID. `type`: `"inclusive"` or `"exclusive"`.
- **`fromLatestEvent`**: Start from last event of given type. `ifEventIsMissing`: `"read-everything"` or `"read-nothing"`. Cannot be combined with `lowerBound`.

### Example

```bash
curl -s --no-buffer -X POST \
  -H "authorization: Bearer ${ESDB_API_TOKEN}" \
  -H "content-type: application/json" \
  -d "{
    \"subject\": \"/books/42\",
    \"options\": {
      \"recursive\": false
    }
  }" \
  "${ESDB_URL:-http://localhost:3000}/api/v1/read-events" \
  | grep -v '"type":"heartbeat"'
```

## Response

NDJSON stream with one line per event:

```json
{"type":"event","payload":{...}}
```

NDJSON responses may contain `{"type":"error","payload":{"error":"..."}}` lines even after an initial `200 OK`.

## Conventions

- Subjects always start with `/` (e.g., `/books/42`).
- Event IDs and bound IDs are strings (e.g., `"42"`).
