---
name: sd-read-script
version: "1.0.0"
last_updated: "2026-07-15"
author: "Takis/安梓豪"
description: 解析剧本格式、分场识别、提取角色/场景/台词/动作，建立保真表。由 awesome-seedance 主 skill 调用。
repository: https://github.com/oijhl852/agent-skill-sync
---

# sd-read-script — 剧本读取

> 作者：Takis/安梓豪 | 仓库：[agent-skill-sync](https://github.com/oijhl852/agent-skill-sync)

解析用户输入的剧本/小说文本，提取结构化信息，建立保真表。

## 输入

- 分场剧本（`第X集`、`X-X 日/夜 内/外`、`△动作`、`角色名：台词`）
- 小说/故事文本
- 单场景描述

## 输出

| 信息 | 说明 |
|------|------|
| 角色表 | 角色名、身份、关系 |
| 场景表 | 地点、时间、内/外、关键道具 |
| 台词表 | 每句原文，标注说话者和情绪 |
| 动作表 | 关键剧情动作 |
| OS/VO | 内心独白、画外音 |
| 分场标记 | 集号、场号 |

## 剧本格式识别

### 标准分场格式

```
第X集
X-X [日/夜] [内/外] [场景名称]
道具：[道具列表]
出场人物：[角色列表]

△ [镜头描述]
角色名（情绪）：[对白]
```

### 保真原则

- 每句原文台词保留且仅出现一次
- 不擅自改写核心剧情
- 不无中生有添加台词

## 🔄 更新

仓库：https://github.com/oijhl852/agent-skill-sync
