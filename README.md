# console-automation-mcp-legacy

Maintenance-only compatibility package for the former Console Automation MCP runtime. It preserves the interactive console, 40-tool MCP surface, test framework, and legacy protocol integrations removed from RunBeacon 3.0.

Use [RunBeacon](https://github.com/Liyuchen0118/RunBeacon) and `console-automation-mcp@3` for new durable local, SSH, Slurm, Apple-signing, or GitHub jobs. Use this package only while migrating a client that still depends on the old interactive console or protocol APIs.

## Support policy

`console-automation-mcp-legacy@2.0.x` receives only High and Critical security fixes for six months after RunBeacon 3.0 reaches Stable. It receives no new protocols, features, compatibility work, Codex plugin releases, or lower-severity fixes.

The package can execute arbitrary local and remote commands. Keep MCP approvals enabled, use restricted accounts, prefer SSH agents or environment-backed secret references, and install optional integrations only when required. See [SECURITY.md](SECURITY.md).

## Installation

```bash
npm install console-automation-mcp-legacy@2
```

The package exposes two equivalent executables:

```bash
npx console-automation-mcp-legacy
npx mcp-console
```

Example MCP configuration:

```json
{
  "mcpServers": {
    "console-automation-legacy": {
      "command": "npx",
      "args": ["-y", "console-automation-mcp-legacy@2"]
    }
  }
}
```

This repository does not ship the `remote-job-monitor` Codex plugin and cannot replace RunBeacon's lifecycle daemon, durable Runner, approval dashboard, or credential tools.

## Preserved surface

The MCP server retains 40 tools in six groups:

- Session management: create, input, keys, output, streaming, waits, stop, list, and cleanup.
- Command execution: execute, error detection, resource usage, output clearing, state, and history.
- Monitoring: system/session metrics, alerts, dashboard data, and monitoring start/stop.
- Profiles: save, list, remove, and use legacy connection profiles.
- Background jobs: execute, status, output, cancel, list, progress, result, metrics, and cleanup.
- Test automation: output, exit-code, error, snapshot, comparison, and state assertions.

The source also preserves the legacy local, SSH, cloud, container, virtualization, serial, database, and remote-console protocol adapters. Most non-core adapters use optional peer dependencies; install only the peers needed by your deployment.

The complete historical tool and protocol references remain under [docs](docs/TOOLS.md).

## Security defaults

- Saved profiles reject inline passwords, passphrases, tokens, and private-key contents.
- File logging is disabled by default in MCP mode and owner-only when explicitly enabled.
- Session persistence is disabled by default because recovery metadata can contain commands, paths, and environment names.
- Xen XAPI requires verified HTTPS.
- VNC challenge authentication is allowed only inside a verified TLS channel.
- Dynamic progress patterns use RE2-compatible matching and bounded input.

Do not publish credentials, host addresses, command output, or production configuration in issues. Use GitHub private vulnerability reporting for security reports.

## Development

Node.js 18, 20, and 22 are covered by the maintenance CI matrix.

```bash
npm ci
npm run typecheck
npm run build
npm run test:mcp
npm run test:logger
npm run test:package
npm run test:ci
```

Dependency audit and CodeQL `security-extended` scanning are required for maintenance releases. Releases are manual; this repository has no automatic npm publication workflow.

## Migration

RunBeacon 3.0 intentionally replaces interactive sessions with one durable job lifecycle:

- `console_create_session` and background-job polling become `job_start` followed by one `job_wait`.
- Long remote work can use the Go Runner and resume after SSH or coordinator restarts.
- The dashboard watches one task through local long polling without model turns.
- Credentials remain in OS-managed stores or SSH agents.
- Slurm, training, Apple signing, and GitHub publishing have explicit adapters and policy gates.

Pin this package to `2.0.x` only for the migration window. Do not add new production dependencies on the legacy surface.

## License

MIT. See [LICENSE](LICENSE).
