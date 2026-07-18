# Contributing to LanSpire

Thanks for your interest in contributing! This mod is a community project and all contributions are welcome.

## Prerequisites

- **.NET SDK 9.0+**
- **Slay the Spire 2** game files (for build references: `sts2.dll`, `0Harmony.dll`, `GodotSharp.dll`)
- Windows (the mod targets the Windows x86_64 game build)

## Setup

1. Clone the repo:

```bash
git clone https://github.com/Madlezz/LanSpire.git
cd LanSpire
```

2. Tell the build where your game is installed. Either:

```bash
# Option A: environment variable
export STS2_GAME_DIR="/path/to/Slay the Spire 2"

# Option B: local config file (not committed to git)
echo '/path/to/Slay the Spire 2' > .sts2-game-dir
```

3. Build:

```bash
cd LanSpire
dotnet build -c Release
```

Output: `bin/Release/net9.0/LanSpire.dll`  
Release builds also copy dll/pdb/json into `$STS2_GAME_DIR/mods/LanSpire/` when that path exists.

## Pre-commit Hook

The repo ships a pre-commit hook in `.githooks/pre-commit` that:

1. Rejects em dash (U+2014) and en dash (U+2013) in staged `.cs` and `.md` files
2. Builds the project in Release mode and rejects on errors

Requires `STS2_GAME_DIR` or a `.sts2-game-dir` file (no silent personal path fallback).

To enable it:

```bash
git config core.hooksPath .githooks
```

If you skip this, builds will still work but you lose the guardrails.

## Code Style

- **No em/en dashes.** Use regular hyphens (`-`) or double hyphens (`--`) in code and docs. The pre-commit hook enforces this.
- **Nullable is enabled** in the `.csproj`. Prefer null-safe code, especially on reflection paths.
- **No implicit usings.** Shared imports live in `GlobalUsings.cs`; add file-local usings only when needed.
- **Bilingual strings** use `Strings.T("Chinese", "English")`. If you add user-facing text, provide both languages.

## Project Structure

```
LanSpire/
  LanSpire.csproj       - Project file (references game DLLs)
  LanSpire.json         - Mod manifest (loaded by game's mod loader)
  Plugin.cs             - Entry point, Harmony patch orchestration
  GlobalUsings.cs       - Shared usings
  Helpers/
    GameInternals.cs    - Reflected field/method name constants
    LanConfig.cs        - File-based JSON config reader/writer
    NetworkHelpers.cs   - IP parsing, endpoint validation
    PatchHelper.cs      - Logging, Harmony helpers
  Localization/
    Strings.cs          - Bilingual string helper
  Components/
    UiHelpers.cs        - Shared UI styling helpers
  Network/
    LanPlayerNameSyncMessage.cs
    LanPassphraseMessage.cs
    LanPingMessage.cs
  Patches/
    LanMultiplayerPatches.cs            - Partial root, live message map
    LanMultiplayerPatches.Menu.cs       - Host/Join LAN buttons
    LanMultiplayerPatches.Platform.cs   - Player identity, name/passphrase/ping
    LanMultiplayerPatches.Settings.cs   - In-game settings
    LanMultiplayerPatches.JoinHost.cs   - Join flow, scan, retry
    LanMultiplayerPatches.JoinScreen.cs - Join UI, history, pinned host
    LanMultiplayerPatches.HostFlow.cs   - Host flow, IP popup
    LanMultiplayerPatches.Discovery.cs  - UDP discovery
    LanMultiplayerBootstrapPatches.cs   - Defer LAN patches until main menu
  Services/
    SyncStallRecovery.cs
    TimingFailsafes.cs
    DisconnectPrevention.cs
    UpdateChecker.cs
LanSpireTests/
  NetworkHelpersTests.cs
```

## Testing

Unit tests cover pure-logic helpers that do not need a running game UI:

```bash
export STS2_GAME_DIR="/path/to/Slay the Spire 2"
dotnet test LanSpireTests -c Release
```

Current coverage:

- `NetworkHelpers.TryParseEndpoint`
- `NetworkHelpers.TryExtractInlinePort`
- `NetworkHelpers.NormalizeText` (and related helpers in that file)

Before submitting:

1. Build succeeds: `dotnet build -c Release` (in `LanSpire/`)
2. Tests pass: `dotnet test` (project `LanSpireTests`)
3. Manual test: ensure `mods/LanSpire/` has dll + json (Release deploy does this)
4. Launch the game and verify LAN buttons appear in Multiplayer
5. Test host + join with a second player if possible
6. Note which **STS2 game version** you used

## Game version notes

- LanSpire builds LAN message IDs from the running game's `INetMessageSubtypes.All`, then appends its custom messages.
- One mod build can load on multiple STS2 versions if the patched APIs still exist.
- **Players in one lobby must share the same STS2 version.** The mod does not bridge different game versions.
- After a new game patch: rebuild against that `sts2.dll`, check Harmony targets, and smoke-test multiplayer.

## Filing Issues

Use [GitHub Issues](https://github.com/Madlezz/LanSpire/issues). Include:

- STS2 game version
- LanSpire version
- What you expected vs what happened
- Relevant lines from `godot.log` (messages tagged `[LanSpire]`)

## License

By contributing, you agree your contributions are licensed under the MIT license, same as the rest of the project.
