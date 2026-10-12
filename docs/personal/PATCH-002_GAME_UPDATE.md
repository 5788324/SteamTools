# PATCH-002 · 游戏更新问题调查单

- 状态：**Research / 未开发 / 未实机复现**
- 优先级：P1
- 基线：SteamToolsProject/SteamTools v0.2.3，2026-10-12
- 维护分支：`personal-patches`
- 目标：确认为什么游戏安装后不显示更新、仍在旧版、刷新清单不生效或下载更新提示「无网络连接」，给出最小诊断及修复补丁。
  
## 上游证据

1. [SteamTools v0.2.3 CHANGELOG](https://github.com/SteamToolsProject/SteamTools/blob/master/CHANGELOG.md)：ManifestDeX 替代失效请求码源，解决**一部分** Access Denied / No connection。它不等于自动检测游戏新版本。
2. [SteamTools `catalog_add.rs`](https://github.com/SteamToolsProject/SteamTools/blob/master/crates/stt-config/src/catalog_add.rs)：生成的 `stt_{id}.lua` 写 `setmanifestid(depot, gid)`，并支持受管项目重新拉取清单。
3. [SteamTools `manifest.rs`](https://github.com/SteamToolsProject/SteamTools/blob/master/crates/stt-steamclient/src/manifest.rs)：实际将指定 Depot 的 Manifest GID 改写为 Lua 配置值；**固定旧 GID 可能抑制版本发现**。
4. [OpenSteamTool Issue #101](https://github.com/OpenSteam001/OpenSteamTool/issues/101)：类似自动更新问题，作者关闭时认定是 Manifest 绑定导致；不意味着 SteamTools 已实现完整更新。
5. [OpenSteamTool Issue #102](https://github.com/OpenSteam001/OpenSteamTool/issues/102)：运行时解绑清单后不触发更新；PR #109 的测试构建得到测试者确认修复。注意这是**另外一个项目**。
6. [OpenSteamTool Issue #195](https://github.com/OpenSteam001/OpenSteamTool/issues/195)：类似「无网络连接」，独立区分请求码/Hook/Steam 授权。

## 需要区分的根因

- 游戏版本更新 ≠ SteamTools 程序本身升级。
- Manifest GID 被 `setmanifestid` 固定，可能一直指向旧版。
- Catalog 数据源本身未刷新或 Lua 热重载/游戏状态未重新识别。
- 请求码失效、Depot key/Access token 缺失、Steam 客户端更新造成 Pattern/Hook 不匹配。
- 真正的内容授权、网络或 Steam 客户端下载错误，不能一概认定是本工具故障。

## 待办与验收

- [ ] 收集具体游戏 AppID、Steam 构建号、SteamTools 版本、复现时间（只收脱敏日志）。
- [ ] 在**用户合法拥有及有权限更新的测试内容**中，复现旧版安装 → 新版发布 → 检测提示。
- [ ] 记录每个 Depot 当前/目标 GID、固定模式、来源时间，保证不显示虚假的更新成功状态。
- [ ] 检查刷新清单 → Lua 热重载 → 宿主快照 → Manifest hook 的闭环。
- [ ] 先提供诊断、显式刷新和可回滚配置，不默认修改所有游戏绑定。
- [ ] 通过 Windows/Steam 实机安装、更新、回滚回归测试后，才允许进入个人发布版本。
- [ ] 每次同步官方先查相同修复；官方真正修复且通过相同用例复测，标记 Superseded 并移除补丁。

结论：**问题有上游实现层面的强嫌疑，但还不能断言特定用户环境的唯一根因；当前未修复。**
