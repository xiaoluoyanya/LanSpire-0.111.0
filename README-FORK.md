<div align="center">

# LanSpire v0.111.0 Patch

**在 Madlezz 的 [LanSpire](https://github.com/Madlezz/LanSpire) 基础上，适配《杀戮尖塔 2》v0.111.0 的修复分支**

MIT License · 原项目保留全部版权声明

</div>

---

## 这个分支改了什么

游戏更新到 v0.111.0 后，`NetHostGameService` 的构造函数新增了 `PeerVersionInfo` 参数（版本握手机制），导致原版 LanSpire v0.11.0 启动局域网主机时报错：

```
System.MissingMethodException: Method not found: 'Void NetHostGameService..ctor()'
```

### 修复内容

| # | 改动 | 文件 |
|---|------|------|
| 1 | `new NetHostGameService()` → `new NetHostGameService(PeerVersionInfo.LocalDefault())`（两处） | `Patches/LanMultiplayerPatches.HostFlow.cs` |
| 2 | 改写 C# 14 预览语法（null 条件事件赋值），兼容 .NET 9 SDK 正式版编译 | 同上 |
| 3 | `mod_manifest` 声明 `min_game_version: 0.111.0` | `LanSpire/LanSpire.json` |

> 💡 `PeerVersionInfo` 是 0.111.0 新增的版本握手结构（含 version / branch / mods 字段），官方提供 `LocalDefault()` 静态工厂。

## 安装

1. 从 [Releases](../../releases) 下载 `LanSpire.zip`
2. 解压到游戏目录的 `mods` 文件夹
3. 主菜单 → Multiplayer → **Host LAN** / **Join LAN**

**要求**：
- 游戏 v0.111.0（原版 LanSpire 适配 0.107.x/0.109.0，请勿混用）
- 所有联机玩家使用相同的游戏版本和模组版本
- 无其他前置依赖

## 从源码构建

```bash
git clone https://github.com/Madlezz/LanSpire LanSpire-upstream
cd LanSpire-upstream
# 应用本分支的修复（或直接使用本仓库）
dotnet build -c Release -p:STS2_GAME_DIR="<你的游戏目录>"
```

- 需要 .NET 9 SDK（游戏运行时为 .NET 9）
- Release 构建会自动把 DLL 部署到 `<游戏目录>\mods\LanSpire\`

## 已知问题

- 反序列化遇到未注册模型时（如双方游戏版本不一致）会直接抛异常，导致客户端卡在选人界面。计划改为跳过未知模型以实现跨版本容错。
- 主机侧偶发 `ObjectDisposedException`（重复切换就绪时），不影响开局。

## 致谢

- [Madlezz](https://github.com/Madlezz) — 原项目作者（MIT License）
- 原项目所有贡献者

## 许可证

MIT License — 原版权声明（Copyright (c) 2026 Madlezz）完整保留，见 [LICENSE](LICENSE)。
本分支的修改部分同样以 MIT 发布。
