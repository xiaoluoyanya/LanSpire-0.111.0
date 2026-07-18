# Changelog

All notable changes to LanSpire are documented in this file.

## [v0.11.0] - 2026-07-18

### Changed
- LAN message IDs now follow the running game's `INetMessageSubtypes.All` order at runtime, then append LanSpire custom messages (name sync, passphrase, ping).
- One build can load on STS2 v0.107.1 and v0.109.0 without a hard-coded per-version table. Peers still must share the same game version.
- Fallback stays on the v0.107.1 order only if the live game table cannot be read.

### Notes
- Compile-checked against STS2 v0.107.1 and v0.109.0.
- Gameplay / full co-op run on v0.109.0 not playtested yet.
- Cross-play between different STS2 versions is not supported.

## [v0.10.0]

Previous release. Built against STS2 v0.107.1 only.
