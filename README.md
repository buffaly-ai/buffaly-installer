# Buffaly Installer

Public release artifacts for installing Buffaly on Windows.

This repository is distribution-only for built Buffaly MSI installer artifacts. It does not contain installer build source code, packaging scripts, or the private installer build system. The installer source and build packaging code are not public.

Buffaly is a runtime for high-trust agents. Public Buffaly component and documentation repositories are available under the `buffaly-ai` GitHub organization for inspection, debugging, plugin/tool development, partner integration, and LLM-assisted understanding where those repositories are public.

## Recommended install path

For most users, start with the latest MSI release artifact.

1. Open the latest release:
   https://github.com/buffaly-ai/buffaly-installer/releases/latest
2. Download the `.msi` asset.
3. Run the installer on Windows.
4. Follow the setup prompts and provide API keys/secrets through local configuration only.

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
