---
name: awesome-seedance
version: "2.1.0"
last_updated: "2026-07-15"
author: "Takis/安梓豪"
description: Seedance 2.0 综合视频分镜技能 — 从剧本到提示词再到面板注入的全流程编排。覆盖剧本读取、15秒拆段、对话/动作分镜、故事改编、资产构建、提示词生成与注入面板。
repository: https://github.com/oijhl852/agent-skill-sync
---

# Awesome Seedance — 主编排层

> 作者：Takis/安梓豪 | 仓库：[agent-skill-sync](https://github.com/oijhl852/agent-skill-sync)

将剧本、台词或故事转成 Seedance 2.0 视频提示词，并可选注入到 Seedance 网页面板。

## 子技能速查

### 📝 内容创作

| 调用 | 职责 | 触发场景 |
|------|------|---------|
| `sd-read-script` | 解析剧本格式、提取角色/场景/台词/动作 | 用户提供分场剧本、小说文本 |
| `sd-segment-split` | 按戏剧节拍拆15秒段、反模式检查 | 已有剧本需分段时 |
| `sd-dialogue` | 对话戏分镜（铁律0-3、镜头分配） | 以对白为主的场景 |
| `sd-action` | 动作/打斗/蒙太奇分镜 | 以动作、战斗为主的场景 |
| `sd-story-adapt` | 故事→剧本改编（三幕式、短剧公式） | 用户说"帮我写个剧本" |
| `sd-asset-guide` | 人物/场景/道具四视图出图提示词 | 需要生成素材图时 |

### 🎯 提示词

| 调用 | 职责 | 触发场景 |
|------|------|---------|
| `sd-prompt` | 组装11标签/时间轴提示词 | 分镜完成后产出最终提示词 |
| `sd-prompt-library` | 精选案例库（按风格分类带视频效果） | 用户想参考别人怎么写的 |
| `sd-community` | 4776条社区提示词索引 | 需要大量灵感参考 |

### ✅ 质量

| 调用 | 职责 |
|------|------|
| `sd-quality` | 保真/密度/约束/衔接检查 |

### 🚀 面板注入

| 调用 | 职责 | 触发场景 |
|------|------|---------|
| `sd-panel` | Chrome 连接、面板元素定位、工具可用性检测 | "注入到 Seedance 面板" |
| `sd-chip` | @mention 触发、chip 数据提取、HTML 构建 | 需要引用资产时 |
| `sd-inject` | 凭证提取、segment/list、segment/update API | 注入提示词到面板 |

## 执行流程

```
输入（剧本/台词/故事/场景描述）
  │
  ├─ 分场剧本/台词 ──→ sd-read-script ──→ sd-segment-split ──→ sd-dialogue|sd-action ──→ sd-prompt
  ├─ 单个场景/镜头 ──→ sd-prompt
  ├─ 故事/大纲 ──────→ sd-read-script ──→ sd-story-adapt ──→ sd-segment-split ──→ ...
  ├─ 已有提示词优化 ──→ sd-quality
  └─ 注入面板 ────────→ sd-panel → sd-chip → sd-inject
```

**默认参数**：时长15秒 · 画幅9:16 · 风格电影写实 · 无字幕/无BGM

## 🔄 版本与更新

**仓库地址**：`https://github.com/oijhl852/agent-skill-sync`

**自动检查规则**：每次本 skill 被调用时，agent 应：
1. `web_fetch` 读取本文件的 GitHub raw 版 frontmatter
2. 对比 `version` 和 `last_updated` 字段
3. 如果仓库版更新 → 提醒用户拉取；如果本地版更新 → 提醒用户推送

**修改后推送规则**：对本 skill 做了优化或新增内容后：
1. 递增 `version` 补丁号，更新 `last_updated` 为当天日期
2. `git add` → `git commit -m "vX.Y.Z: 改动摘要"` → `git push`
