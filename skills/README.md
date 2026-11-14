# SillyTavern 长篇小说制卡 Agent Skills

本目录存放用于自动化构建与升级 **SillyTavern V2/V3 长篇小说角色卡（v3.0 架构）** 的 Agent Skill 规范与配套前端模板。

## 包含技能

- **[`sillytavern-pixiv-card/`](sillytavern-pixiv-card/)**：长篇 / Pixiv 小说酒馆角色卡 v3.0 制作与升级工作流
  - [`SKILL.md`](sillytavern-pixiv-card/SKILL.md)：完整工作流指令（全本章节扫描、原著原文开局切片、正向里程碑阶段解耦世界书、4 条卡片级局部正则、JS 宏转义安全校验等）
  - [`references/panel_template.html`](sillytavern-pixiv-card/references/panel_template.html)：0 楼交互式主从分幕开局选择面板参考模板（支持自由探索沙盒 / 男主视角 / 女主视角，以及保留可截胡 NPC / 清退反派切换）
  - [`references/status_template.html`](sillytavern-pixiv-card/references/status_template.html)：顶栏状态条 + 可折叠「剧情控制」抽屉参考模板（实时统计本段回合数、三阶段事件进度指示、`chatMetadata` 持久化与下一节点自然衔接注入）

## 安装与使用方式

将 `sillytavern-pixiv-card` 文件夹复制到你所使用的 AI 编程助手 Skills 目录即可自动识别触发：

- **Workspace 级（推荐）**：`<你的制卡项目根目录>/.agents/skills/sillytavern-pixiv-card/`
- **Antigravity / Gemini 全局级**：`~/.gemini/config/skills/sillytavern-pixiv-card/`
- **Claude Code / Codex 全局级**：`~/.claude/skills/sillytavern-pixiv-card/` 或 `~/.codex/skills/sillytavern-pixiv-card/`
