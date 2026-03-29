---
name: verify-api-token
description: Verify an API token against an EventSourcingDB instance. Use when the user wants to check if their API token is valid.
allowed-tools: Bash, AskUserQuestion
---

# Verify API Token

Verify that an API token is valid for an EventSourcingDB instance.

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
  "${ESDB_URL:-http://localhost:3000}/api/v1/verify-api-token"
```

## Response

Returns `200 OK` if the token is valid. Returns `401 Unauthorized` if the token is invalid. Inform the user accordingly.
