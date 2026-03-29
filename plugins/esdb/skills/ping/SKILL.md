---
name: ping
description: Ping an EventSourcingDB instance to check if it is reachable. Use when the user wants to verify connectivity or check if EventSourcingDB is running.
allowed-tools: Bash, AskUserQuestion
---

# Ping

Check if an EventSourcingDB instance is reachable.

## Configuration

Read the base URL from the environment variable `ESDB_URL`. If it is not set, default to `http://localhost:3000`.

```bash
echo "ESDB_URL: ${ESDB_URL:-http://localhost:3000}"
```

## Request

No authentication is required for this endpoint.

```bash
curl -s -i "${ESDB_URL:-http://localhost:3000}/api/v1/ping"
```

## Response

Check that the response contains HTTP status `200 OK` and the `Server` header starts with `EventSourcingDB/`.

The response body is JSON in CloudEvents format and contains:

```json
{"data":{"message":"My God, it's full of stars."}}
```
