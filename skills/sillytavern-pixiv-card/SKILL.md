---
name: sillytavern-pixiv-card
description: Build or upgrade Pixiv novel SillyTavern V2/V3 character cards (v3.9+ card-specific architecture). Use when the user asks to create, refactor, or upgrade a Pixiv character card (角色卡/酒馆卡), extract 100% full-novel sub-openings (开局), build nested-regex per-act packs (正则套正则), add female companion co-submissive modes (摆法A·女伴同受调教/双姝共受), add multi-archetype male lead routes & custom male/female protagonist name+persona overrides (男主多路线下克上/共犯/自拟 & 女主换名换设定), stitch cross-book Easter-egg arcs with anti-crowding cast toggles (solo/full), preserve introduced or recruited companions across scene transitions (跨幕随行防丢人/切场景保留角色), purify NPC-erased modes, prevent CoT status-bar regex collisions, enhance co-tamable female supporting cast, or deploy cards to SillyTavern servers (archiving prior versions locally while keeping only the latest version on the server).
metadata:
  version: "3.9.1"
---

# SillyTavern Pixiv Card Maker & Upgrader (v3.9.1 Card-Specific Architecture)

> **Guiding Principle — 一张卡一个策略，Skill 只是辅助底盘**:
> Every novel has its own narrative perspective (1st-person vs 3rd-person), world mechanics (cybernetic devices vs abyssal/temple magic constructs), visual aesthetic, and fetish appeal. **Never mechanically clone another card's UI color palette, base POV, or mode switches.** Audit each novel's specific strengths first, preserve its bespoke visual identity (e.g., Dark-Gold Obsidian `#110f0d/#c9a84c` for dark fantasy vs Cyan-Mint for sci-fi), and activate only the mode extensions that genuinely elevate that work.

Convert or upgrade Pixiv novel TXT/EPUB works into a **single unified SillyTavern V2/V3 character card (`v3.9.1` Architecture)** combining universal reliability invariants with tailored per-card gameplay strategies:

1. **Local Archive + Server Latest-Only Versioning Discipline (`本地保留历史版本，服务器只保留最新版`)**:
   - **Local Workspace (`历史版本/v<旧版本号>/`)**: Never overwrite prior local release files unless explicitly instructed — before building a new version, archive all prior card PNG/JSON, standalone worldbook JSON, node outlines, and `源码/` snapshots into `历史版本/v<旧版本号>/` locally, and output the newly bumped `<卡名>_v<新版本号>.png` & `<卡名>_世界书_v<新版本号>.json` in the card root.
   - **SillyTavern Server (Keep Latest Only)**: After uploading and verifying the new `<卡名>_v<新版本号>.png` and `<卡名>_世界书_v<新版本号>.json` on the server, **delete all superseded older versions** of that card from `characters/`, `worlds/`, and `thumbnails/avatar/` (plus prune stale old-version keys from `settings.json` `character_allowed_regex`) so the server only displays the single latest version.
   - Maintain archival history inside this skill folder as well (`history/SKILL_v<old>.md`) whenever upgrading `SKILL.md`.
2. **Decluttered, Draggable 0th-Floor Opening Panel + Chained Nested-Regex On-Demand Packs (`正则套正则`)**:
   - **100% Verbatim Novel Coverage Across Sub-Openings (`.1 ~ .5`)**: Every narrative paragraph of the source novel (`100%` coverage, zero dropped tails) is sliced along natural paragraph/quote boundaries into **3–5 clickable ~450–650 char independent sub-openings per node**, each paired with a **~100–130 char mode-aware prior-story preface (`【前情提要】`)**, toggleable preface display, and a full-node continuous reading toggle.
   - **Chained Nested-Regex Architecture (`正则套正则`)**: Splits the lightweight UI shell (`CATALOG` metadata only) from per-act/per-volume prose data packs (`[[<NS>_PACK:<arc_id>]]` → `<nspack>...</nspack>`, using a single canonical base + sparse `"si:pi"` paragraph patches). Only 1 act's pack (~35–270 KB) mounts in the DOM at a time, eliminating frontend lag even on 500,000+ char multi-volume novels. State (`customNote`, search query, reader height) is preserved across pack swaps via `window.parent.__<ns>PanelStash`.
   - **Bespoke Visual Theme + Grab-Scroll & Height Resizer**: Tailor colors/typography to the novel's genre; include mouse grab-to-scroll (`pointerdown`/`pointermove`), mobile `touch-action: pan-y` isolation, and a bottom height drag resizer (`150px–720px`).
3. **Three Clean Top-Level Modes + Optional "摆法 A" Female Co-Submissive Sub-Stance (`女伴同受调教`)**:
   - **`1. 第三人称 · 自建入场` (`free`)**: Pure 3rd-person cast narration (`女主名 / 她`) + 2nd-person player (`你`), paired with `⚔️ 原著男配` (`keep` 零免死保留 vs `erase` 零残留净版).
     - **Standard Sandbox (`stance: sandbox`)**: 5 grounded, zero-privilege everyday passerby presets + `custom` blank self-definition for free observation, interception, rescue, or domination.
     - **Optional Card-Specific Extension — "摆法 A" Female Co-Submissive Stance (`⛓ 女伴同受调教 / 双姝共受`, `stance: cosub`)**: Recommended when the novel revolves around a proud heroine undergoing trials, bondage, or taming! Keeps top-level modes to 3 clean cards while adding an `🎭 入场定位` switch (`[🗡 自由沙盒路人]` vs `[⛓ 女伴同受调教]`) inside `free` mode:
       - Places `<user>` (`你`) as an **18+ adult female companion** physically bound, tested, humiliated, and tamed **side-by-side with the heroine** in every scene (`4` bespoke companion presets such as `随侍女仆 / 同行修女 / 伴读贵女 / 感官共鸣副印旅者` + `自拟同行女伴`).
       - Pairs seamlessly with `原著男配`: `cosub + erase` = **Pure Yuri / Arcane-Mechanism Dual-Taming** (canon female dominators or temple/device mechanisms co-tame both heroine and female `你`); `cosub + keep` = **Canon Dominator Dual-Taming** (comparative humiliation & linked sub-locks).
   - **`2. 男主视角 · 当场主控与多路线定制` (`male` — 100% On-Site + Multi-Archetype `maleBar` + Custom Name/Persona)**:
     - 2nd-person male lead (`你`) + 3rd-person heroine & female cast, all original male NPCs cleared (`npc_policy: male`), and **`你` MUST be physically on-site in the exact room/venue face-to-face with the heroine across 100% of sub-openings** (zero remote CCTV/balcony/window-gazing spectator hooks).
     - **Never lock `male` mode into a single rigid "suit-wearing omnipotent master" template!** Expose a dedicated `maleBar` sub-toolbar with **4 distinct bespoke on-site male routes + `custom_male` + inline `男主姓名 / 设定补充` inputs (`protag_name` & `protag_bio`)**:
       - `supreme` (`👑 权限主宰`): Classic root-key / master-seal holder commanding the scene from the start; uses `pack.pm` + `n.mm`.
       - `reversal` (`⚡ 下克上·拿捏把柄`), `protector` (`🛡 破局共犯·贴身护卫`), `insider` (`🏠 同居近水楼台`), and `custom_male` (`✨ 完全自拟男主`): Route 0th-floor prose onto the **clean rival-erased 3rd-person base (`pack.pe` + `n.em`)** (so zero forced boss outfits/privileges bleed in!) and append a **role-specific 100%-on-site hook** placing `你` face-to-face with the heroine under that exact dynamic or custom persona.
       - Persist `protag_name` and `protag_bio` inside `[<NS>_STATUS]` and `chatMetadata` so `下一幕 ▶` / `填入跳转 ▶` carries custom male identity across every node.
   - **`3. 女主视角 · 原著本尊 / 自定义女主覆写` (`female` — `canon | custom` + `keep | erase`)**:
     - Immersive 2nd-person (`你`) or 1st-person (`我`) toggle + `原著阵容 (keep) | 纯女角净版 (erase)` stage switch + **`[🌸 原著本尊]` (`canon`) vs `[✨ 自定义女主]` (`custom`)** switch inside `femBar`!
     - When `custom` (`[[<NS>_FEM:custom]]`) is selected, expose inline **自定义女主姓名 (`protag_name`) + 自定义外貌与人设 (`protag_bio`)** inputs + quick-fill preset chips:
       - **Live Quote-Inclusive Name Swap**: Replace all canon heroine names & nicknames both outside and **inside dialogue quotes** (`“...”`, `「...」`, `【...】`), prefaces, node titles, and status displays with `protag_name`.
       - **Worldbook Physical Isolation**: Gate the canon heroine's `<character_profile>` (`Order 200`) behind `secondary_keys: ["[[<NS>_VIEW:free]]", "[[<NS>_VIEW:male]]", "[[<NS>_FEM:canon]]"], selective_logic: 0` (`AND_ANY`) so `Order 200` turns **100% OFF** in `female + custom` (zero canon hair/body/backstory bleed-through!), while `[[<NS>_FEM:custom]]` activates a high-priority custom-heroine override entry and persists `protag_name` / `protag_bio` across scene jumps.
4. **Curated High-Appeal Female Supporting Cast + Co-Taming + Cross-Book Easter-Egg Stitching (`xcast: solo | full`)**:
   - Prefer canon attractive adult female characters or diegetic arcane/automated mechanisms over random profile-less stand-in names; when minor antagonist slots in huge novels require female counterparts in `erase` mode, give them **distinct feminine names with zero substring overlap** against the original male name (`加扎克` → `葛蕾莎`, NEVER `加扎克雅` which trips substring checks and chained `.replace()` doubling).
   - Give every female supporting character (including quiet/stoic bodyguards like `黑发剑姬·黯`) a dedicated worldbook profile with 18+ sensual visual details, psychological soft spots, and explicit **co-taming / co-sub hooks** for both `male` and `cosub`/`erase` modes.
   - **Optional Cross-Book Easter-Egg Stitching & Anti-Crowding Cast Switch (`xcast: solo | full`)**: When another short/medium work by the same author (or a sister work) isn't strong enough as a standalone card, stitch its core heroine and 4–5 signature highlight scenes into the main card as a dedicated on-demand Easter-egg pack (`X01~X05`) + main-story encounter hooks (e.g., visiting the rooftop terrace). Always pair multi-character Easter eggs with a **`✦ 彩蛋阵容` switch (`solo` 仅保留核心彩蛋女主精简推荐 vs `full` 全彩蛋女配同台)**:
     - Default to `solo` (`[[<NS>_XCAST:solo]]`), physically gating extra Easter-egg supporting profiles behind `secondary_keys: ["[[<NS>_XCAST:full]]"], selective_logic: 3` (`0` extra tokens in `solo` so the LLM never gets crowded) and mounting sparse `sf`/`p3f`/`pef`/`pmf` patches only when `full` is selected.
     - **Anti-Pink-Elephant Rule**: In `solo` prompts and `solo` `<ns_scene>` entries, **never** list the excluded extra characters' names in negative disclaimers (`不额外自动刷出其他无关彩蛋配角`, NEVER `严禁出现琪琪、程欣然`).
5. **Cross-Scene Companion Persistence & Non-Destructive Node Advance (`跨幕随行防丢人双保险系统`)**:
   - **Why Characters Previously Disappeared on Scene Switch**: Static `<ns_scene>` rosters at `depth: 2` read like closed whitelists, `makeAdvancePrompt` pasted static `[<NS>_STATUS]` blocks that wiped live progress, and selective `<character_profile>` entries (`scan_depth: 4`) turned off after 2 turns without the character's name.
   - **4-Part Solution (`companions` Keep-Alive + Dual-Source Auto-Detection + Venue Bridges + Open `<ns_scene>`)**:
     1. **Persistent `companions:` Slot in `[<NS>_STATUS]`**: Defaults to `无` (`0` extra tokens, zero premature spoilers before encounter); once an Easter-egg heroine, tamed supporting girl, or player-introduced character enters play, her name stays in `companions:` every turn within `scan_depth: 4`, permanently keeping her worldbook profile active across scene jumps.
     2. **Dual-Source Auto-Detection in `status_template.html`**: Combines `getField("companions")` + `ctx.chatMetadata` persistence + **scanning `ctx.chat` visible prose** (stripping `<div style="display:none">` trigger blocks and `[<NS>_STATUS]` blocks first) so even if the LLM forgot `companions:` on one turn, clicking `下一幕 ▶` or `填入跳转 ▶` still 100% catches introduced cross-scene heroines, illuminates a top-bar `✦ 同行：...` chip, and exposes an editable `👥 跨幕随行同伴` bar + quick-add pills inside `调度 ▾`.
     3. **Clean Non-Overwriting `makeAdvancePrompt` + Multi-Venue Co-Presence Bridges (`getVenueBridge`)**: Never paste a literal static `[<NS>_STATUS]...[/<NS>_STATUS]` block inside the user's advance prompt (which duplicates the status bar in the user bubble and resets live state); instead pass target stage/step parameters in plain text plus a high-priority `【跨幕同行强制保留】` block with **multi-venue diegetic excuses** (concatenating bridges for all matched venues in `A → B` transition nodes so companions follow on-site everywhere naturally).
     4. **Open-Ended `<ns_scene>` Framing**: Label node character lists as **baseline cast (`本节基准人物`)**, ban closed-whitelist phrasing (`"仅聚焦于这四位角色"` / `"严格依节点设定决定是否登场"`), and instruct the LLM to merge everyone in `companions:` on-site into the new node's events.
6. **3-Way Mutually Exclusive Stage-Decoupled Worldbook (`scan_depth: 4`)**:
   - Every node's `<ns_scene>` is generated in **3 mutually exclusive variants** (`keep/female` with `AND_ANY=0`, `free+erase` with `AND_ALL=3`, `male` with `AND_ANY=0`), guaranteeing **exact 1-of-3 scene activation** across all mode & `sandbox/cosub` combinations, and preserving `selectiveLogic` in standalone worldbook JSON.
7. **CoT-Safe Status Bar & History Stripper Regexes (`<think>` / `<think_nya>` Zero Collision + CJK Stage Support)**:
   - Negative lookaheads prevent `<think>`, `<thinking>`, `<thought>`, `<think_nya>`, or `<report_nya>` drafts from swallowing closing CoT tags or `<game>` prose; stage patterns explicitly support both ASCII and Chinese chapter prefixes (`R1`, `旧1`, `间1`, `一1`, `二1`, `番1`).
8. **18+ Adult Cast Baseline**: All characters (including petite/immortal/bionic characters and player companions) are explicitly anchored as 18+ adult women, scrubbing minor/doll refusal triggers across prose and metadata.

Reference benchmarks in `./角色卡/PIXIV量产卡/更新版v3/`:
- **3rd-Person Epic Fantasy + 摆法 A Co-Sub Benchmark**: `./角色卡/PIXIV量产卡/更新版v3/月见_深渊魔帝艾拉蒂雅/` (`v3.6`: 70 Nodes / 210 Sub-Openings across 6 Volumes, Dark-Gold Obsidian UI, **摆法 A `sandbox | cosub` 女伴同受调教**, 6-Volume Nested-Regex Packs, Yuri Construct Preservation, CJK Stage Codes)
- **1st-Person Modern Sci-Fi + Multi-Route Male + Custom Heroine + Easter-Egg Benchmark**: `./角色卡/PIXIV量产卡/更新版v3/Syolily_我居然被机器人保姆调教了/` (`v3.9`: 24 Main Nodes + 5 Stitched Alien-Envoy Easter-Egg Nodes `X01~X05` = 99 Sub-Openings, `segs_first` Base + Sparse Patches, **男主 5 路线 (`supreme/reversal/protector/insider/custom_male`) + 女主自定义姓名人设 (`canon/custom`) + `companions` 跨幕随行双保险防丢人**, `xcast: solo | full` 仅希蓉精简/全彩蛋双阵容防抢戏切换, 6-Act Nested Regex)
- **3rd-Person Multi-World Trials + Zero-TSF Pure Female Heroine + Full v3.9.1 Matrix**: `./角色卡/PIXIV量产卡/更新版v3/闲读_无限之淫神的诅咒/` (`v3.3`: 119 Nodes / 357 ~1000-char 3-Paragraph Sub-Openings across 6 Worlds, Amethyst-Abyss UI, `sandbox | cosub`, 5 Male Routes, `canon | custom` Heroine Override, 6-Pack Nested Regex)

---

## Workflow Overview

```
Pixiv Novel Sources (audit ALL TXT chapters, POV, cast, fetish structure & aesthetic tone)
  → ① Strategy, Versioning & Cast Audit: archive prior version (`历史版本/v<old>/`); align on card-specific strategy (e.g., whether to include 摆法 A `cosub`, cross-book Easter eggs, cast consolidation, bespoke UI theme)
  → ② Compile 100% Full-Text `源码/nodes_v3.json` (canonical `segs` or `segs_first` + sparse `"si:pi"` patches + zero-leak `meta_erase` & 100%-on-site `meta_male`)
  → ③ Build Bespoke Draggable/Resizable Templates (`panel_template.html` & `status_template.html` with `companions` dual-source detection, multi-venue bridges, clean non-overwriting `makeAdvancePrompt` & cross-iframe safe `#send_textarea` setter)
  → ④ Assemble Card, Open-Cast 3-Way Worldbook & Nested-Regex Pipeline via `源码/build_v3.py` (`$$` escaping before `$1`/`$2`, CoT-safe CJK/ASCII status regexes)
  → ⑤ Run Mandatory Python + Node VM Verification Suite (archive check, PNG parity, WB activation + companion keep-alive/non-intrusion matrix, `<think_nya>` CoT collision, full sub-opening DOM sweep + companion advance carry-over)
  → ⑥ (When requested) Deploy latest version to the SillyTavern server, prune superseded older versions from server (`characters/`, `worlds/`, `thumbnails/avatar/`, stale `character_allowed_regex`), create chat dir & whitelist latest regexes
```

---

## Step ①: Card-Specific Strategy, Archiving & Supporting Cast Audit

1. **Non-Destructive Archiving First**:
   - Before modifying `源码/` or generating new outputs, archive existing root artifacts (`*.png`, `*.json`, `剧情节点大纲_*.md`, `说明.md`, and a snapshot of `源码/`) into `历史版本/v<旧版本号>/`. Only overwrite in place if the user explicitly requests keeping the exact filename for existing chat bindings.
2. **Card-Specific Design Alignment**:
   - Choose canonical base POV according to the source text: **3rd-person `segs`** (`s` + `pe`/`pm`/`pf`) for 3rd-person novels; **1st-person `segs_first`** (`s` + `p3`/`pe`/`pm`/`pf2`) for 1st-person novels.
   - Decide whether **"摆法 A (`stance: sandbox | cosub`)"** fits the novel (ideal when the protagonist is a female character undergoing taming/trials and players may enjoy side-by-side female co-submission).
   - Audit canon yuri / magic-construct chapters (e.g., female angels or demonesses manifesting constructs via magic): whitelist those specific node codes (`YURI_CONSTRUCT_NODES`) so `erase` mode preserves canon yuri construct play while stripping masculine labels (`雄性`, `他`).
3. **Supporting Cast & Antagonist Slot Rules**:
   - Proactively inspect supporting characters; prioritize canon attractive 18+ female characters or arcane/automated mechanisms in `erase` mode, and ask the user via `ask_question` when consolidating ambiguous minor roles.
   - If a standalone antagonist slot in `erase` mode requires a female counterpart name, choose a **completely distinct feminine name** with **zero substring overlap** with the banned male name (avoiding false-positive audit hits and `.replace()` suffix doubling like `加扎克雅雅`).

---

## Step ②: Single-Base Sparse Patches & Zero-Residue Metadata (`nodes_v3.json`)

1. **100% Paragraph Coverage & Sparse `"si:pi"` Patches**:
   - Verify 100% source paragraph coverage; slice each chapter into `3–5` natural sub-openings (`events` length == `segs` length).
   - Store one canonical base array (`segs` or `segs_first`) and build sparse paragraph patches (`pe`, `pm`, `pf` / `p3`, keyed by `"si:pi"` where `bsg.split("\n\n").length === tsg.split("\n\n").length`), asserting lossless reconstruction (`reconstruct(base, patches) == target`).
2. **Complete `meta_erase` & `meta_male` (Including Neighbor Titles & Active Character Blurbs)**:
   - Include `short_title`, `full_title`, `subtitle`, `prev_title`, `next_title`, `location`, `<heroine>_stage`, `<heroine>_equipment`, `<heroine>_mental`, `milestone`, `plot_detail`, `active_characters`, `events`, and `prefs` inside both `meta_erase` and `meta_male`.
   - Run global proper-noun and gender-label replacements across `prev_title`, `next_title`, AND `active_characters` role blurbs (watch out for phrases like `"在场唯一男性"` inside character descriptions!).
   - Programmatically assert **zero** banned male names, male mob labels (`男人`, `男性`, `雄性`, `壮汉`, `混混`, `盗贼`, `卫兵队长`, `教皇`, `干爹`, `父亲`, etc.), standalone male pronouns `他` (protecting neutral compounds `其他/他人/他处/他日/他乡/他国`), and non-yuri male anatomical terms across `meta_erase` + `clean_er_segs`.
3. **Positive Framing in `erase` Worldbook Entries**:
   - **Never** write negative disclaimers containing banned words inside `erase` `<ns_scene>` or `<ns_npc_erase>` entries (e.g., writing `"已清退一切男性角色"` literally injects `"男性"` into the `erase` prompt and fails zero-leak checks!). Always frame positively: `"本局为全女角纯净舞台"`.

---

## Step ③: Open-Cast 3-Way Worldbook, Companion Continuity & "摆法 A" Entries (`build_v3.py`)

1. **Constant & Mode/Stance Entries (`Order 100..119`)**:
   - `Order 100..103` (`constant: True`):
     - Core rules (`depth: 4`): Include an explicit **Cross-Scene Companion Continuity Iron Law (`跨幕同行继承铁律`)** — once any stitched Easter-egg heroine, tamed supporting character, or player-introduced character enters play (`companions`), switching scenes or advancing nodes **never** erases or sidelines them; arrange a plausible venue-matched excuse to bring them on-site into the new scene.
     - Roadmap (`depth: 4`) & sub-step pacing (`depth: 2`, reminding the LLM that `【阶段推进确认】` carries over all `companions` on-site).
     - Status output protocol (`depth: 1`): Include `companions:` (`无` when none introduced; otherwise persistent names & co-presence status across turns), and forbid literal `[<NS>_STATUS]` inside `<think>` / `<thinking>` / `<thought>` / `<think_nya>` / `<report_nya>` blocks.
   - `Order 104..110` (`depth: 2`): `free`, `sandbox`, optional `cosub`, `male` (`100%` on-site), `female`, `keep`, and `erase` (positively framed and welcoming introduced female companions).
   - `Order 111..119` (`depth: 2`): 5 passerby role entries + (when using 摆法 A) 4 female co-sub companion role entries (`maid_peer`, `nun_peer`, `noble_peer`, `soul_peer`).
2. **Enriched 18+ Co-Tamable Female Profiles (`Order 200..215`, `depth: 4`)**:
   - Every female character gets vivid 18+ appearance/attire details + psychological soft spots + explicit `male` co-taming, `cosub`/`erase` dual-taming hooks, and cross-scene co-presence instructions.
   - Gate the male NPC archive strictly with `selective: True, secondary_keys: ["[[<NS>_NPC:keep]]"], selective_logic: 0`.
   - On 0th-floor main-node openings (`N01..`), omit unnecessary Easter-egg heroine names from `<div style="display:none">` unless the node is `X01..` or `customNote` mentions her, preventing false-positive turn-1 profile activation while `[[<NS>_XCAST:solo/full]]` still gates worldbook rules cleanly.
3. **Open-Cast 3-Way Mutually Exclusive `<ns_scene>` Node Entries (`depth: 2`)**:
   - Emit `npc_policy: "male"` (`[[<NS>_NPC:male]]`) in `male` mode while allowing both `free` and `female` modes to toggle `keep | erase`:
     - `Order 300 .. 300+N-1` (`keep` — serves `free+keep`, `cosub+keep`, `female+keep`): `secondary_keys=["[[<NS>_NPC:keep]]"]`, `selective_logic=3` (`AND_ALL`).
     - `Order 300+N .. 300+2N-1` (`erase` — serves `free+erase`, `cosub+erase`, `female+erase`): `secondary_keys=["[[<NS>_NPC:erase]]"]`, `selective_logic=3` (`AND_ALL`), formatted from `meta_erase`.
     - `Order 300+2N .. 300+3N-1` (`male` — serves all 5 `male` routes `supreme / reversal / protector / insider / custom_male`): `secondary_keys=["[[<NS>_VIEW:male]]"]`, `selective_logic=3` (`AND_ALL`), formatted neutrally so non-`supreme` male routes never inherit forced boss assumptions.
   - **Never word `<ns_scene>` as a closed cast whitelist**: Title the cast section `【本节基准人物档案（若前文或 companions 已有其他女伴入局，须全程随行并入现场，严禁清退）】` (and note `[[<NS>_FEM:custom]]` custom-heroine override on the heroine slot) so the LLM never purges carried-over companions or reverts custom heroine settings upon node advance!
   - Always copy `"selectiveLogic": ext.get("selectiveLogic", 0)` into standalone worldbook JSON entries!

---

## Step ④: Chained Nested-Regex Pipeline, Companion-Aware Status Bar & Cross-Iframe JS Hygiene

1. **Chained Nested-Regex Pipeline (`0 .. A+4`)**:
   - `0`: Entry `[[<NS>_OPENING_PANEL]]` → `[[<NS>_UI:<default_state>]][[<NS>_PACK:<first_arc_id>]]` (`markdownOnly: True`).
   - `1..A`: Per-act/per-volume pack injectors `/\[\[<NS>_PACK:<arc_id>\]\]/g` → `<nspack>{pack_json.replace("$", "$$")}</nspack>` (`markdownOnly: True`).
   - `A+1`: UI shell merger `/\[\[<NS>_UI:([^\]]+)\]\][\s\S]*?<nspack>([\s\S]*?)<\/nspack>/g` (`markdownOnly: True` — **always use `([^\]]+)`, NEVER `([A-Za-z0-9_:]+)`**, because URL-encoded `protag_name` / `protag_bio` tokens contain `%`!).
     - **Critical `$` Escaping Order**: Inject `CATALOG` into `panel_template.html` first, escape **every** `$` → `$$` across the entire shell string, and **only then** replace `__UI_STATE_JSON__` → `"$1"` and `__PACK_JSON_SLOT__` → `$2`!
     - **MANDATORY ```` ```html ... ``` ```` Iframe Codeblock Fence Invariant (`shell_replace_str` & `status_replace_str`)**: Both the UI shell regex (`A+1`) and the status bar regex (`A+3`) `replaceString`s **MUST wrap `<!DOCTYPE html>...</html>` inside a Markdown HTML codeblock fence**: ````"```html\n" + html_str.strip() + "\n```"```` (and `panel_template.html`, `status_template.html`, and `nodes_v3.json` must contain `0` internal ```` ``` ```` sequences). In SillyTavern + `JS-Slash-Runner / TavernHelper` ("渲染前端代码块"), regexes run *before* `showdown` + `DOMPurify`: omitting ```` ```html ... ``` ```` causes `DOMPurify` to treat the HTML as inline chat markup and strip `<script>` and `:root` CSS variables, rendering a blank shell!
   - `A+2`: Hidden trigger block hider (`markdownOnly: True`).
   - `A+3`: **CoT-Safe Status Bar Renderer (`markdownOnly: True, promptOnly: False`)**:
     - Protect `<textarea id="rawStatus" style="display:none">$1</textarea>` with a temporary placeholder token, escape all other `$` → `$$` in `status_html`, restore `$1`, and wrap in ````"```html\n" + status_replace_str.strip() + "\n```"````!
     - Match both ASCII and CJK stage prefixes while forbidding any closing CoT tag inside the match:
       ```python
       cot_guard = r"(?![\s\S]*?<\/(?:think|thinking|thought|think_nya|report_nya)>)"
       stage_pat = r"stage:[ \t]*(?:[A-Z]+|旧|间|一|二|番)\d+"  # tailor prefix set to card's node codes
       find_status_rx = rf"/\[{CARD_NS}_STATUS\]{cot_guard}\s*({stage_pat}[\s\S]*?)\[\/{CARD_NS}_STATUS\]/g"
       ```
   - `A+4`: **CoT-Safe History & Pack-Token Stripper (`markdownOnly: False, promptOnly: True, minDepth: 2`)**:
     - Strip hidden opening trigger blocks, `[[<NS>_UI:...]]`, `[[<NS>_PACK:...]]`, and CoT-guarded `[<NS>_STATUS]...[/<NS>_STATUS]` blocks at `minDepth: 2`.
2. **Companion-Aware `status_template.html` & Clean `makeAdvancePrompt`**:
   - **Dual-Source Companion Detection**: Merge `getField("companions")` + `ctx.chatMetadata` saved companions + **visible `ctx.chat` prose scan** (stripping hidden `<div style="display:none">` trigger blocks and `[<NS>_STATUS]` blocks before checking cross-scene character names like `希蓉`), displaying a top `✦ 同行：...` badge and an editable `👥 跨幕随行同伴` input + quick-add/clear pills inside `调度 ▾`.
   - **Multi-Venue Excuse Concatenation (`getVenueBridge`)**: Build venue-specific diegetic co-presence excuses for every setting type, **concatenating all matched venue segments** on multi-location nodes (`车库 → 教学楼`) instead of returning early on the first match.
   - **No Literal `[<NS>_STATUS]` in `makeAdvancePrompt`**: Never append a pre-filled `[<NS>_STATUS]...[/<NS>_STATUS]` block to the user's advance message; pass `companions:` + worldbook trigger keys + plain-text target status fields so user bubbles don't render duplicate status bars or overwrite live state.
3. **Cross-Iframe `#send_textarea` Setter & `<script>` Hygiene**:
   - Never put literal `<user>`, `{{user}}`, `[<NS>_STATUS]`, `[/<NS>_STATUS]`, `&amp;`, `&lt;`, `&gt;`, or `&quot;` inside `<script>`.
   - In `status_template.html`'s `fillChatInput`, `#send_textarea` lives in `window.parent.document` (cross-iframe). Always resolve `window.parent.HTMLTextAreaElement.prototype` first and null-check the property descriptor before calling `.set`:
     ```js
     const proto = (window.parent && window.parent.HTMLTextAreaElement && window.parent.HTMLTextAreaElement.prototype)
       || (window.HTMLTextAreaElement && window.HTMLTextAreaElement.prototype);
     const desc = proto ? Object.getOwnPropertyDescriptor(proto, "value") : null;
     if (desc && typeof desc.set === "function") desc.set.call(el, text);
     else el.value = text;
     ```

---

## Step ⑤: Mandatory Verification Suite & SillyTavern Server Deployment

1. **Automated Python + Node VM Verification (`verify_<card>.py`)**:
   - Assert `历史版本/` archives exist and PNG `chara` / `ccv3` chunks byte-match the JSON card.
   - **Assert Mandatory ```` ```html ... ``` ```` Iframe Fences**: Assert that both the UI shell regex (`A+1`) and status bar regex (`A+3`) `replaceString`s start with ````"```html\n<!DOCTYPE html>"```` and end with ````"</html>\n```"```` (and strip those fences inside the Node VM runner right before `extractScript(...)` so VM DOM simulation tests the exact inner iframe document).
   - Simulate World Info activation across all `N` nodes × all mode/stance/xcast combinations, verifying **exact 1-of-3** (or exact `1-of-6` Easter-egg) `<ns_scene>` activation, **0** banned male tokens across all active entries in `erase`, **0** premature Easter-egg profile activation when `companions: 无` on main nodes, and **100%** Easter-egg profile keep-alive across all nodes when `companions` contains the introduced heroine.
   - Test genuine JS `RegExp` execution on `<think_nya>...[STATUS] draft...</think_nya><game>prose</game>[STATUS]...companions:...` across both ASCII and CJK stage codes.
   - Run all volume packs through the chained nested regexes inside Node `vm` across sub-openings + `status_template.html` (`调度 ▾`, auto-detection of companions from visible `ctx.chat` prose even when omitted in `rawStatus`, multi-venue `makeAdvancePrompt` bridges, and absence of literal `[<NS>_STATUS]` inside user advance prompts).
2. **SillyTavern Server Deployment (Server Latest-Only Cleanup & `settings.json` Schema Rule)**:
   - Upload the new `<CardName>.png` to `data/default-user/characters/` and `<WorldbookName>.json` to `data/default-user/worlds/`, verify both exist and are non-empty, create `chats/<CardName>`, and remove stale `thumbnails/avatar/<CardName>.png`.
   - **Server Latest-Only Rule (`服务器只保留最新版`)**: Once the new version is verified on the server, remove any superseded older versions of the same work (`_v<old>.png`, `v1.x.png`, unversioned old PNGs, and old worldbook `.json` files) from `characters/`, `worlds/`, and `thumbnails/avatar/` (and remove empty old chat dirs if unused), while keeping local `历史版本/v<old>/` archives intact on Mac.
   - **Schema Warning**: In SillyTavern's `settings.json`, `s["extension_settings"]["regex"]` is a **`list`** of global regex scripts (NOT a `dict`!). Never call `.setdefault()` on `extension_settings["regex"]`; always whitelist character regexes by appending both `"<CardName>.png"` and `"<CardName>"` to `s["extension_settings"].setdefault("character_allowed_regex", [])` while pruning deleted old-version entries from that list.
