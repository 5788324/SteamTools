# PATCH-003 · 云存档上传问题调查单

- 状态：**Research / 未开发 / 未实机复现**
- 优先级：P2
- 基线：SteamToolsProject/SteamTools v0.2.3，2026-10-12
- 维护分支：`personal-patches`
- 目标：诊断 Steam Cloud 无法上传/同步冲突，研究可恢复的个人存档备份和可选 CloudRedirect 适配。
  
## 上游与第三方依据

1. [OpenSteamTool Issue #116](https://github.com/OpenSteam001/OpenSteamTool/issues/116)：存在云同步失败反馈。
2. [OpenSteamTool PR #138](https://github.com/OpenSteam001/OpenSteamTool/pull/138)：合并了 CloudRedirect 兼容相关代码，不代表 Rust SteamTools v0.2.3 已支持。
3. [OpenSteamTool PR #153](https://github.com/OpenSteam001/OpenSteamTool/pull/153)：修复 Cloud RPC 时序/潜在 BeginFileUpload、CommitFileUpload 超时问题。
4. [CloudRedirect](https://github.com/Selectively11/CloudRedirect)：第三方存档同步/重定向，与 Valve 原生 Steam Cloud 不是一回事。其版本兼容及存档冲突/丢失风险需独立验证。
5. 目前 SteamTools v0.2.3 源码树未找到完整专用云同步实现；**没有原生账号授权不能通过本地补丁凭空获得云端权限**。

## 技术边界

- 已购买游戏原生 Steam Cloud：若失败，优先按客户端网络、游戏 AppID、Auto-Cloud 文件路径、Steam 用户和游戏本身支持状况诊断。
- 非原生或未授权云端：不承诺真正上传 Valve 服务器；可考虑**在用户自有空间备份**。
- 独立存档同步应优先“不覆盖唯一存档”，支持冲突提示、版本保留、校验和手动恢复。
- 不在未验证兼容的情况下将旧版 SteamTools、OpenSteamTool 和 Rust SteamTools 的 `dwmapi.dll` / `xinput1_4.dll` 混装。

## 待办与验收

- [ ] 确定用户遇到的是云端上传失败、同步冲突、云端状态错误，还是某游戏根本不支持云存档。
- [ ] 获取用户授权的可复现样例和脱敏日志，区分 Steam 原生云与第三方后端。
- [ ] 审查本项目有无已存在的相关拦截点、可用状态事件和隔离接入方式。
- [ ] 比较原生问题诊断、独立文件备份和 CloudRedirect API 适配的维护成本。
- [ ] 建立备份 → 同步 → 冲突处理 → 恢复测试，必须验证不影响已购买游戏的原生云同步。
- [ ] 上游完整修复并实机验证后，撤回对应私人补丁。

结论：**已有其他项目实现路径，但本项目尚未验收或集成，不可声称已解决。**
