# Mac staging .NET runtime mismatch handoff

## Problem

A Mac staging Buffaly instance targeting `net9.0` was able to run when it resolved the Buffaly-bundled .NET 9 runtime, but tool/harness execution could fail when child processes resolved the Homebrew/system `dotnet` installation instead.

Observed failure shape in logs:

- App/tool targets `net9.0`.
- Required framework: `Microsoft.NETCore.App`, version `9.0.0`.
- Failing .NET location: `/opt/homebrew/Cellar/dotnet/10.0.300/libexec`.
- Only .NET 10 runtime was visible there.

This is not a missing .NET 10 problem. It is a runtime-resolution problem: Buffaly `net9.0` processes must not accidentally resolve against a .NET 10-only Homebrew runtime root.

## Current short-term machine fix

Staging root:

```text
/Users/matthewfurnari/buffaly-newinstall-staging-20260611_232155
```

Temporary runtime source:

```text
/Users/matthewfurnari/buffaly-mac/dotnet
```

Created local runtime scripts:

```text
/Users/matthewfurnari/buffaly-newinstall-staging-20260611_232155/scripts/start-staging-with-dotnet9.sh
/Users/matthewfurnari/buffaly-newinstall-staging-20260611_232155/scripts/recycle-staging-with-dotnet9.sh
```

Created local LaunchAgent:

```text
/Users/matthewfurnari/Library/LaunchAgents/com.buffaly.agent.staging.5108.plist
```

The LaunchAgent pins:

```text
DOTNET_ROOT=/Users/matthewfurnari/buffaly-mac/dotnet
PATH=/Users/matthewfurnari/buffaly-mac/dotnet:/opt/homebrew/bin:/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin
BUFFALY_INSTALL_ROOT=/Users/matthewfurnari/buffaly-newinstall-staging-20260611_232155
DOTNET_ENVIRONMENT=Production
ASPNETCORE_URLS=http://127.0.0.1:5108/
```

Validation after recycle:

- `plutil -lint /Users/matthewfurnari/Library/LaunchAgents/com.buffaly.agent.staging.5108.plist` returns `OK`.
- `http://127.0.0.1:5108/buffaly-agent.html` returns HTTP 200.
- Running web process loads runtime files from `/Users/matthewfurnari/buffaly-mac/dotnet/shared/Microsoft.NETCore.App/9.0.17`.
- Process environment includes the pinned `DOTNET_ROOT`, `PATH`, `BUFFALY_INSTALL_ROOT`, and `ASPNETCORE_URLS` values above.

## Required long-term installer fix

The installer should stop staging installs from borrowing another Buffaly install's runtime. Each install root should own or receive a compatible runtime and launch configuration.

Implement the following:

1. Install or bundle a compatible .NET runtime under the target install root, for example:

   ```text
   <install-root>/dotnet/dotnet
   <install-root>/dotnet/shared/Microsoft.NETCore.App/9.x
   <install-root>/dotnet/shared/Microsoft.AspNetCore.App/9.x
   ```

2. Generate LaunchAgent plist files that set:

   ```text
   DOTNET_ROOT=<install-root>/dotnet
   PATH=<install-root>/dotnet:/opt/homebrew/bin:/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin
   BUFFALY_INSTALL_ROOT=<install-root>
   DOTNET_ENVIRONMENT=Production
   ```

3. Ensure `agent-web`, spawned `agent-worker` processes, and tool/harness child processes inherit those variables.

4. Do not rely on Homebrew/system `dotnet` for Buffaly runtime execution. Homebrew may contain only .NET 10 while Buffaly currently targets `net9.0`.

5. Validate the install-local runtime before launching:

   ```bash
   <install-root>/dotnet/dotnet --list-runtimes
   ```

   It must show `Microsoft.NETCore.App` and `Microsoft.AspNetCore.App` versions compatible with all installed `*.runtimeconfig.json` files.

6. After launch, validate the live web process:

   ```bash
   lsof -p <web-pid> | grep Microsoft.NETCore.App
   ```

   The paths should point under:

   ```text
   <install-root>/dotnet/shared/Microsoft.NETCore.App/...
   ```

7. Add installer regression coverage for this scenario: launching a `net9.0` Buffaly tool/harness must succeed even when `/opt/homebrew/bin/dotnet` is .NET 10-only.

## Acceptance criteria

- Fresh Mac staging install has its own compatible runtime under `<install-root>/dotnet`.
- Generated LaunchAgent plist validates with `plutil -lint`.
- Generated LaunchAgent starts `agent-web` with install-local `DOTNET_ROOT` and `PATH`.
- `agent-web`, `agent-worker`, and tool child processes inherit install-local runtime environment.
- A `net9.0` tool/harness launch succeeds on a machine whose Homebrew/system dotnet is .NET 10-only.
- No new staging install depends on `/Users/matthewfurnari/buffaly-mac/dotnet`.