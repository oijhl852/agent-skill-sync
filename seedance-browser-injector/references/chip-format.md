# Seedance Panel Chip Format Reference

## Native Chip HTML Structure

When the user types `@` in the Seedance panel editor, the platform creates a mention-chip span:

```html
<span class="mention-chip" contenteditable="false"
  data-id="role:3195"
  data-name="秦牧-基础形象"
  data-role-label=""
  data-desc=""
  data-remark=""
  data-label="秦牧-基础形象"
  data-avatar-bg="linear-gradient(135deg, #9077ff, #5965ff)"
  data-url="https://aigc-2-1320883699.cos.ap-beijing.myqcloud.com/..."
  data-asset-id="asset-20260427162713-47j6w"
  data-mention-type=""
  data-asset-type="image"
  data-source-kind="role">
  <span class="mention-chip-avatar">
    <img class="mention-chip-avatar-image" src="https://..." alt="秦牧-基础形象">
  </span>
  <span class="mention-chip-label">秦牧-基础形象</span>
</span>
```

## CRITICAL: data-id MUST be a real asset ID

- ❌ `data-id="role:0"` or `data-id="audio:0"` → WILL fail with `invalid asset uri`
- ✅ `data-id="role:3195"` → real asset, platform can resolve

## Asset Types

| Type | data-source-kind | data-id format |
|------|-----------------|----------------|
| Character | role | `role:XXXX` |
| Scene | scene | `scene:XXXX` |
| Audio | audio | `audio:XXXX` |

## Audio Chips Quirk

Audio chips use `data-asset-type="image"` (NOT "audio") in the platform's native format, even though `data-source-kind="audio"`. Match the native format exactly.

## How to Extract Chip Data from Panel

Use evaluate_script to read chip HTML from the editor after the user creates native chips:

```javascript
const chips = document.querySelectorAll('.mention-chip');
for (const c of chips) {
  const label = c.querySelector('.mention-chip-label')?.textContent || '';
  // Extract: data-id, data-url, data-asset-id, data-source-kind
}
```

## Key Differences: Native vs Injected

| Attribute | Native (platform @) | Safe Injection |
|-----------|-------------------|----------------|
| data-desc | `""` (empty) | `""` (empty) |
| data-remark | `""` (empty) | `""` (empty) |
| img alt | `alt="name"` | `alt="name"` |
| data-id | Real platform ID | Real platform ID |
