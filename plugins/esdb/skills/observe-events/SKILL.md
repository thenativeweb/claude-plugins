---
name: observe-events
description: Observe events in real time from an EventSourcingDB instance. Use when the user wants to watch, monitor, or stream live events as they are written.
allowed-tools: Bash, AskUserQuestion
---

# Observe Events

Observe events in real time from an EventSourcingDB instance. This opens a long-lived streaming connection.

## Configuration

Read configuration from environment variables:

```bash
echo "ESDB_URL: ${ESDB_URL:-http://localhost:3000}"
echo "ESDB_API_TOKEN: ${ESDB_API_TOKEN:-(not set)}"
```

- Use `ESDB_URL` if set, otherwise default to `http://localhost:3000`.
- If `ESDB_API_TOKEN` is not set, use AskUserQuestion to ask the user for the API token.

## Request

This endpoint returns a long-lived NDJSON stream. Always use `--max-time` to ensure the connection terminates (default: 30 seconds). Use `--no-buffer` and filter out heartbeats:

```bash
curl -s --no-buffer --max-time 30 -X POST \
  -H "authorization: Bearer ${ESDB_API_TOKEN}" \
  -H "content-type: application/json" \
  -d '<REQUEST_BODY>' \
  "${ESDB_URL:-http://localhost:3000}/api/v1/observe-events" \
  | grep -v '"type":"heartbeat"'
```

When the user wants a custom timeout, use AskUserQuestion to ask for the desired duration in seconds.

### Request Body

```json
{
  "subject": "/<subject-path>",
  "options": {
    "recursive": false,
    "lowerBound": {
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

Options:

- **`recursive`** (boolean): Include sub-subjects.
- **`lowerBound`**: Resume from event ID. `type`: `"inclusive"` or `"exclusive"`.
- **`fromLatestEvent`**: Start from last event of given type. `ifEventIsMissing`: `"read-everything"` or `"wait-for-event"`. Cannot be combined with `lowerBound`.

### Example

```bash
curl -s --no-buffer --max-time 30 -X POST \
  -H "authorization: Bearer ${ESDB_API_TOKEN}" \
  -H "content-type: application/json" \
  -d "{
    \"subject\": \"/\",
    \"options\": {
      \"recursive\": true
    }
  }" \
  "${ESDB_URL:-http://localhost:3000}/api/v1/observe-events" \
  | grep -v '"type":"heartbeat"'
```

## Response

NDJSON stream with one line per event:

```json
{"type":"event","payload":{...}}
```

Heartbeats (`{"type":"heartbeat","payload":{}}`) are sent periodically and are filtered out by the `grep -v` pattern.

## Conventions

- Subjects always start with `/` (e.g., `/books/42`).
- Event IDs and bound IDs are strings (e.g., `"42"`).
