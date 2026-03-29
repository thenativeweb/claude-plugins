---
name: read-event-types
description: Read all event types from an EventSourcingDB instance. Use when the user wants to list or discover all event types that exist in the event store.
allowed-tools: Bash, AskUserQuestion
---

# Read Event Types

List all event types from an EventSourcingDB instance.

## Configuration

Read configuration from environment variables:

```bash
echo "ESDB_URL: ${ESDB_URL:-http://localhost:3000}"
echo "ESDB_API_TOKEN: ${ESDB_API_TOKEN:-(not set)}"
```

- Use `ESDB_URL` if set, otherwise default to `http://localhost:3000`.
- If `ESDB_API_TOKEN` is not set, use AskUserQuestion to ask the user for the API token.

## Request

This endpoint returns NDJSON. Use `--no-buffer` and filter out heartbeats. No request body is required.

```bash
curl -s --no-buffer -X POST \
  -H "authorization: Bearer ${ESDB_API_TOKEN}" \
  "${ESDB_URL:-http://localhost:3000}/api/v1/read-event-types" \
  | grep -v '"type":"heartbeat"'
```

## Response

NDJSON stream with one line per event type:

```json
{"type":"eventType","payload":{"eventType":"io.example.book-acquired","isPhantom":false,"schema":{...}}}
```

## Conventions

- Event types use reverse domain notation (e.g., `io.eventsourcingdb.library.book-acquired`).
