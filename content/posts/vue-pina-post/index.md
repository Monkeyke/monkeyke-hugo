+++
date = '2025-08-04T05:34:53+08:00'
draft = false
title = 'Vue3.0 中 Pinia 的使用指南'

+++

# 

![Pinia封面图](https://picsum.photos/id/96/1200/400)

## 一、Pinia 简介

Pinia 是 Vue3 官方推荐的状态管理库，替代了传统的 Vuex，具有以下优势：



*   完整支持 TypeScript

*   简化的 API，去除了 mutations

*   支持 Vue DevTools

*   模块化设计，易于拆分状态

*   支持服务器端渲染

## 二、Pinia 核心概念

Pinia 的核心概念主要有：



*   **Store**：存储状态的容器，每个 Store 都是一个独立的模块

*   **State**：存储的状态数据

*   **Getters**：类似计算属性，用于处理派生状态

*   **Actions**：用于处理业务逻辑，支持异步操作



![Pinia核心概念图](https://picsum.photos/id/201/800/400)

## 三、Pinia 基本使用

### 3.1 安装与初始化



1.  安装 Pinia：



```
npm install pinia

\# 或

yarn add pinia
```



1.  在 main.ts 中初始化：



```
import { createApp } from 'vue'

import { createPinia } from 'pinia'

import App from './App.vue'

const app = createApp(App)

app.use(createPinia())

app.mount('#app')
```

### 3.2 创建 Store

创建`stores/counter.ts`：



```
import { defineStore } from 'pinia'

// 定义并导出Store

export const useCounterStore = defineStore('counter', {

&#x20; // 状态

&#x20; state: () => ({

&#x20;   count: 0,

&#x20;   name: 'Pinia'

&#x20; }),

&#x20;&#x20;

&#x20; // 计算属性

&#x20; getters: {

&#x20;   doubleCount: (state) => state.count \* 2,

&#x20;   // 使用this访问其他getter

&#x20;   greeting: function() {

&#x20;     return \`Hello, \${this.name}!\`

&#x20;   }

&#x20; },

&#x20;&#x20;

&#x20; // 方法

&#x20; actions: {

&#x20;   increment() {

&#x20;     this.count++

&#x20;   },

&#x20;   // 异步操作

&#x20;   async incrementAsync() {

&#x20;     await new Promise(resolve => setTimeout(resolve, 1000))

&#x20;     this.increment()

&#x20;   }

&#x20; }

})
```

### 3.3 在组件中使用

在 Vue 组件中使用 Store：



```
\<template>

&#x20; \<div>

&#x20;   \<p>Count: {{ counterStore.count }}\</p>

&#x20;   \<p>Double Count: {{ counterStore.doubleCount }}\</p>

&#x20;   \<p>{{ counterStore.greeting }}\</p>

&#x20;   \<button @click="counterStore.increment">Increment\</button>

&#x20;   \<button @click="counterStore.incrementAsync">Increment Async\</button>

&#x20; \</div>

\</template>

\<script setup lang="ts">

import { useCounterStore } from './stores/counter'

// 获取Store实例

const counterStore = useCounterStore()

\</script>
```

## 四、Pinia 高级用法

### 4.1 状态修改方式

除了直接修改和通过 action 修改，还可以使用`$patch`批量修改：



```
// 方式1：直接修改

counterStore.count++

// 方式2：通过action

counterStore.increment()

// 方式3：\$patch

counterStore.\$patch({

&#x20; count: counterStore.count + 2,

&#x20; name: 'New Name'

})

// 方式4：\$patch函数形式

counterStore.\$patch((state) => {

&#x20; state.count += 3

&#x20; state.name = 'Updated Name'

})
```

### 4.2 持久化存储

结合`pinia-plugin-persistedstate`实现状态持久化：



1.  安装插件：



```
npm install pinia-plugin-persistedstate
```



1.  配置插件：



```
import { createPinia } from 'pinia'

import piniaPluginPersistedstate from 'pinia-plugin-persistedstate'

const pinia = createPinia()

pinia.use(piniaPluginPersistedstate)
```



1.  在 Store 中启用持久化：



```
export const useCounterStore = defineStore('counter', {

&#x20; state: () => ({ count: 0 }),

&#x20; // 启用持久化

&#x20; persist: true

})
```

### 4.3 Store 之间的通信

不同 Store 之间可以直接相互调用：



```
// stores/user.ts

import { defineStore } from 'pinia'

import { useCounterStore } from './counter'

export const useUserStore = defineStore('user', {

&#x20; actions: {

&#x20;   login() {

&#x20;     // 调用其他Store的action

&#x20;     const counterStore = useCounterStore()

&#x20;     counterStore.increment()

&#x20;   }

&#x20; }

})
```

## 五、Pinia 与 Vuex 的对比



| 特性            | Pinia | Vuex       |
| ------------- | ----- | ---------- |
| mutations     | 无     | 有          |
| TypeScript 支持 | 原生支持  | 需要额外配置     |
| 模块化           | 天然支持  | 需要 modules |
| 代码简洁度         | 更高    | 较低         |
| Vue3 兼容性      | 完美支持  | 较好         |



![Pinia与Vuex对比](https://picsum.photos/id/180/800/400)

通过以上内容，我们可以看到 Pinia 作为新一代的状态管理库，在 Vue3 项目中使用更加简洁高效，推荐在新的 Vue3 项目中使用 Pinia 替代 Vuex。