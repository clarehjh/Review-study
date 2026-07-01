# 前端面试复习笔记

> 共 22 篇笔记，按主题分为 6 个模块

---

## 📂 CSS（5 篇）

| 文件 | 核心内容 |
|------|----------|
| [回流和重绘](CSS/回流和重绘.md) | reflow/repaint 触发条件、合成器、避免策略 |
| [BFC块级格式化上下文](CSS/BFC块级格式化上下文.md) | BFC 概念、创建方式、解决 margin 折叠和浮动塌陷 |
| [CSS3新特性](CSS/CSS3新特性.md) | 选择器、背景边框、文本效果、2D/3D 转换和动画 |
| [物理像素与逻辑像素](CSS/物理像素与逻辑像素.md) | 物理像素 vs 逻辑像素、@2x/@3x 倍图、0.5px 实现 |
| [浏览器兼容问题](CSS/浏览器兼容问题.md) | 不同浏览器兼容处理 |

## 📂 JavaScript（6 篇）

| 文件 | 核心内容 |
|------|----------|
| [原型链与继承](JavaScript/原型链与继承.md) | 原型链原理、手写 new、组合继承、寄生组合继承 |
| [闭包与内存泄漏](JavaScript/闭包与内存泄漏.md) | 闭包定义、私有变量/防抖/柯里化场景、内存泄漏解决 |
| [异步编程与Promise](JavaScript/异步编程与Promise.md) | 事件循环、宏微任务、手写 Promise.all/race、async-await |
| [深浅拷贝](JavaScript/深浅拷贝.md) | 浅拷贝方法、手写深拷贝（含循环引用）、WeakMap 作用 |
| [防抖与节流](JavaScript/防抖与节流.md) | 手写防抖节流（基础版+进阶版）、业务场景、this 绑定 |
| [ES6核心特性](JavaScript/ES6核心特性.md) | 解构、箭头函数、Set/Map/WeakMap、Proxy/Reflect、Symbol |

## 📂 HTML（2 篇）

| 文件 | 核心内容 |
|------|----------|
| [script标签async和defer](HTML/script标签async和defer.md) | async vs defer 区别、DOM 解析阻塞、DOMContentLoaded |
| [SEO搜索引擎优化](HTML/SEO搜索引擎优化.md) | SSR、TDK 描述、语义化 HTML、robots.txt、https |

## 📂 Vue（5 篇）

| 文件 | 核心内容 |
|------|----------|
| [Vue2与Vue3响应式原理](Vue/Vue2与Vue3响应式原理.md) | Object.defineProperty vs Proxy、Ref/Reactive 区别、toRef/toRefs |
| [CompositionAPI与OptionsAPI](Vue/CompositionAPI与OptionsAPI.md) | 设计思想差异、场景选择、面试高频题 |
| [组件通信](Vue/组件通信.md) | 10+ 种通信方式、provide/inject、v-model、mitt、Pinia |
| [路由与状态管理](Vue/路由与状态管理.md) | 路由守卫、动态路由、懒加载、hash/history、Vuex/Pinia 持久化 |
| [toRef和toRefs](Vue/toRef和toRefs.md) | toRef/toRefs 核心概念、响应式关联、实际开发场景 |

## 📂 后端（1 篇）

| 文件 | 核心内容 |
|------|----------|
| [Node.js与Koa面试要点](后端/Node.js与Koa面试要点.md) | Koa 洋葱模型、小程序登录鉴权(JWT)、跨域处理 |

## 📂 综合（4 篇）

| 文件 | 核心内容 |
|------|----------|
| [面试题库](综合/面试题库.md) | Vue3/JS/CSS/性能/工程化/网络/安全等 50+ 高频面试题 |
| [复习规划](综合/复习规划.md) | 4 周复习计划、霸王茶姬业务场景适配、STAR 法则 |
| [算法准备](综合/算法准备.md) | 前端高频算法题范围、扁平转树形、LeetCode 必刷题 |
| [AI工具面试话术](综合/AI工具面试话术.md) | 如何表述 AI 辅助开发经历、面试话术模板 |

---

## 🎯 复习路线建议

```
第1周：JavaScript → 第2周：Vue → 第3周：CSS + HTML → 第4周：综合 + 后端
```

| 优先级 | 模块 | 原因 |
|--------|------|------|
| ⭐⭐⭐ | JavaScript | 面试核心，手写题和原理题最多 |
| ⭐⭐⭐ | Vue | 你的优势区，要答出深度 |
| ⭐⭐ | 综合 | 面试题库 + 复习规划是总纲 |
| ⭐⭐ | CSS | 高频基础题，快速过 |
| ⭐ | HTML | 内容少，半天搞定 |
| ⭐ | 后端 | 差异化加分项，1-2 天复习 |