---
name: easymqtt-cli
description: Use when an agent needs to configure, inspect, monitor, publish to, subscribe to, or troubleshoot MQTT brokers through the EasyMQTT CLI or its local MCP server on macOS.
version: 1.0.0
author: EasyMQTT
metadata:
  platforms:
    - macOS
  minimum_cli_version: 0.1.0
  requires:
    binaries:
      - easymqtt
  homepage: https://easymqtt.app/cli
  tags:
    - mqtt
    - mcp
    - macos
    - iot
    - home-automation
---

# EasyMQTT CLI

Use the installed `easymqtt` command-line tool for local MQTT work on macOS. Prefer the MCP server when the current agent host supports MCP; otherwise use bounded CLI commands.

## First Checks

Verify the CLI before trying broker operations:

```bash
which easymqtt
easymqtt --help
easymqtt mcp --help
```

If `easymqtt` is missing, tell the user to install it:

```bash
brew tap LucaKaufmann/tap
brew install easymqtt
```

The CLI can usually auto-detect the EasyMQTT Mac app database. Set `EASYMQTT_DB_PATH` only when auto-detection fails, when testing against a temporary database, or when the user explicitly wants a specific database.

## Safety Rules

- Do not print broker passwords, certificate passwords, private keys, or credential-bearing config values.
- Prefer saved broker names or saved broker IDs over inline credentials.
- Keep subscriptions bounded with an explicit duration.
- Do not start long-running monitoring unless the user explicitly asks for it.
- Summarize MQTT payloads carefully; call out binary, unusually large, or sensitive-looking payloads instead of dumping them.
- Close MCP connections that are no longer needed.

## MCP Workflow

When MCP tools are available, use the EasyMQTT MCP server exposed by:

```bash
easymqtt mcp
```

Useful MCP tools include:

- `mqtt_connect`
- `mqtt_disconnect`
- `mqtt_test_connection`
- `mqtt_publish`
- `mqtt_subscribe`
- `mqtt_unsubscribe`
- `mqtt_list_topics`
- `easymqtt_list_brokers`
- `easymqtt_list_favorites`
- `easymqtt_recent_history`
- `easymqtt_save_broker`
- `easymqtt_save_favorite`
- `easymqtt_delete_broker`

Useful MCP resources include:

- `easymqtt://brokers`
- `easymqtt://favorites`
- `easymqtt://history/recent`
- `easymqtt://history/<percent-encoded-topic>?since=...&limit=...`

Useful MCP prompts include:

- `mqtt_setup_guide`
- `mqtt_debug`
- `mqtt_monitor`

For setup, a typical stdio server command is:

```json
{
  "mcpServers": {
    "easymqtt": {
      "type": "stdio",
      "command": "/opt/homebrew/bin/easymqtt",
      "args": ["mcp"]
    }
  }
}
```

If the user needs a forced database path, add `EASYMQTT_DB_PATH` under `env`.

## CLI Workflows

List saved brokers:

```bash
easymqtt brokers list --json
```

List saved favorites:

```bash
easymqtt favorites list --json
```

Inspect recent history:

```bash
easymqtt history --limit 20 --json
```

Test a saved broker:

```bash
easymqtt test --broker Home
```

Publish to a saved broker:

```bash
easymqtt publish --broker Home --topic sensors/office --message "hello"
```

Subscribe briefly:

```bash
easymqtt subscribe --broker Home --topic "sensors/#" --duration 10
```

For machine-readable subscription output:

```bash
easymqtt subscribe --broker Home --topic "sensors/#" --duration 10 --format json
```

`easymqtt subscribe --format json` emits one pretty-printed JSON object per message without NDJSON line framing. Do not parse it as one JSON object per line. Prefer bounded runs and either parse complete JSON object boundaries or summarize the captured output directly.

Use manual broker details only when saved broker data is not available:

```bash
easymqtt publish \
  --host broker.example.com \
  --port 1883 \
  --topic sensors/office \
  --message "hello"
```

## Troubleshooting Flow

For connection or authentication problems:

1. Read saved brokers or ask for the target broker.
2. Run `easymqtt test --broker <name>` or use `mqtt_test_connection`.
3. Distinguish DNS, TCP, TLS, MQTT CONNACK, authentication, and topic-level failures.
4. Check recent history when topic behavior is unclear.
5. Recommend the smallest next action, such as fixing host/port, enabling TLS, checking credentials, or verifying topic permissions.

For topic monitoring:

1. Confirm the broker and topic filter.
2. Use a bounded subscription.
3. Summarize counts, retained messages, payload shapes, anomalous values, and missing expected traffic.
4. Avoid dumping credentials or large payloads.

## App Data Notes

EasyMQTT CLI can reuse saved brokers, favorites, and recent history from the EasyMQTT Mac app when the app database is available on the same Mac. It cannot directly read data from an iPhone-only install.

Default shared database path:

```text
~/Library/Group Containers/group.com.mqtthings.EasyMQTT/EasyMQTT-v2.sqlite
```

Explicit database example:

```bash
EASYMQTT_DB_PATH="$HOME/Library/Group Containers/group.com.mqtthings.EasyMQTT/EasyMQTT-v2.sqlite" easymqtt brokers list --json
```
