# Buffaly Installer

Public release artifacts for installing Buffaly on supported platforms.

This repository is distribution-only for built Buffaly installer artifacts. It does not contain installer build source code, packaging scripts, or the private installer build system. Build source and packaging implementation live in the product/provisioning source repositories.

Buffaly is a runtime for high-trust agents. Public Buffaly component and documentation repositories are available under the `buffaly-ai` GitHub organization for inspection, debugging, plugin/tool development, partner integration, and LLM-assisted understanding where those repositories are public.

## Release artifacts

The first Linux release uses GitHub Releases only. The public website should link to these release assets instead of hosting binaries separately.

Expected release asset names:

```text
BuffalyInstaller-win-x64-<version>.msi
buffaly-linux-x64-<version>.tar.gz
buffaly-linux-x64-<version>.sha256
release-manifest.json
```

Future Linux variants may add `buffaly-linux-arm64-<version>.tar.gz` after the x64 tarball flow is stable.

The first Mac developer-preview artifact is staged for GitHub Releases as a command-line package, not a graphical installer. It requires an external SQL Server 2025 database endpoint reachable from the Mac. The Mac package must not reuse private evaluation database names, production database names, or existing SQL logins unless the operator explicitly chooses to attach that install to existing state.

Expected Mac developer-preview release asset names:

```text
buffaly-mac-arm64-phase1-dev-20260608003948.zip
buffaly-mac-arm64-phase1-dev-20260608003948.sha256
release-manifest.json
```

Validated Mac developer-preview checksum:

```text
aa52376eec44790e26b46e64367e67f17fea4d9c4019e6dd122d8f4c48881add  buffaly-mac-arm64-phase1-dev-20260608003948.zip
```

## Recommended install path

Open the latest release:

```text
https://github.com/buffaly-ai/buffaly-installer/releases/latest
```

### Windows

1. Download the `.msi` asset.
2. Run the installer on Windows.
3. Follow the setup prompts and provide API keys/secrets through local configuration only.

### Linux MVP

1. Download `buffaly-linux-x64-<version>.tar.gz` and `buffaly-linux-x64-<version>.sha256`.
2. Verify the checksum:

```bash
sha256sum -c buffaly-linux-x64-<version>.sha256
```

3. Extract and install:

```bash
tar -xzf buffaly-linux-x64-<version>.tar.gz
cd buffaly-linux-x64-<version>
bash ./scripts/install.sh \
  --install-root "$HOME/buffaly" \
  --port 5088 \
  --auth none \
  --sql-mode external \
  --sessions-connection 'Server=<host>,<port>;Database=buffaly_linux_sessions;User Id=buffaly_linux_app;Password=<password>;TrustServerCertificate=True;' \
  --semantic-connection 'Server=<host>,<port>;Database=buffaly_linux_semanticdb_entities;User Id=buffaly_linux_app;Password=<password>;TrustServerCertificate=True;'
```

4. Start and validate:

```bash
$HOME/buffaly/buffaly-start.sh
$HOME/buffaly/buffaly-status.sh
$HOME/buffaly/validate.sh --install-root "$HOME/buffaly" --url http://127.0.0.1:5088/ --expect-no-auth
```

Linux MVP defaults:

- no-auth by default;
- shell launch scripts, with optional systemd service;
- external SQL Server as the stable database mode;
- Linux-specific databases and login, for example `buffaly_linux_sessions`, `buffaly_linux_semanticdb_entities`, and `buffaly_linux_app`.

Do not reuse or alter existing Buffaly SQL logins for Linux installs.

### Mac developer preview

The Mac Phase 1 package is for Apple Silicon (`osx-arm64`) and uses command-line install/update scripts. It is not a `.pkg` or graphical installer yet.

Prerequisites:

- Apple Silicon Mac.
- External SQL Server 2025 endpoint reachable from the Mac. SQL Server does not need to run on the Mac.
- A dedicated SQL login and dedicated Buffaly sessions/semantic databases for this Mac install, unless the operator intentionally attaches to existing Buffaly state.
- macOS Terminal access.

1. Download `buffaly-mac-arm64-phase1-dev-20260608003948.zip` and its checksum file.
2. Verify the checksum:

```bash
shasum -a 256 buffaly-mac-arm64-phase1-dev-20260608003948.zip
```

Expected checksum:

```text
aa52376eec44790e26b46e64367e67f17fea4d9c4019e6dd122d8f4c48881add
```

3. Extract and install:

```bash
unzip buffaly-mac-arm64-phase1-dev-20260608003948.zip -d buffaly-mac-arm64-phase1-dev-20260608003948
cd buffaly-mac-arm64-phase1-dev-20260608003948
chmod +x ./scripts/*.sh
./scripts/install-mac.sh \
  --install-root "$HOME/buffaly-mac" \
  --port 5090 \
  --auth none \
  --sql-host <sql-server-host> \
  --sql-port 1433 \
  --sql-login <dedicated-buffaly-sql-login> \
  --sql-password '<password>' \
  --sessions-db <dedicated-sessions-database> \
  --semantic-db <dedicated-semantic-database> \
  --sign-mode adhoc \
  --no-validate
```

4. Start and validate:

```bash
$HOME/buffaly-mac/scripts/buffaly-start.sh --install-root "$HOME/buffaly-mac"
$HOME/buffaly-mac/scripts/validate.sh --install-root "$HOME/buffaly-mac" --port 5090
```

5. Open:

```text
http://127.0.0.1:5090/buffaly-agent.html
```

Mac developer-preview defaults and limitations:

- command-line install flow only;
- no-auth mode is supported with `--auth none` for private/dev networks;
- external SQL Server 2025 is the supported Phase 1 database mode;
- package uses ad-hoc signing for development validation;
- public Developer ID signing and notarization are not complete yet;
- launchd support is scaffolded but still needs reboot/logout validation before public guidance treats it as production-ready;
- updater scripts are scaffolded, but rollback/signature hardening remains future work.

Website/docs handoff notes:

- Website download buttons should link to GitHub Release assets in this repository rather than hosting binaries separately.
- Public docs should describe generic SQL Server 2025 requirements and dedicated database/login guidance only.
- Public docs must not include private evaluation database names, private SQL login names, private passwords, Tailscale hostnames, or private validation IPs.
- The Mac docs should clearly label this as a developer-preview command-line package until Developer ID signing, notarization, and the production installer flow are complete.
## Source repositories

This repository does not provide installer build source. For public Buffaly runtime/component source and documentation, start with the docs/source map:

- https://buffa.ly/docs
- https://github.com/buffaly-ai

Relevant public component repositories may include:

- `protoscript` - executable language for prototype graphs and typed actions.
- `ontology` - prototype graph runtime foundation.
- `buffaly-providers` - provider contracts and model-provider modules.
- `buffaly-tools-browser` - browser automation tool/module example.

## Secrets and safety

Do not publish or attach PHI, customer data, OAuth tokens, API keys, bearer tokens, local browser state, private session data, or configuration files containing live credentials.

The installer package is intended to be public. Runtime secrets must be supplied locally by the user after installation.

## Licensing

Use of the distributed installer is subject to the applicable terms for the Buffaly components it installs. Buffaly runtime/core components are GPLv3 by default unless separately commercially licensed. Public source and component license details can be reviewed in the relevant `buffaly-ai` repositories and documentation.

If your organization needs different terms for proprietary use, redistribution, or supported deployment, contact us for commercial licensing.

Commercial licensing inquiries can be opened as GitHub issues using the `commercial-licensing` label/template in the relevant repository.
