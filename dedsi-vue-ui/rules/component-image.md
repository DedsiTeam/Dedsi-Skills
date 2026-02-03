# DedsiImage 图片

## Rule

使用 DedsiImage 组件展示图片，支持缩放、旋转、多图切换。

## Impact

LOW

## Problem

不使用 Image 组件会导致图片预览功能实现复杂。

## Solution

### Props

| 属性 | 类型 | 默认值 | 必需 | 说明 |
|------|------|--------|------|------|
| src | string | 是 | - | 图片地址 |
| alt | string | - | 否 | 替代文本 |
| width | string \| number | '100%' | 否 | 图片宽度 |
| height | string \| number | 'auto' | 否 | 图片高度 |
| fit | 'fill' \| 'contain' \| 'cover' \| 'none' \| 'scale-down' | 'cover' | 否 | 图片填充模式 |
| preview | boolean | true | 否 | 是否可预览 |
| previewSrcList | string[] | [] | 否 | 预览图片列表 |

### 使用示例

```vue
<!-- 基础用法 -->
<dedsi-image
  src="https://example.com/image.jpg"
  width="200px"
  height="200px"
/>

<!-- 多图预览 -->
<dedsi-image
  :src="currentImage"
  :previewSrcList="imageList"
  fit="cover"
/>

<script setup lang="ts">
import { ref } from 'vue'

const currentImage = ref('https://example.com/image1.jpg')
const imageList = ref([
  'https://example.com/image1.jpg',
  'https://example.com/image2.jpg',
  'https://example.com/image3.jpg'
])
</script>

<!-- 不同填充模式 -->
<dedsi-space>
  <dedsi-image src="image.jpg" width="100px" height="100px" fit="fill" />
  <dedsi-image src="image.jpg" width="100px" height="100px" fit="contain" />
  <dedsi-image src="image.jpg" width="100px" height="100px" fit="cover" />
</dedsi-space>

<!-- 禁用预览 -->
<dedsi-image src="image.jpg" :preview="false" />

<!-- 自定义宽高 -->
<dedsi-image
  src="image.jpg"
  width="300px"
  height="200px"
/>
```

### 填充模式说明

| 模式 | 说明 |
|------|------|
| fill | 拉伸填充，可能变形 |
| contain | 等比缩放，完整显示 |
| cover | 等比缩放，填满容器，可能裁剪 |
| none | 原始大小 |
| scale-down | 同 contain 或 none，取较小尺寸 |

### 类型定义

```typescript
interface Props {
  src: string
  alt?: string
  width?: string | number
  height?: string | number
  fit?: 'fill' | 'contain' | 'cover' | 'none' | 'scale-down'
  preview?: boolean
  previewSrcList?: string[]
}
```

## Related

- [组件命名规范](./component-naming.md)
- [DedsiQRCode 二维码](./component-qrcode.md)
