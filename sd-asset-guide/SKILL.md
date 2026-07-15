---
name: sd-asset-guide
version: "1.0.0"
last_updated: "2026-07-15"
author: "Takis/安梓豪"
description: 角色/场景/道具资产构建指引 — 四视图、AI出图提示词。由 awesome-seedance 主 skill 调用。
repository: https://github.com/oijhl852/agent-skill-sync
---

# sd-asset-guide — 资产构建

> 作者：Takis/安梓豪 | 仓库：[agent-skill-sync](https://github.com/oijhl852/agent-skill-sync)

为 Seedance 视频生成所需的角色、场景、道具图片编写 AI 出图提示词。

## 资产编号

| 类别 | 编号 | 示例 |
|------|------|------|
| 角色 | C01-C99 | C01 林冲·正面全身 |
| 场景 | S01-S99 | S01 草料场·雪景 |
| 道具 | P01-P99 | P01 长枪 |

## 角色提示词要求

- 全身正面、纯白背景
- 包含鞋子描述
- 双手自然垂落，表情中性
- 描述：身高/体型/年龄/发型/发色/服装/脸型
- 二八身材比例

## 场景提示词要求

- 无人物
- 每个场景3张：正打/反打/侧面全景
- 风格与视觉主题一致

## 道具提示词要求

- 纯白背景，无人物
- 主体清晰，材质符合剧本设定

## 出图比例

| 资产 | 比例 | 分辨率 |
|------|------|--------|
| 角色全身 | 9:16 | 2K |
| 三/四视图 | 16:9 | 2K |
| 场景 | 16:9 | 2K |
| 道具 | 1:1 | 1K/2K |

## 🔄 更新

仓库：https://github.com/oijhl852/agent-skill-sync
