# Shared Instructions for EventSourcingDB Skills

## Configuration

Read configuration from environment variables:

```bash
echo "ESDB_URL: ${ESDB_URL:-http://localhost:3000}"
echo "ESDB_API_TOKEN: ${ESDB_API_TOKEN:-(not set)}"
```

- Use `ESDB_URL` if set, otherwise default to `http://localhost:3000`.
- If `ESDB_API_TOKEN` is not set and the endpoint requires authentication, use AskUserQuestion to ask the user for the API token.

## NDJSON Responses

Streaming endpoints return NDJSON (one JSON object per line). When calling them with curl:

- Use `--no-buffer` to avoid buffering.
- Filter out heartbeats (`{"type":"heartbeat","payload":{}}`) by appending `| grep -v '"type":"heartbeat"'`.
- Error lines (`{"type":"error","payload":{"error":"..."}}`) may appear even after an initial `200 OK` — watch for them and report errors to the user.

## Conventions

- Subjects always start with `/` (e.g., `/books/42`, `/readers/23`).
- Event types use reverse domain notation (e.g., `io.eventsourcingdb.library.book-acquired`).
- Event IDs and bound IDs are strings, even if they look numeric (e.g., `"0"`, `"42"`).
