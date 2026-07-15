---
name: sd-action
version: "1.0.0"
last_updated: "2026-07-15"
author: "Takis/安梓豪"
description: 动作/打斗/蒙太奇分镜 — 连续动作链写法。由 awesome-seedance 主 skill 调用。
repository: https://github.com/oijhl852/agent-skill-sync
---

# sd-action — 动作分镜

> 作者：Takis/安梓豪 | 仓库：[agent-skill-sync](https://github.com/oijhl852/agent-skill-sync)

专注动作戏、打斗、蒙太奇的分镜头设计。

## 动作线模板

```
起始状态 → 发力/移动方向 → 接触或变化 → 结果状态 → 下一段承接状态
```

## 写法要点

- 写连续动作链，避免切太碎
- 用 `→` 串联连续动作
- 用速度副词替代情绪词：猛然、瞬间、快速、缓慢
- 关键动作前重复当前身体状态（防止模型改姿势）
- 可润色动作连续性，不可改变剧情结果

## 蒙太奇

- 所有 B-roll 集中在一段
- 快切：6-8个镜头，每镜约2秒
- 方便后期调用素材

## 打斗

- 5-7个镜头，每镜2-3秒
- 关键物理动作给独立镜头

## 🔄 更新

仓库：https://github.com/oijhl852/agent-skill-sync
