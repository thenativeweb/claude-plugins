# claude-plugins

Claude Code plugins by [the native web](https://www.thenativeweb.io).

## Available Plugins

| Plugin | Description |
| ------ | ----------- |
| esdb   | Interact with [EventSourcingDB](https://www.eventsourcingdb.io) using its HTTP API |

## Getting Started

Add the marketplace to Claude Code:

```shell
/plugin marketplace add thenativeweb/claude-plugins
```

Then install any plugin listed above.

## esdb

Interact with [EventSourcingDB](https://www.eventsourcingdb.io) using its HTTP API.

### Installation

```shell
/plugin install esdb@thenativeweb
```

### Configuration

Set the following environment variables:

- `ESDB_URL` – Base URL of your EventSourcingDB instance (default: `http://localhost:3000`)
- `ESDB_API_TOKEN` – API token for authentication (you will be asked if not set)

### Skills

```shell
/esdb:ping
/esdb:verify-api-token
/esdb:write-events
/esdb:read-events
/esdb:observe-events
/esdb:read-subjects
/esdb:read-event-type
/esdb:read-event-types
/esdb:run-eventql-query
/esdb:register-event-schema
```
