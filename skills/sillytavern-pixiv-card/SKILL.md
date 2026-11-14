---
name: sillytavern-pixiv-card
description: Build or upgrade Pixiv novel SillyTavern V2/V3 character cards (v3.0 architecture). Use when the user asks to create, refactor, or upgrade a Pixiv character card (角色卡/酒馆卡), extract full novel openings (开局), build stage-decoupled worldbooks with Zhetian-style plot control (剧情控制), or redesign card UI panels and status bars.
---

# SillyTavern Pixiv Card Maker & Upgrader (v3.0 Architecture)

Convert or upgrade Pixiv novel TXT/EPUB works into a **single unified SillyTavern V2/V3 character card (`v3.0`)** featuring:
1. **0th-Floor Interactive Master-Detail Opening Panel (`panel_template.html`)** with 3 playable modes (`自由探索·沙盒介入` / `男主视角` / `女主视角`) and an antagonist policy switch (`保留可截胡NPC` vs `清退原著反派独享舞台`).
2. **Stage-Decoupled Worldbook (`scan_depth: 4`)** with Zhetian-style positive node entries (`<ns_scene>`: `此前已经发生` + `节点起点人物档案` + `近期事件序列 1→2→3` + `承接方法`) — **never** using negative "unoccurred boundary" spoilers.
3. **Compact Dark-Gold Status Bar + Expandable Plot Control Drawer (`status_template.html`, `卷六 · 剧情控制`)** with live round counting, 3-stage pacing display, target preview, `chatMetadata` persistence, and natural bridge transition prompts.
4. **Strict Directory & Version Hygiene** (`更新v3.0/` + `历史版本/` archive).

Reference implementation & templates:
- Benchmark cards under `./角色卡/PIXIV量产卡/更新版v3/`:
  - `./角色卡/PIXIV量产卡/更新版v3/月见_深渊魔帝艾拉蒂雅/` (Dark-Gold Obsidian Scroll UI)
  - `./角色卡/PIXIV量产卡/更新版v3/月见_学院女神/` (Ivory Alabaster & Royal Violet-Gold Cathedral Codex UI)
- Reusable UI templates in this skill folder:
  - `references/panel_template.html`
  - `references/status_template.html`

---

## Workflow Overview

```
Pixiv Novel Sources (check ALL TXT editions: remake + 旧版 + 番外)
  → ① Output Directory Setup: create clean card folder under `./角色卡/PIXIV量产卡/更新版v3/<作者>_<作品名>/` (leave `量产v1/` and `更新版v2/` untouched as read-only historical sources)
  → ② Full Novel Coverage Check: scan all acts/chapters/status sheets across all TXT files
  → ③ Extract Openings (`开局/<篇章>/*.md`, 1,300–1,800 chars each, verbatim novel prose)
  → ④ Build Structured `源码/nodes_v3.json` (positive milestone + cast snapshot + 3-step `events` + `body`)
  → ⑤ Design Bespoke Theme UI & Assemble Card via `源码/build_v3.py` (4 Blue + Mode/Char/Lore Green + N Node Green + 4 Regex)
  → ⑥ Verify (`node --check` on extracted JS, PNG `chara`+`ccv3` chunks, zero placeholders, regex simulation)
  → ⑦ (If requested) Deploy to SillyTavern server & pre-authorize `character_allowed_regex`
```

---

## Step ①: Directory Hygiene & Version Management (`更新版v3/`)

All `v3.0` upgraded cards MUST be placed in `./角色卡/PIXIV量产卡/更新版v3/<作者>_<作品名>/`:
1. **Keep `量产v1/` and `更新版v2/` Untouched**: Treat `./角色卡/PIXIV量产卡/更新版v2/` as a read-only reference for existing cover images (`image.png` — verify header is true PNG `b"\x89PNG\r\n\x1a\n"`, not JPEG!) and character notes (`世界观梗概.md`, `女角色介绍.md`, `剧情梗概.md`).
2. **Standard `更新版v3/<作者>_<作品名>/` Directory Layout**:
   ```
   ./角色卡/PIXIV量产卡/更新版v3/<作者>_<作品名>/
   ├── <卡名>_v3.0.png               # V2 (`chara`) + V3 (`ccv3`) dual-chunk PNG card
   ├── <卡名>_v3.0.json              # Full character card JSON
   ├── <卡名>_世界书_v3.0.json        # Standalone ST worldbook (`entries` + `originalData`)
   ├── 剧情节点大纲_v3.0.md           # Auto-generated full node outline
   ├── 论坛简介.md                   # Short, literary, non-AI-flavored story introduction
   ├── 说明.md                       # Architecture & changelog documentation
   ├── image.png                    # Valid PNG cover image
   ├── build.py                     # Thin wrapper delegating to 源码/build_v3.py
   ├── 开局/                         # Markdown opening files grouped by act/edition
   │   ├── 上篇-.../
   │   └── 下篇-.../
   └── 源码/
       ├── build_v3.py              # Reproducible build script
       ├── nodes_v3.json            # Single source of truth for all opening nodes
       ├── panel_template.html      # Bespoke opening panel template
       ├── status_template.html     # Bespoke status bar & plot control template
       ├── panel_v3.html            # Rendered opening panel (for inspection & `node --check`)
       └── status_v3.html           # Rendered status bar (for inspection & `node --check`)
   ```

---

## Step ②: Full Novel Coverage & Character Status Sheet Audit

Before writing openings or worldbook entries:
1. Check **every** file for the work in `./资料/小说资源/PIXIV/<作者>/` (many Pixiv works have both a `remake.txt` and a much longer `旧版.txt` or `第二幕/番外`).
2. Scan all chapter headings (`序幕`, `间章`, `第一幕`, `第二幕`, `番外`, `IF`) and line ranges via a Python script to ensure **zero acts or chapters are omitted**.
3. Scan for author-embedded character sheets (`具体人物属性`, `种族：`, `能力：`, `装备：`) and incorporate those exact stats/skills into the character worldbook entries.
4. **Content Safety Rule during Tool Calls**: Process novel text on disk using Python scripts; print only structural metadata (line numbers, char counts, sanitized headings) to stdout, and use literary terminology in Python source code so tool calls never trigger stream filters.

---

## Step ③: Extract Openings (`开局/`) & Build `nodes_v3.json`

### Opening Text (`body`) Rules:
- **Length**: 1,300–1,800 Chinese characters per opening, sliced directly from the novel's key scene start points.
- **Fidelity**: Use original novel prose, dialogue, and sensory details directly; do not summarize into dry outline text.
- **Granularity**: Split long chapters into 2–5 sequential nodes so every major scene shift, new character debut, or climax has its own entry point.

### Node Schema (`nodes_v3.json`):
Each node in `nodes_v3.json` MUST follow the **Zhetian-style positive node schema** (do **NOT** include `unoccurred_boundary` or negative "future chapter X hasn't happened" text):

```json
{
  "id": 0,
  "code": "R1",
  "arc_id": "remake_prologue",
  "arc_name": "remake序幕 — 魔帝的日常与试炼 (R1-R11)",
  "short_title": "便器总督的就职仪式",
  "full_title": "【R1】便器总督的就职仪式 — 艾拉蒂雅降临黑水城，救下柯露蜜儿",
  "subtitle": "序幕第一章 便器总督的就职仪式",
  "prev_code": "",
  "prev_title": "",
  "next_code": "R2",
  "next_title": "魔帝の排卵期",
  "location": "魔界·黑水城中央广场",
  "eilatia_stage": "无敌魔帝全盛期（初次接触爱欲宝珠余波）",
  "eilatia_equipment": "魔帝日常黑金华服（无任何拘束具或印记）",
  "eilatia_mental": "唯我独尊、冷酷高傲，随手抹杀叛军，对世俗情欲毫无概念。",
  "active_characters": {
    "柯露蜜儿": "黑水城代理总督，刚从叛军手中获救，左臀留有烙印，右臀被魔帝签下总督花体字。",
    "弗弗拉奇": "潜伏在侧的盗匪头目，怀揣爱欲魔神宝珠。"
  },
  "milestone": "艾拉蒂雅平定魔界群神登顶魔帝后，因黑水城爆发叛乱而首次亲临边境城邦。",
  "plot_detail": "艾拉蒂雅从天而降瞬杀黑水城叛党，救下受辱的代理总督柯露蜜儿，并以魔力在其右臀签下「总督」二字命其继续治城。",
  "events": [
    "艾拉蒂雅降临黑水城广场，以压倒性魔神伟力瞬杀叛乱首领卡尔珀与匪帮。",
    "审视幸存的代理总督柯露蜜儿，在她右臀签下「总督」花体字，确立其双重身份。",
    "暗处的爱欲魔神宝珠悄然产生微弱共鸣，为后续皇城异变埋下伏笔。"
  ],
  "body": "（原著开场正文 1300~1800 字）"
}
```

---

## Step ④: Stage-Decoupled Worldbook Architecture (`scan_depth: 4`)

### 1. Main Card Fields (`description` + `personality` <= 1,000 chars total)
- Include ONLY worldview fundamentals, baseline appearance, core abilities, and baseline personality/speech habits.
- **Never** put late-stage corruption, cursed outfits, or pregnancy states into `description` or `personality`. Explicitly state that attire, physical state, and relationships are determined by the active `【开局节点·XX】`.

### 2. Four Constant Blue Entries (`Order 100..103`, ~2,000 chars total)
- `Order 100` (`depth: 4`): `★核心设定·演出守则` — Defines the 3 modes (`free` sandbox mode, `male` conqueror POV, `female` heroine POV) and the dynamic state continuity principle.
- `Order 101` (`depth: 4`): `★篇章目录与路线脉络` — Clean directory of all routes and node codes (`R1~R11`, `旧1~旧11`, `一1~一19`, `二1~二19`, etc.), linking sequential arcs where appropriate (e.g. `一19 -> 二1`).
- `Order 102` (`depth: 2`): `★本回合剧情调度与推进规则` — Instructs the AI to pace each chapter across its 3 sequential events (`.1 → .2 → .3`) without rushing all events in a single turn, and to switch nodes only when `【阶段推进确认】` is received.
- `Order 103` (`depth: 1`): `★状态栏输出协议` — Defines the `[<NS>_STATUS] ... [/<NS>_STATUS]` block appended at the end of every reply.

### 3. Selective Mode & Character/Lore Green Entries (`Order 105..225`)
- `Order 105`: `模式·自由探索沙盒法则` (keys: `[["<NS>_VIEW:free"]]`, `[["<NS>_VIEW:original"]]`, `depth: 2`) — Zero preset identity, anchors `<user>`'s self-defined identity/actions in Turn 1, enables full butterfly-effect plot rewriting.
- `Order 106`: `模式·原著反派可截胡与击杀` (keys: `[["<NS>_NPC:keep"]]`, `depth: 2`) — Original male antagonists have zero plot armor; `<user>` can seize their artifacts, defeat, or kill them at any time.
- `Order 107`: `模式·清退原著反派独享舞台` (keys: `[["<NS>_NPC:erase"]]`, `depth: 2`) — Original male antagonists are removed; `<user>` has exclusive agency over all heroines and key items.
- `Order 200..215`: Stage-neutral character profiles (`depth: 4`).
  - **Important**: Any entry for original male antagonists (`弗弗拉奇`, `图兹`, etc.) MUST set `selective: True` and `secondary_keys: ["[[<NS>_NPC:keep]]"]` so it never triggers in `male` POV or `erase` mode!

### 4. Unified Single-Node Green Entries (`Order 300..300+N`, `depth: 2`)
- **Exactly 1 green entry per node** (never duplicate `节点·XX` and `开局强关联·XX`), triggered by `[["<NS>_STAGE:<code>"]]` and `[["<NS>_OPENING:<code>"]]`.
- Format using the **Zhetian-style `<eilatia_scene>` positive template**:
  ```
  <eilatia_scene id="{code}">
  # 【开局节点·{code}】{short_title}
  所属篇章：{arc_name}
  时间与阶段：{eilatia_stage}
  当前地点：{location}

  【此前已经发生】
  {milestone}

  【该节点开始时的人物档案】
  · 女主（{name}）
    当前着装与配饰：{eilatia_equipment}
    当前状态与心理：{eilatia_mental}
  {active_characters_lines}

  【近期事件序列（按以下顺序逐步展开）】
    1. {code}.1：{events[0]}
    2. {code}.2：{events[1]}
    3. {code}.3：{events[2]}
  - 场景脉络补充：{plot_detail}

  【承接方法】
  以上资料记录本节点起点状态。后续位置、衣着、身体变化与人物关系以本局已经发生的变化更新；人物行动根据各自的目标、手段和知情范围展开。
  每轮回复优先承接上一轮正在发生的事，使当前阶段产生一项具体可见的互动结果，再逐步推向下一项近期事件，避免单轮速通全部事件。
  【后续相关入口】{next_code} · {next_title}
  </eilatia_scene>
  ```

---

## Step ⑤: UI Design & Regex Scripts (`panel_template.html` & `status_template.html`)

### Aesthetic & Layout Guidelines (Bespoke Theme Per Card — Avoid Visual Fatigue!):
- **NEVER reuse the exact same color palette and layout structure across every card.** Each card MUST have a **bespoke visual theme and layout** tailored to its worldview, heroine temperament, and story atmosphere:
  - *Dark Fantasy / Demon Realm* (e.g. `深渊魔帝艾拉蒂雅`): **Dark-Gold Scroll & Obsidian** (`#110f0d` / `#c9a84c`), 2-column left-right Master-Detail node browser.
  - *Academy / Holy Cathedral / Knighthood* (e.g. `学院女神·塞西莉亚`): **Ivory Alabaster & Royal Violet-Gold Cathedral Codex** (`#f6f2ea` / `#3a2b52` / `#b08536`), crest banner + 3-column compact node matrix grid + bottom split dossier (left: connected vertical 3-step timeline, right: warm parchment manuscript reader) + progress track bar in the status strip.
  - *Modern Urban / Sci-Fi / Honkai / Xianxia / Gothic*: Design a distinct palette and component layout (e.g., Frosted Glass & Cyan-Silver HUD, Jade & Ink-Wash Scroll, Crimson Velvet & Brass Salon, etc.) that matches the work's setting while remaining refined, readable, and un-cluttered.
- **Opening Panel (`panel_template.html`) Core Functional Elements**:
  - Section 1: 3 horizontal mode cards (`自由探索 · 沙盒介入` / `男主视角` / `女主视角`) + inline `原著反派设定` switch (`保留为普通 NPC` vs `清退原著反派`) when `自由探索` is selected.
  - Section 2: Route/Volume filter tabs with node counts + **Interactive Node Browser** (e.g. 2-column master-detail list OR 3-column matrix grid + split timeline/manuscript dossier) displaying location/stage/equipment chips, `1 → 2 → 3` event sequence, and scrollable opening text preview.
  - Section 3: Collapsible `<details>` for custom opening notes + bottom launch bar.
- **Status Bar & Plot Control (`status_template.html`) Core Functional Elements**:
  - **Compact Top Strip**: Displays current node badge, location chip, mode chip, live round counter (`本段 X / N 回合 · 阶段 1/3`), plot control drawer toggle button, and `推进下一章 ▶` quick button.
  - **Expandable Plot Control Drawer (Zhetian Vol. 6 Style)**:
    - Counts actual user-assistant rounds in `window.parent.SillyTavern.getContext().chat` since the current stage marker.
    - Displays the current node's 3 sequential events (`1 / 2 / 3`) and highlights the active phase.
    - Provides a grouped `<select>` (`接下来发展到`) with live target preview, configurable round limit (`每段回合数`, default 8) and auto-prompt checkbox persisted in `chatMetadata`, plus `注入选中剧情 ▶`, `保存设置`, and `本段重新计数 ↺` buttons.

### Four Required Card-Scoped Regex Scripts:
1. **Opening Panel Renderer**: `/\[\[<NS>_OPENING_PANEL\]\]/g` → `panel_html` (`placement: [1, 2], markdownOnly: True, promptOnly: False, runOnEdit: True`).
2. **Hidden Trigger Key Hider**: `/(?:<div style="display:none">[\s\S]*?【\/开局阶段触发键】\s*<\/div>|<!--[\s\S]*?【\/开局阶段触发键】[\s\S]*?-->)/g` → `""` (`placement: [1, 2], markdownOnly: True, promptOnly: False, runOnEdit: True`).
3. **Status Bar Renderer**: `/\[<NS>_STATUS\]([\s\S]*?)\[\/<NS>_STATUS\]/g` → `status_html` (`placement: [1, 2], markdownOnly: True, promptOnly: False, runOnEdit: True`).
4. **History Status & Trigger Key Stripper (Critical for `scan_depth: 4`)**:
   - `findRegex`: `/(?:<div style="display:none">[\s\S]*?【\/开局阶段触发键】\s*<\/div>|<!--[\s\S]*?【\/开局阶段触发键】[\s\S]*?-->|\[<NS>_STATUS\][\s\S]*?\[\/<NS>_STATUS\])/gm`
   - `replaceString`: `""`
   - `placement`: `[1, 2]`, `markdownOnly: False`, **`promptOnly: True`**, `runOnEdit: True`, **`minDepth: 2`**.

### JavaScript Safety Rules Inside SillyTavern Regex HTML:
- **NEVER** include literal `<user>`, `{{user}}`, or HTML entities (`&amp;`, `&lt;`, `&gt;`, `&quot;`) inside `<script>` tags—SillyTavern's macro substitution and entity decoding will corrupt the JS before execution!
- Construct `<user>` via `String.fromCharCode(60) + "user" + String.fromCharCode(62)` and `&` via `String.fromCharCode(38)`.
- Always extract `<script>` blocks from generated `panel_v3.html` and `status_v3.html` and run `node --check` to guarantee zero syntax errors.

---

## Step ⑥: Build & Verification Checklist

Every build MUST verify:
1. `node --check` passes on both `panel_v3.html` and `status_v3.html`, and neither file contains literal `&amp;`, `&lt;`, `&gt;`, `&quot;`, `<user>`, or `{{user}}`.
2. PNG file contains valid `chara` and `ccv3` `tEXt` chunks with `scan_depth == 4`.
3. Standalone worldbook JSON (`<卡名>_世界书_v3.0.json`) contains top-level `"entries"` (keyed by string UID) AND `"originalData": character_book` (required by SillyTavern's world-info loader).
4. Zero node entries contain legacy placeholders (`"未出现或对应阶段"`, `"只锁定本开局"`, `"演绎范围与未发生边界"`, or `"next_code":"next_title:"`).
5. **`论坛简介.md` Style Rule**: Keep `论坛简介.md` short, purely introductory, and written with genuine literary cadence (3–4 evocative narrative paragraphs + a brief 3-line card overview). **NEVER** write bloated feature lists, markdown tables, emoji bullet points, technical specs (`ccv3`, `scan_depth: 4`, hex codes), or AI-marketing buzzwords.

---

## Step ⑦: SillyTavern Server Deployment (Optional, When Requested)

When deploying the assembled card to a remote or local SillyTavern instance (`<SILLYTAVERN_ROOT>/data/default-user/`):
1. Upload `<卡名>_v3.0.png` to `<SILLYTAVERN_ROOT>/data/default-user/characters/<卡名>_v3.0.png`.
2. Delete stale thumbnail cache at `<SILLYTAVERN_ROOT>/data/default-user/thumbnails/avatar/<卡名>_v3.0.png` so the avatar refreshes.
3. Upload `<卡名>_世界书_v3.0.json` to `<SILLYTAVERN_ROOT>/data/default-user/worlds/<卡名>_世界书_v3.0.json`.
4. Ensure `<卡名>_v3.0.png` is present in `<SILLYTAVERN_ROOT>/data/default-user/settings.json` under `extension_settings.character_allowed_regex` so the opening panel and status bar regex scripts render immediately without a permission popup.
