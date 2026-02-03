# DedsiQRCode 二维码

## Rule

使用 DedsiQRCode 组件生成二维码。

## Impact

LOW

## Problem

不使用 QRCode 组件会导致二维码生成功能实现复杂。

## Solution

### Props

| 属性 | 类型 | 默认值 | 必需 | 说明 |
|------|------|--------|------|------|
| value | string | 是 | - | 二维码内容 |
| size | number | 160 | 否 | 二维码尺寸（px） |
| color | string | '#000000' | 否 | 前景色 |
| backgroundColor | string | '#ffffff' | 否 | 背景色 |
| margin | number | 2 | 否 | 边距 |
| errorCorrectionLevel | 'L' \| 'M' \| 'Q' \| 'H' | 'M' | 否 | 容错级别 |

### Slots

| 插槽名 | 说明 |
|--------|------|
| loading | 加载状态内容 |
| error | 错误状态内容 |

### 使用示例

```vue
<!-- 基础用法 -->
<dedsi-qrcode value="https://example.com" />

<!-- 自定义样式 -->
<dedsi-qrcode
  value="https://example.com"
  :size="200"
  color="#1890ff"
  backgroundColor="#f0f0f0"
  errorCorrectionLevel="H"
/>

<!-- 不同尺寸 -->
<dedsi-space>
  <dedsi-qrcode value="https://example.com" :size="100" />
  <dedsi-qrcode value="https://example.com" :size="150" />
  <dedsi-qrcode value="https://example.com" :size="200" />
</dedsi-space>

<!-- 不同容错级别 -->
<dedsi-qrcode
  value="https://example.com"
  errorCorrectionLevel="L"
/>
<dedsi-qrcode
  value="https://example.com"
  errorCorrectionLevel="M"
/>
<dedsi-qrcode
  value="https://example.com"
  errorCorrectionLevel="Q"
/>
<dedsi-qrcode
  value="https://example.com"
  errorCorrectionLevel="H"
/>
```

### 容错级别说明

| 级别 | 说明 | 纠错能力 |
|------|------|----------|
| L | 低 | 7% |
| M | 中 | 15% |
| Q | 高 | 25% |
| H | 极高 | 30% |

### 类型定义

```typescript
interface Props {
  value: string
  size?: number
  color?: string
  backgroundColor?: string
  margin?: number
  errorCorrectionLevel?: 'L' | 'M' | 'Q' | 'H'
}
```

## Related

- [组件命名规范](./component-naming.md)
- [DedsiImage 图片](./component-image.md)
