# SteamTools 交互方式调查：经典悬浮窗 vs 当前 Rust v0.2.3

记录日期：2026-10-12

## 用户当前习惯（经典 SteamTools 1.8 体系）

- A：外部资源获得 Lua 文件，拖入桌面悬浮图标。
- B：把 Steam 商店/库游戏、DLC 列表等项目拖入悬浮图标；按住左 Ctrl 再拖通常触发取消解锁记录，**不代表删除本地游戏文件**。
- 依据：[SteamManifestCache SteamTools Wiki](https://github.com/pjy612/SteamManifestCache/wiki/SteamTools)、[插件说明](https://github.com/pjy612/SteamManifestCache/wiki/SteamTools_Plugin)。
- 警告：这套经典版手势**不可自动套用**到本 Fork。

## 本 Fork 的真实实现

- [中文 README](https://github.com/SteamToolsProject/SteamTools/blob/master/docs/README_ZH.md)：Steam 商店详情页「入库」按钮，右键菜单「刷新清单 / 移除入库」。
- [`pages.tsx`](https://github.com/SteamToolsProject/SteamTools/blob/master/crates/stt-steamui/ui/src/pages/pages.tsx)：SteamTools 配置面板「脚本目录」拖入 `.lua`、文件选择器；`lua_drop` 需启用；导入到 `config/lua` 后热重载。
- [`import_local.rs`](https://github.com/SteamToolsProject/SteamTools/blob/master/crates/stt-config/src/import_local.rs)：对 Lua 做 DSL 识别、内容检查、落盘/热重载；从路径导入和从面板导入是不同代码入口。
- DLC：商店侧允许勾选扩展列表，具体依赖目录源和下载数据。
- 源码没有观察到经典的“桌面常驻悬浮图标+拖放链接+Ctrl 删除”完整交互模块。

## 维护建议（仅记录，尚未立项）

1. 优先确认用户实际安装版本（经典版 1.8.x vs Rust v0.2.3），避免为不同程序查错。
2. 如果要求**保留经典版手势**，再单独评估一个外置悬浮窗适配层（增删记录、导入 Lua、状态展示、避免碰触 Steam 原生文件）。
3. 不把“入库成功”等同于获取正式所有权、合法下载许可、内容安装完成或 Steam Cloud 可用。
