---
name: run-eventql-query
description: Run an EventQL query against an EventSourcingDB instance. Use when the user wants to query, filter, aggregate, or analyze events using the EventQL query language.
allowed-tools: Bash, AskUserQuestion
---

# Run EventQL Query

Run an EventQL query against an EventSourcingDB instance.

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
  "${ESDB_URL:-http://localhost:3000}/api/v1/run-eventql-query" \
  | grep -v '"type":"heartbeat"'
```

### Request Body

```json
{
  "query": "<EventQL query>"
}
```

### Example

```bash
curl -s --no-buffer -X POST \
  -H "authorization: Bearer ${ESDB_API_TOKEN}" \
  -H "content-type: application/json" \
  -d "{\"query\": \"FROM e IN events PROJECT INTO e\"}" \
  "${ESDB_URL:-http://localhost:3000}/api/v1/run-eventql-query" \
  | grep -v '"type":"heartbeat"'
```

## Common EventQL Patterns

- All events: `FROM e IN events PROJECT INTO e`
- Filter by type: `FROM e IN events WHERE e.type == "io.example.book-acquired" PROJECT INTO e`
- Filter by subject: `FROM e IN events WHERE e.subject == "/books/42" PROJECT INTO e`
- Count: `FROM e IN events WHERE e.type == "io.example.book-borrowed" PROJECT INTO { total: COUNT() }`
- Group by type: `FROM e IN events GROUP BY e.type PROJECT INTO { type: UNIQUE(e.type), count: COUNT() }`
- Latest N events: `FROM e IN events ORDER BY e.time DESC TOP 10 PROJECT INTO e`

## Response

NDJSON stream with one line per result row:

```json
{"type":"row","payload":{...}}
```
