# CloudEvents in EventSourcingDB

EventSourcingDB stores events in the CloudEvents 1.0 format. The fields fall into three groups:

| Group | Fields | Notes |
| ----- | ------ | ----- |
| Client-provided (required when writing) | `source`, `subject`, `type`, `data` | See validation rules below. |
| Server-managed (never provide these) | `specversion`, `id`, `time`, `datacontenttype` | `specversion` is always `"1.0"`, `id` is a sequential numeric string per event store, `time` is RFC 3339 with nanosecond precision in UTC, `datacontenttype` is always `application/json`. |
| EventSourcingDB extensions (returned when reading) | `hash`, `predecessorhash`, `signature`, `traceparent`, `tracestate` | Integrity and tracing metadata, not payload. `hash` and `predecessorhash` chain all events of the store cryptographically (SHA-256); `signature` is `null` unless the server has a signing key configured. |

## Validation Rules

- **`source`** must be a valid URI reference (e.g., `https://library.example.io` or `tag:example.io,2025:library`). Arbitrary strings like `my-app` are rejected.
- **`subject`** must be a path starting with `/`; segments may only contain `[0-9A-Za-z_-]` (e.g., `/books/42`). Spaces or special characters are rejected.
- **`type`** must use reverse domain notation with at least three dot-separated segments, the first segment being at least two characters long (e.g., `io.example.book-acquired`).
- **`data`** must be a JSON object. `null`, arrays, or scalars at the top level are rejected; an empty object `{}` is allowed.

## Example Event (as returned when reading)

```json
{
  "specversion": "1.0",
  "id": "0",
  "source": "https://library.example.io",
  "subject": "/books/42",
  "type": "io.example.book-acquired",
  "time": "2025-07-02T14:30:45.123456789Z",
  "datacontenttype": "application/json",
  "data": {
    "title": "2001 – A Space Odyssey",
    "author": "Arthur C. Clarke"
  },
  "predecessorhash": "0000000000000000000000000000000000000000000000000000000000000000",
  "hash": "…64 hex characters…",
  "signature": null,
  "traceparent": null,
  "tracestate": null
}
```

A `predecessorhash` of all zeros marks the very first event of the store.
