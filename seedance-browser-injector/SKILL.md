---
name: seedance-browser-injector
description: >
  Seedance 2.0 Web Panel Automation. Combines awesome-seedance prompt generation
  with Chrome DevTools MCP browser injection. Trigger when user wants to generate
  Seedance video prompts from a script, split into 15s segments, and inject them
  directly into the Seedance web panel editor with proper chip and asset references.
  Trigger keywords: Seedance panel, 注入提示词, 填入工作面板, 片段注入, 分镜注入,
  browser seedance, chrome seedance injection.
agent_created: true
---

# Seedance Browser Injector

Automated workflow: Script → Prompt Generation → Browser Injection into Seedance Web Panel.

## Prerequisites

- Chrome DevTools MCP configured in `reasonix.toml` (see above)
- User has Seedance panel open in Chrome with remote debugging enabled (`--remote-debugging-port=9222`)
- `awesome-seedance` skill available for prompt generation

## Panel Element Reference

This is the canonical reference for the Seedance 2.0 web panel. Always consult this before writing selectors or scripts.

### 1. Video State Detection

| State | HTML Signature | Meaning |
|-------|---------------|---------|
| 🟡 Generating | `div.el-loading-mask` containing `task ID: NNN...` | Video is still being generated |
| 🟢 Done | `<video class="vjs-tech" src="...mp4">` | Video has finished and is playable |

### 2. Episode (集) Switching

| Element | Selector |
|---------|----------|
| Episode selector (closed) | `input[readonly]` value="第X集" (the FIRST of 7 el-select inputs) |
| Dropdown list | `ul.el-select-dropdown__list` |
| Episode items | `li.el-select-dropdown__item` containing `<span>第N集</span>` |
| Currently selected | `li.el-select-dropdown__item.selected` |

**⚠️ The 7 el-select dropdowns share the same popup.** To switch episodes:
1. Click the first `input[readonly]` (value="第X集") to open the correct dropdown
2. Click the target `li.el-select-dropdown__item` by matching `textContent === '第N集'`
3. Page will reload with new `part_no`

### 3. Segment (片段) Switching

| Element | Selector | Notes |
|---------|----------|-------|
| Timeline node | `div.timeline-node` | Each segment is a `.timeline-node` |
| Segment number | `div.card-order` | Contains the **segment number** as text (e.g. "6") — **this is the canonical ID** |
| Segment cover image | `img.img` inside `.card-preview` of the target `.timeline-node` | **Click this to switch** |
| Empty segment | `<img class="empty" src="/images/bubble-empty...">` | No content yet |
| Active segment | `div.video-card.item.active` | The currently selected segment has `.active` class |

**⚠️ CRITICAL:** Fragment numbers are the `card-order` text inside each `.timeline-node`, NOT the index of cover images. The DOM is a flat list of `.timeline-node` elements in display order. To switch to segment N:

```javascript
// ✅ CORRECT: Find the .timeline-node whose .card-order text matches N, then click its img
function switchToSegment(n) {
  const nodes = document.querySelectorAll('.timeline-node');
  for (const node of nodes) {
    const orderEl = node.querySelector('.card-order');
    if (orderEl && orderEl.textContent.trim() === String(n)) {
      const img = node.querySelector('img.img');
      if (img) { img.click(); return true; }
    }
  }
  return false;
}
switchToSegment(6); // switches to segment 6
```

**Never** use `document.querySelectorAll('img.img[src*="segment_cover"]')[N]` — the index of an image in the DOM does NOT correspond to the segment number. Always match by `card-order` text.

### 4. Asset Library (资产库)

| Element | Selector |
|---------|----------|
| Container | `div.resource-wrap` |
| Category tabs | `div.capsule-btn` (角色 / 场景 / 道具 / 加白资源) |
| Active category | `div.capsule-btn.active` |
| Search box | `input[placeholder="资源名模糊查询"]` |
| Asset card | `div.item` — contains `img` (thumbnail) + `span.name-text` (name) |
| Apply to head frame | `button` containing text "应用到首帧" |
| Apply to tail frame | `button` containing text "应用到尾帧" |

### 5. Prompt Editor

| Element | Selector |
|---------|----------|
| Editor container | `[contenteditable="true"]` |
| Mention chip | `span.mention-chip` |
| Role chip | `data-source-kind="role"` |
| Scene chip | `data-source-kind="scene"` |
| Prop chip | `data-source-kind="prop"` |
| Audio chip | `data-source-kind="audio"` |

**Chip data attributes to extract:**
- `data-id` — e.g. `"role:9099"` (REQUIRED — never use placeholder "0")
- `data-url` — asset image URL
- `data-asset-id` — platform asset ID (e.g. `"asset-20260525..."`; may be empty for some assets)
- `data-source-kind` — `"role"` / `"scene"` / `"prop"` / `"audio"`

---

## Workflow

### Phase 1: Connect to Seedance Panel

1. Load `awesome-seedance` skill for prompt generation knowledge
2. Use `mcp__chrome-devtools__list_pages` to verify the Seedance panel URL is active
3. Use `mcp__chrome-devtools__take_snapshot` to read current panel state (which segment is selected, what's in the editor)

### Phase 2: Extract Asset Data

Before injecting any prompt, extract chip data from the panel:

1. Read `references/chip-format.md` for the correct chip HTML structure
2. If the editor already contains native chips (user-created via `@`), use `evaluate_script` to read their `data-id`, `data-url`, and `data-asset-id`
3. For new assets not yet in the editor, ask the user to `@` them natively first, then extract the data
4. Store all chip data as key-value pairs: `{name: {type, id, url, assetId}}`

**CRITICAL RULE:** Never inject chips with placeholder IDs like `data-id="role:0"`. This causes `invalid asset uri` errors. Only use verified real asset IDs extracted from the platform.

### Phase 3: Generate Prompt from Script

#### 3.1 Script Fidelity Rules

- **Every line of original dialogue MUST appear exactly once** — no deletion, no repetition, no invention
- Direction descriptions (△ 动作) are mandatory scene beats, not optional
- Embellishments (reaction shots, physical details like "烟掉地上") are acceptable only if they don't replace or delete script content
- Split long dialogue lines cleanly at natural pause points without overlap

#### 3.2 Shot Distribution Principles

For 15-second short drama segments:

| Rule | Guideline |
|------|-----------|
| Shots per segment | **5-6 shots** (not 7-8). Fewer shots = slower, more natural pace |
| Lead character A | ~40% of screen time |
| Lead character B | ~40% of screen time |
| Supporting characters | **1 shot per segment max** — they are background, not co-stars |
| Average shot length | **2.5-3.5 seconds** for dialogue shots |
| Action shots | 1-2 seconds for physical beats (slaps, pushes, gestures) |

**Anti-pattern:** Ping-pong cutting between two leads for 8 rapid shots. The audience needs breathing room.

**Correct pattern:** A speaks (3s) → B reacts + speaks (4s) → A reacts (2s) → B acts (3s) → supporting glance (1s) → A climax (2s).

#### 3.3 Complete Technical Template

Every segment MUST include ALL of these tags. Never omit any:

```
【风格】[genre specific]
【画幅】竖屏 9:16
【镜头】ARRI MINI拍摄，中焦段 [mm]，[aperture]，电影写实人像
【环境】[scene chip]
【布光】[lighting plan]
【光影】侧顺光为主，环境反射补亮暗部，保留面部自然阴影层次
【色调】[color temp + contrast]
【特效】背景轻微虚化散景，轻微胶片颗粒感，保留皮肤细节与毛孔质感
【运镜】手持拍摄，自然呼吸感运镜，[scene-specific movement]
【构图】非对称构图，禁止越轴，镜头语言符合现实逻辑
【表演】动作自然流畅，加强眉眼鼻表演，每个镜头人物必须有眨眼动作，不生成多个相同角色
【人物】[character chips]
```

**Negative prompt essentials** (must always be present):
- `禁止越轴`
- `不生成多个相同角色`
- `每个镜头人物必须有眨眼动作`
- `全片不生成任何文字` (unless script specifies subtitles)

#### 3.4 Prompt Format

1. Use awesome-seedance's Format B — time-axis format for 15s segments
2. Structure as shot-by-shot timeline
3. Include tail frame description for continuity with next segment
4. The first shot's visual state MUST match the previous segment's tail frame
5. Default technical params: 竖屏 9:16, 50mm f/2.8, 720p, 写实电影人像

#### 3.5 Pre-Injection Review Protocol

Before injecting, present the shot breakdown in a table and ask user to confirm:
- Dialogue completeness
- Shot count and pacing
- Character screen time balance
- Tail-to-head continuity

**Do NOT inject until user confirms.**

### Phase 4: Inject Into Panel

1. Navigate to the correct segment using `switchToSegment(n)` (see Panel Element Reference §3)
2. Verify the segment is selected and editor is clear/focused
3. Build prompt HTML using the native chip HTML extracted in Phase 2
4. Use `evaluate_script` to set `editor.innerHTML` and dispatch `input`/`change` events
5. Take a snapshot to verify all content rendered correctly

**Injection format rules (EP8 实战验证):**

- **Line breaks:** Use `<br>` NOT `<p>` tags. `editor.innerHTML = lines.join('<br>')` — `<p>` causes extra spacing and content gets merged into gibberish when the panel re-parses.
- **Redundant lines:** DO NOT inject `【画幅】竖屏 9:16` or standalone `15秒，9:16竖屏` — the panel's own selectors already handle aspect ratio and duration.
- **Chip syntax:** Use `[角色名]` placeholders in the storyboard file (e.g. `[傅临琛-贵族常服]`), replace with native chip HTML during injection.
- **Chip extraction:** The fastest workflow is: user lists needed chips → user `@` mentions them in editor → script extracts all chip HTML via `evaluate_script` → build injection. Do NOT try to reverse-engineer Vue component trees or API calls.

Injection script template:
```javascript
const editor = document.querySelector('[contenteditable="true"]');

// ... define chip HTML variables from extracted data ...

const lines = [
  '写实风格古装正剧，极致画质，高对比度光影，电影运镜',
  '【镜头】ARRI MINI拍摄，中焦段85mm，大光圈f/1.2浅景深，电影写实人像',
  '【环境】' + sceneChip + ' 内部楼梯口，冬日白天',
  // ... more tech tags ...
  '【场景】' + sceneChip,
  '【角色】' + roleChip1 + ' ' + roleChip2 + ' ' + roleChip3,
  '【站位】一句初始站位描述。',
  '',
  '0-3s：【标题】景别——具体画面+动作+对话',
  roleChip1 + audioChip1 + '（情绪）：台词内容。',
  '△ 动作描述。'
];

editor.innerHTML = lines.join('<br>');
editor.dispatchEvent(new Event('input', { bubbles: true }));
editor.dispatchEvent(new Event('change', { bubbles: true }));
```
editor.dispatchEvent(new Event('input', { bubbles: true }));
editor.dispatchEvent(new Event('change', { bubbles: true }));
```

### Phase 5: Post-Injection Reminders

After each injection, remind the user to:
- Set segment duration to 15s (default is often 4s)
- Verify chip rendering visually in browser (image chips vs plain text)
- Remind user: inject only when instructed, one segment at a time

## Troubleshooting

| Error | Cause | Fix |
|-------|-------|-----|
| `invalid asset uri` | Placeholder data-id (e.g. `role:0`) | Extract real ID from native chip first |
| Editor shows plain text chips | Chips not registered in Vue data model | User must `@` the chip natively before injection |
| `content[6].image_url.url` | Specific asset index has bad URI | Check all data-ids are real; test with single chip |
| Injection overwrites native chips | innerHTML replaces all | Extract native chip HTML first, rebuild with it |
| Segment switch triggers delete | Clicked number button instead of cover image | Use `switchToSegment(n)` — matches by `.card-order` text, not image index |
| Same character appears 3+ shots in a row | No reaction shots interspersed | Insert 1-2s cuts to other characters between long monologues |
| Dialogue line cut off mid-sentence | Split at wrong point | Split at natural pauses (。！？) or between distinct ideas |
| Page snapshot too short to see content | Large DOM tree truncated | Use `evaluate_script` to read `[contenteditable].textContent` directly |

## Complete Injection Checklist

**Before Generating Prompt:**
- [ ] All asset data-ids verified (no placeholder "0")
- [ ] Original script open side-by-side for line-by-line verification

**Prompt Quality:**
- [ ] Every line of original dialogue present exactly once (no gaps, no repeats)
- [ ] 5-6 shots per 15s segment (not faster)
- [ ] Lead characters get ~80% combined screen time; supporting cast max 1 shot each
- [ ] All 11 technical tags present: 风格/画幅/镜头/环境/布光/光影/色调/特效/运镜/构图/表演
- [ ] Negative prompts included: 禁止越轴, 不生成多个相同角色, 眨眼动作, 不生成文字
- [ ] Tail frame description matches next segment's head frame

**Before Injection:**
- [ ] Chrome DevTools MCP connected
- [ ] Seedance panel page active
- [ ] Correct segment selected via `switchToSegment(n)` (card-order matching)
- [ ] User confirmed shot breakdown table

**After Injection:**
- [ ] Snapshot verified content rendered
- [ ] Duration set to 15s (default is 4s)
- [ ] Reminded user: next segment only when instructed
