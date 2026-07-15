---
name: sd-chip
version: "1.0.0"
last_updated: "2026-07-15"
author: "Takis/安梓豪"
description: 资产 chip 管理 — @mention 触发、chip 数据提取、HTML 构建。由 awesome-seedance 主 skill 调用。
repository: https://github.com/oijhl852/agent-skill-sync
---

# sd-chip — 资产 Chip 管理

> 作者：Takis/安梓豪 | 仓库：[agent-skill-sync](https://github.com/oijhl852/agent-skill-sync)

管理 Seedance 面板中资产引用 chip 的创建和数据提取。

## Chip HTML 结构

```html
<span class="mention-chip" contenteditable="false"
  data-id="role:3195"
  data-name="秦牧-基础形象"
  data-label="秦牧-基础形象"
  data-avatar-bg="linear-gradient(135deg, #9077ff, #5965ff)"
  data-url="https://..."
  data-asset-id="asset-20260427-..."
  data-source-kind="role">
  <span class="mention-chip-avatar">
    <img class="mention-chip-avatar-image" src="..." alt="秦牧-基础形象">
  </span>
  <span class="mention-chip-label">秦牧-基础形象</span>
</span>
```

## 关键属性

| 属性 | 说明 | 示例 |
|------|------|------|
| data-id | 资产ID（必填，不可为0） | `role:3195` |
| data-source-kind | 资产类型 | role / scene / prop / audio |
| data-url | 资产图片URL | https://... |
| data-asset-id | 平台资产ID | asset-20260427-... |

## 提取原生 Chip

```javascript
const chips = document.querySelectorAll('.mention-chip');
for (const c of chips) {
  const label = c.querySelector('.mention-chip-label')?.textContent || '';
  const id = c.dataset.id;
  const url = c.dataset.url;
  const assetId = c.dataset.assetId;
  const kind = c.dataset.sourceKind;
}
```

## 构建 Chip HTML

```javascript
const chipHtml = (dataId, sourceId, name, desc, remark, url, assetId, assetType, sourceKind, avatarBg) => {
  const bg = avatarBg || 'linear-gradient(135deg, #9077ff, #5965ff)';
  return `<span class="mention-chip" ...>...</span>`;
};
```

## 手动 @mention

```
1. click 编辑器区域
2. type_text "@角色名"（触发 mention 弹窗）
3. click 目标资产选项
4. chip 自动插入编辑器末尾
```

⚠️ 绝不使用占位符 ID（如 `role:0`），会导致 `invalid asset uri` 错误。

## 🔄 更新

仓库：https://github.com/oijhl852/agent-skill-sync
