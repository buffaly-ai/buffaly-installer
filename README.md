# Buffaly Installer

Public installer releases for Buffaly runtime packages.

Buffaly is a field-tested runtime for high-trust agents, developed by Matt Furnari. The source is available under the `buffaly-ai` GitHub organization for inspection, debugging, plugin/tool development, partner integration, and LLM-assisted understanding.

## Recommended install path

For most users, start with the latest installer release instead of building every repository from source.

1. Open the latest release:
   https://github.com/buffaly-ai/buffaly-installer/releases/latest
2. Download the `.msi` asset.
3. Run the installer on Windows.
4. Follow the setup prompts and provide API keys/secrets through local configuration only.

## Source repositories

Start with the docs/source map:

- https://buffa.ly/docs
- https://github.com/buffaly-ai

Important source repos:

- `buffaly-development` - main runtime source, agent host, typed tools, session services, web/worker hosts, and ProtoScript integration.
- `protoscript` - executable language for prototype graphs and typed actions.
- `ontology` - prototype graph runtime foundation.
- `buffaly-providers` - provider contracts and model-provider modules.
- `buffaly-tools-browser` - browser automation tool/module example.

## Secrets and safety

Do not publish or attach PHI, customer data, OAuth tokens, API keys, bearer tokens, local browser state, private session data, or configuration files containing live credentials.

The installer package is intended to be public. Runtime secrets must be supplied locally by the user after installation.

## Licensing

Buffaly core is GPLv3 by default. If your organization needs different terms for proprietary use, redistribution, or supported deployment, contact us for commercial licensing.

Buffaly is developed by Matt Furnari.

Commercial licensing inquiries can be opened as GitHub issues using the `commercial-licensing` label/template in the relevant repository.
