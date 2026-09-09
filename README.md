# 晨辉星游（CHXYOrderPrice）

一款基于 **Vue 3 + Vite + Vant** 开发的移动端电竞陪玩商城页面，主题为三角洲行动（Delta Force）游戏的物资单 / 小时陪服务。

## 🎮 项目简介

晨辉星游是一个电竞陪玩服务的移动端 H5 页面，提供三角洲游戏的物资代取、地图护航、小时陪玩等服务。页面采用粉色 + 战术深色的混合风格，包含商品浏览、分类筛选、详情查看和扫码下单等完整的移动端电商交互流程。

## ✨ 功能特性

- 🏠 **首页商品列表** — 分类 Tab 切换、关键词搜索、价格区间筛选、2 列商品网格
- 📋 **商品详情页** — 商品介绍、多价位选项、战术风格价目表、底部下单栏
- 📱 **扫码下单弹窗** — 复用式弹窗组件，首页和详情页均通过扫码联系客服下单
- 🎲 **随机来一个** — 点击按钮图标随机切换并跳转到详情页，增加趣味性
- 📐 **移动端适配** — Vant 移动端组件，`overflow-x: hidden` 防止横向溢出

## 🛠 技术栈

| 技术 | 版本 | 说明 |
|------|------|------|
| Vue | ^3.4.31 | 渐进式 JavaScript 框架 |
| Vite | ^5.3.4 | 下一代前端构建工具 |
| Vant | ^4.9.4 | 移动端 UI 组件库 |
| Vue Router | ^4.4.0 | Vue 官方路由（Hash 模式） |

## 📁 项目结构

```
CHXYOrderPrice/
├── index.html                # 入口 HTML
├── package.json              # 依赖配置
├── vite.config.js            # Vite 配置（含 @ 路径别名）
└── src/
    ├── main.js               # 应用入口（注册 Vant、Router）
    ├── App.vue               # 根组件（router-view）
    ├── style.css             # 全局重置样式
    ├── components/
    │   └── QrPopup.vue       # 扫码下单弹窗组件（可复用，v-model 绑定）
    ├── router/
    │   └── index.js          # 路由配置（/ → 首页，/detail → 详情页）
    └── views/
        ├── Home.vue          # 首页（商品列表、筛选、体验单推荐）
        └── Detail.vue        # 商品详情页（价位选项、价目表、下单）
```

## 🚀 快速开始

### 环境要求

- Node.js >= 16
- npm（或 yarn / pnpm）

### 安装依赖

```bash
npm install
```

### 启动开发服务器

```bash
npm run dev
```

启动后访问 `http://localhost:5173/`。

### 构建生产版本

```bash
npm run build
```

构建产物输出到 `dist/` 目录。

### 预览生产版本

```bash
npm run preview
```

## 📄 页面说明

### 首页 `/`

- 顶部栏：品牌标识 + 「下单找我」「随机来一个」按钮
- Banner 区：三角洲战术风格展示
- 体验单推荐卡片
- 分类 Tab：全部 / 物资单 / 小时陪 / 趣味单
- 筛选区：关键词搜索 + 最低/最高价
- 商品网格：2 列布局，展示物资单商品

### 详情页 `/detail`

- 顶部返回按钮
- 商品标题、价格区间（¥98 ~ ¥158）
- 商品详情描述
- 价位选项（高手双陪 / 顶尖双陪 / 魔王双陪，可切换选中）
- 战术风格三栏价目表（单陪 / 双陪 / 保底）
- 固定底部「立刻下单」按钮 → 弹出扫码弹窗

## 🧩 可复用组件

### QrPopup.vue

扫码下单弹窗组件，支持 `v-model` 双向绑定控制显示：

```vue
<template>
  <QrPopup v-model="showQrPopup" />
</template>

<script setup>
import { ref } from 'vue'
import QrPopup from '@/components/QrPopup.vue'

const showQrPopup = ref(false)
</script>
```

## 📝 开发说明

- 路由使用 **Hash 模式**（`createWebHashHistory`），无需服务端配置即可部署
- Vant 通过 `app.use(Vant)` 全量引入，开箱即用
- 样式采用 `<style scoped>` 局部作用域，避免全局污染
- 二维码图案使用纯 CSS 渐变 + 定位方块模拟，无需图片资源

## 📄 License

MIT
