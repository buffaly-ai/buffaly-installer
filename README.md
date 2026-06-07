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
