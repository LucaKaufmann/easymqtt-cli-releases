# easymqtt-cli-releases

Public releases for the EasyMQTT CLI.

This repository hosts public distribution assets for the standalone macOS `easymqtt` command-line tool, including binary release artifacts and the AgentSkills-compatible EasyMQTT CLI skill for local agent workflows.

## Install The CLI

Homebrew:

```bash
brew tap LucaKaufmann/tap
brew install easymqtt
```

Direct package install:

1. Download `easymqtt-macos-universal.pkg` from the latest GitHub release.
2. Install it with Finder or:

```bash
sudo installer -pkg easymqtt-macos-universal.pkg -target /
```

Verify:

```bash
which easymqtt
easymqtt --help
easymqtt mcp --help
```

## Agent Skill

The portable EasyMQTT CLI skill lives at:

```text
skills/easymqtt-cli/SKILL.md
```

Use it with OpenClaw, Hermes, or another AgentSkills-compatible host to teach agents how to:

- verify the `easymqtt` install
- configure and use `easymqtt mcp`
- list saved brokers, favorites, and recent history
- run bounded broker diagnostics
- publish and subscribe safely
- avoid printing secrets or unbounded MQTT traffic

Manual install examples:

```bash
mkdir -p ~/.openclaw/skills
cp -R skills/easymqtt-cli ~/.openclaw/skills/
```

```bash
mkdir -p ~/.hermes/skills
cp -R skills/easymqtt-cli ~/.hermes/skills/
```

After installation, ask your agent something like:

```text
Use EasyMQTT to test my Home broker and explain any connection failure.
```

```text
Use EasyMQTT to monitor sensors/# for 30 seconds and summarize unusual payloads.
```

## Skill Release Asset

To package the skill as a release artifact from the repository root:

```bash
tar -czf easymqtt-agent-skill-v1.0.0.tar.gz -C skills easymqtt-cli
```

Attach the archive to a GitHub release alongside the CLI artifacts when publishing or updating the skill.
