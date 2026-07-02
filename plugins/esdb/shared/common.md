# Shared Instructions for EventSourcingDB Skills

## Configuration

Read configuration from environment variables:

```bash
echo "ESDB_URL: ${ESDB_URL:-http://localhost:3000}"
echo "ESDB_API_TOKEN: ${ESDB_API_TOKEN:-(not set)}"
echo "ESDB_SOURCE: ${ESDB_SOURCE:-(not set)}"
echo "ESDB_EVENT_TYPE_PREFIX: ${ESDB_EVENT_TYPE_PREFIX:-(not set)}"
```

- Use `ESDB_URL` if set, otherwise default to `http://localhost:3000`.
- If `ESDB_API_TOKEN` is not set and the endpoint requires authentication, use AskUserQuestion to ask the user for the API token.

### Event Source and Type Prefix

Skills that construct events or event types (e.g., writing events, registering schemas) need a consistent `source` and a common reverse-domain prefix for event types, so that all events in a session share a uniform format (e.g., source `https://library.example.io`, prefix `io.example.library`).

- Use `ESDB_SOURCE` and `ESDB_EVENT_TYPE_PREFIX` if set.
- Otherwise, the first time either value is needed in a session, use AskUserQuestion to ask the user for both values.
- Reuse the answers for the rest of the session — do not ask again, and do not invent deviating values.

## NDJSON Responses

Streaming endpoints return NDJSON (one JSON object per line). When calling them with curl:

- Use `--no-buffer` to avoid buffering.
- Filter out heartbeats (`{"type":"heartbeat","payload":{}}`) by appending `| grep -v '"type":"heartbeat"'`.
- Error lines (`{"type":"error","payload":{"error":"..."}}`) may appear even after an initial `200 OK` — watch for them and report errors to the user.

## Conventions

- Subjects always start with `/` (e.g., `/books/42`, `/readers/23`).
- Event types use reverse domain notation (e.g., `io.eventsourcingdb.library.book-acquired`).
- Event IDs and bound IDs are strings, even if they look numeric (e.g., `"0"`, `"42"`).
