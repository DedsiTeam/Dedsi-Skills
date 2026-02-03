---
name: dedsi-vue-ui
displayName: Dedsi Vue UI 组件库
description: Dedsi-Vue-UI 是一个基于 Vue 3 + TypeScript 的现代化 UI 组件库，提供了 37+ 个高质量组件，涵盖基础、布局、导航、数据展示和反馈等场景。
version: 0.0.9
category: vue-components
tags:
  - vue3
  - typescript
  - ui-components
  - component-library
author: Dedsi
repository: https://github.com/Dedsi-Vue-UI
license: MIT
framework: vue@3.5.26
language: typescript@5.9.3

stats:
  totalComponents: 60
  implementedComponents: 37
  unimplementedComponents: 23
  categories: 6
---

# Dedsi-Vue-UI 组件库 SKILL 文档

帮助 AI 理解和使用 Dedsi-Vue-UI 组件库的详细指南。

## 何时使用

- 使用 Dedsi Vue UI 组件库开发 Vue 3 + TypeScript 项目
- 需要查询组件的 Props、Events、Slots 和使用方法
- 解决组件使用中的常见问题
- 遵循 Dedsi UI 组件的开发和使用规范

---

## 能力规则

没有这些规则，AI 无法解决问题或生成的代码无法正确运行。

### 核心规范

| 规则 | 影响 | 描述 |
|------|------|------|
| [component-naming.md](rules/component-naming.md) | HIGH | 组件命名规范和使用格式 |
| [component-table.md](rules/component-table.md) | HIGH | 表格组件的完整使用指南 |
| [component-menu.md](rules/component-menu.md) | HIGH | 导航菜单的使用方法 |

### 基础组件

| 规则 | 影响 | 描述 |
|------|------|------|
| [component-alert.md](rules/component-alert.md) | MEDIUM | 警告提示组件 |
| [component-avatar.md](rules/component-avatar.md) | MEDIUM | 头像组件 |
| [component-badge.md](rules/component-badge.md) | MEDIUM | 徽标数组件 |
| [component-breadcrumb.md](rules/component-breadcrumb.md) | MEDIUM | 面包屑导航 |
| [component-card.md](rules/component-card.md) | MEDIUM | 卡片容器 |
| [component-divider.md](rules/component-divider.md) | MEDIUM | 分割线 |
| [component-empty.md](rules/component-empty.md) | MEDIUM | 空状态占位 |
| [component-tag.md](rules/component-tag.md) | MEDIUM | 标签组件 |
| [component-space.md](rules/component-space.md) | MEDIUM | 间距布局 |
| [component-split.md](rules/component-split.md) | MEDIUM | 分割布局 |
| [component-typography.md](rules/component-typography.md) | MEDIUM | 排版组件 |

### 布局组件

| 规则 | 影响 | 描述 |
|------|------|------|
| [component-grid.md](rules/component-grid.md) | MEDIUM | 栅格布局系统 |
| [component-scrollbar.md](rules/component-scrollbar.md) | MEDIUM | 自定义滚动条 |
| [component-skeleton.md](rules/component-skeleton.md) | MEDIUM | 骨架屏 |

### 导航组件

| 规则 | 影响 | 描述 |
|------|------|------|
| [component-menu.md](rules/component-menu.md) | HIGH | 导航菜单 |
| [component-tabs.md](rules/component-tabs.md) | MEDIUM | 标签页切换 |
| [component-dropdown.md](rules/component-dropdown.md) | MEDIUM | 下拉菜单 |

### 数据展示组件

| 规则 | 影响 | 描述 |
|------|------|------|
| [component-table.md](rules/component-table.md) | HIGH | 数据表格 |
| [component-statistic.md](rules/component-statistic.md) | MEDIUM | 统计数值 |
| [component-number-converter.md](rules/component-number-converter.md) | MEDIUM | 数字转换 |
| [component-countdown.md](rules/component-countdown.md) | MEDIUM | 倒计时 |
| [component-image.md](rules/component-image.md) | MEDIUM | 图片预览 |
| [component-qrcode.md](rules/component-qrcode.md) | MEDIUM | 二维码生成 |

### 反馈组件

| 规则 | 影响 | 描述 |
|------|------|------|
| [component-dialog.md](rules/component-dialog.md) | HIGH | 对话框 |
| [component-modal.md](rules/component-modal.md) | MEDIUM | 模态框 |
| [component-drawer.md](rules/component-drawer.md) | MEDIUM | 抽屉 |
| [component-message.md](rules/component-message.md) | HIGH | 全局提示 |
| [component-popconfirm.md](rules/component-popconfirm.md) | MEDIUM | 气泡确认框 |
| [component-popover.md](rules/component-popover.md) | MEDIUM | 气泡卡片 |
| [component-tooltip.md](rules/component-tooltip.md) | MEDIUM | 文字提示 |
| [component-result.md](rules/component-result.md) | MEDIUM | 结果反馈 |
| [component-popper.md](rules/component-popper.md) | MEDIUM | 基础气泡 |

### 其他组件

| 规则 | 影响 | 描述 |
|------|------|------|
| [component-segmented.md](rules/component-segmented.md) | MEDIUM | 分段器 |
| [component-marquee.md](rules/component-marquee.md) | MEDIUM | 跑马灯 |

---

## 效率规则

这些规则帮助 AI 更有效、更一致地解决问题。

### 最佳实践

| 规则 | 影响 | 描述 |
|------|------|------|
| [best-practices.md](rules/best-practices.md) | HIGH | 组件使用最佳实践 |

---

## 安装使用

```bash
npm install dedsi-vue-ui
```

```typescript
import { createApp } from 'vue'
import { DedsiAlert, DedsiButton, DedsiTable } from 'dedsi-vue-ui'

const app = createApp(App)
app.use(DedsiAlert)
app.use(DedsiButton)
app.use(DedsiTable)
```

---

## 组件统计

### 已实现组件 (37个)

**基础组件 (10个)**:
- DedsiAlert, DedsiAvatar, DedsiBadge, DedsiBreadcrumb, DedsiCard
- DedsiDivider, DedsiEmpty, DedsiTag, DedsiSpace, DedsiSplit

**布局组件 (3个)**:
- DedsiRow/DedsiCol, DedsiScrollbar, DedsiSkeleton

**导航组件 (3个)**:
- DedsiMenu, DedsiTabs, DedsiDropdown

**数据展示组件 (7个)**:
- DedsiTable, DedsiStatistic, DedsiNumberConverter, DedsiCountdown
- DedsiImage, DedsiQRCode, DedsiTypography

**反馈组件 (9个)**:
- DedsiDialog, DedsiModal, DedsiDrawer, DedsiMessage
- DedsiPopconfirm, DedsiPopover, DedsiTooltip, DedsiResult, DedsiPopper

**其他组件 (5个)**:
- DedsiSegmented, DedsiMarquee, DedsiCountdown, DedsiEmpty, DedsiResult

### 未实现组件 (23个)

**表单组件**:
- DedsiButton, DedsiForm/DedsiFormItem, DedsiInput/DedsiInputNumber/DedsiInputPassword
- DedsiTextarea, DedsiCheckbox/DedsiCheckboxGroup, DedsiRadio/DedsiRadioGroup
- DedsiSelect, DedsiSwitch, DedsiSlider, DedsiRate

**数据选择组件**:
- DedsiAutoComplete, DedsiCascader, DedsiDatePicker/DedsiMonthPicker/DedsiRangePicker
- DedsiTimePicker/DedsiTimeRangePicker, DedsiTreeSelect, DedsiTransfer, DedsiMentions

**其他**:
- DedsiUpload

---

## 核心特性

- **Vue 3 Composition API**: 充分利用 Vue 3 的新特性
- **TypeScript 支持**: 完整的类型定义和类型推断
- **组件化设计**: 高复用性和可组合性
- **响应式布局**: 支持多种屏幕尺寸
- **主题定制**: 支持自定义主题和样式

---

## 组件命名规则

- **单文件组件**: `Dedsi` + 功能名称（如 `DedsiAlert`、`DedsiButton`）
- **组合组件**: 主组件 + 子组件（如 `DedsiMenu` + `DedsiMenuItem`）

**重要**: 在模板中使用组件时，必须使用 `kebab-case` 格式的 name 值（如 `dedsi-alert`），而不是 PascalCase（如 `DedsiAlert`）。

---

## 统计信息

| 指标 | 数值 |
|------|------|
| 总组件数 | 60 |
| 已实现 | 37 |
| 未实现 | 23 |
| 组件类别 | 6 |

---

## 技术栈

- **框架**: Vue 3.5.26 (Composition API)
- **语言**: TypeScript 5.9.3
- **构建工具**: Vite
- **图标**: @ant-design/icons-vue
- **日期处理**: dayjs

---

## 参考

- [Vue 3 Documentation](https://vuejs.org/) - Vue 3 官方文档
- [TypeScript Documentation](https://www.typescriptlang.org/) - TypeScript 官方文档
