---
name: read-event-type
description: Read details about a single event type from an EventSourcingDB instance. Use when the user wants to inspect a specific event type, check if it has a schema, or see its details.
allowed-tools: Bash, AskUserQuestion
---

# Read Event Type

Get details about a single event type from an EventSourcingDB instance.

## Configuration

Read configuration from environment variables:

```bash
echo "ESDB_URL: ${ESDB_URL:-http://localhost:3000}"
echo "ESDB_API_TOKEN: ${ESDB_API_TOKEN:-(not set)}"
```

- Use `ESDB_URL` if set, otherwise default to `http://localhost:3000`.
- If `ESDB_API_TOKEN` is not set, use AskUserQuestion to ask the user for the API token.

## Request

```bash
curl -s -i -X POST \
  -H "authorization: Bearer ${ESDB_API_TOKEN}" \
  -H "content-type: application/json" \
  -d '<REQUEST_BODY>' \
  "${ESDB_URL:-http://localhost:3000}/api/v1/read-event-type"
```

### Request Body

```json
{
  "eventType": "<reverse.domain.event-type>"
}
```

### Example

```bash
curl -s -i -X POST \
  -H "authorization: Bearer ${ESDB_API_TOKEN}" \
  -H "content-type: application/json" \
  -d '{"eventType": "io.example.book-acquired"}' \
  "${ESDB_URL:-http://localhost:3000}/api/v1/read-event-type"
```

## Response

JSON object:

```json
{
  "eventType": "io.example.book-acquired",
  "isPhantom": false,
  "schema": { ... }
}
```

## Conventions

- Event types use reverse domain notation (e.g., `io.eventsourcingdb.library.book-acquired`).
