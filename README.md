# claude-plugins

Claude Code plugins by [the native web](https://www.thenativeweb.io).

## Available Plugins

| Plugin | Description |
| ------ | ----------- |
| esdb   | Interact with [EventSourcingDB](https://www.eventsourcingdb.io) using its HTTP API |

## Usage

### Add the Marketplace

```shell
/plugin marketplace add thenativeweb/claude-plugins
```

### Install a Plugin

```shell
/plugin install esdb@thenativeweb
```

### Use the Skills

```shell
/esdb:ping
/esdb:write-events
/esdb:read-events
/esdb:observe-events
/esdb:read-subjects
/esdb:read-event-type
/esdb:read-event-types
/esdb:run-eventql-query
/esdb:register-event-schema
/esdb:verify-api-token
```

## Configuration

The esdb plugin reads configuration from environment variables:

- `ESDB_URL` – Base URL of your EventSourcingDB instance (default: `http://localhost:3000`)
- `ESDB_API_TOKEN` – API token for authentication (you will be asked if not set)

## License

MIT
