# SillyTavern 长篇小说制卡 Agent Skills

本目录存放用于自动化构建与升级 **SillyTavern V2/V3 长篇小说角色卡（v3.9 架构）** 的 Agent Skill 规范与配套前端模板。

## 包含技能

- **[`sillytavern-pixiv-card/`](sillytavern-pixiv-card/)**：长篇 / Pixiv 小说酒馆角色卡 v3.9 制作与升级工作流
  - [`SKILL.md`](sillytavern-pixiv-card/SKILL.md)：完整工作流指令（全本 100% 子开局切片、正则套正则按需分包、第三人称沙盒与双姝共受模式、男主多路线定制、女主换名换设定物理隔离、跨幕随行防丢人世界书、防 CoT 碰撞状态栏等）
  - [`references/panel_template.html`](sillytavern-pixiv-card/references/panel_template.html)：0 楼可拖拽按需分包开局选择面板参考模板（支持自由探索 / 男主 5 路线定制 / 女主本尊与自定义换名覆写，以及保留男配 / 纯女角净版切换）
  - [`references/status_template.html`](sillytavern-pixiv-card/references/status_template.html)：顶栏状态条 + 可折叠「调度」抽屉参考模板（支持跨幕随行同伴双源检测、多场地合理同场衔接、三阶段事件追踪、`chatMetadata` 持久化与任意子段跳转）

## 安装与使用方式

将 `sillytavern-pixiv-card` 文件夹复制到你所使用的 AI 编程助手 Skills 目录即可自动识别触发：

- **Workspace 级（推荐）**：`<你的制卡项目根目录>/.agents/skills/sillytavern-pixiv-card/`
- **Antigravity / Gemini 全局级**：`~/.gemini/config/skills/sillytavern-pixiv-card/`
- **Claude Code / Codex 全局级**：`~/.claude/skills/sillytavern-pixiv-card/` 或 `~/.codex/skills/sillytavern-pixiv-card/`
