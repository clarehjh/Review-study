# Vue3 Composition API vs Options API 面试复习核心

## 一、核心知识点梳理

### 1. 设计思想差异

表格


| 维度         | Options API（选项式 API）                             | Composition API（组合式 API）                   |
| ------------ | ----------------------------------------------------- | ----------------------------------------------- |
| 核心思想     | 按「功能类型」组织代码（data/methods/computed/watch） | 按「业务逻辑」组织代码（把同一功能的代码聚合）  |
| 代码组织方式 | 分散式：同一功能的代码分散在不同选项中                | 聚合式：同一功能的代码集中在一个函数 / 模块中   |
| 复用逻辑方式 | Mixin（混入），存在命名冲突、来源不清晰问题           | Composables（组合式函数），按需导入，无命名冲突 |
| 类型支持     | 对 TypeScript 支持差（需手动类型注解）                | 天然适配 TypeScript，类型推导更友好             |
| 灵活性       | 固定选项结构，灵活性低                                | 自由组合代码，灵活性高                          |

### 2. 核心区别（面试必答）

* **代码复用**：
  * Options API：Mixin 复用逻辑，易出现命名冲突、代码来源不清晰、逻辑耦合问题；
  * Composition API：Composables（如 `useUser()``useTable()`）复用，按需导入、来源清晰、无命名冲突。
* **代码组织**：
  * Options API：同一业务功能的代码分散在 `data`/`methods`/`watch` 等选项中，代码量大时维护成本高；
  * Composition API：同一功能的状态、方法、监听逻辑聚合在一个函数中，可读性更高。
* **响应式语法**：
  * Options API：依赖 `this` 上下文，`this` 指向易混淆（嵌套函数 / 异步操作中）；
  * Composition API：无需 `this`，通过 `ref/reactive` 直接创建响应式数据，上下文清晰。
* **大型项目适配**：
  * Options API：面对大型项目，代码碎片化问题突出，维护难度大；
  * Composition API：支持逻辑拆分和按需导入，更适配大型项目模块化开发。

### 3. 业务场景选择

表格


| 场景类型                                  | 推荐 API                 | 原因                                                   |
| ----------------------------------------- | ------------------------ | ------------------------------------------------------ |
| 小型组件 / 简单业务（按钮、弹窗）         | Options API              | 语法简单、上手快，无需额外拆分逻辑，开发效率高         |
| 大型项目 / 复杂业务（数据大屏、订单系统） | Composition API          | 逻辑聚合、复用性强、适配 TS，便于多人协作和后期维护    |
| 团队技术栈含 TypeScript                   | Composition API          | 天然支持 TS 类型推导，无需大量手动注解，类型校验更完善 |
| 需复用通用逻辑（分页、权限）              | Composition API          | Composables 比 Mixin 更清晰、无冲突，可抽离为独立文件  |
| 快速原型开发 / 个人项目                   | 任选（推荐 Options API） | 快速上手，无需复杂的逻辑拆分，降低开发成本             |

## 二、面试高频题目 & 标准答案

### 题目 1：说说 Vue3 的 Composition API 和 Options API 的区别？为什么 Vue3 要推出 Composition API？

**标准答案**：

核心区别体现在设计思想、代码组织和工程化能力上，Vue3 推出 Composition API 主要解决 Options API 的痛点：

1. **设计思想**：
   * Options API 按「功能类型」（data/methods/computed）组织代码，同一业务逻辑的代码分散在不同选项中；
   * Composition API 按「业务逻辑」组织代码，同一功能的代码聚合在一处，可读性和维护性更高。
2. **核心痛点解决**：
   * 代码复用：Mixin 存在命名冲突、来源不清晰问题，Composables 按需导入、无冲突、来源明确；
   * `this` 上下文：Options API 依赖 `this`，指向易混淆；Composition API 无需 `this`，逻辑更清晰；
   * 类型支持：Options API 对 TS 支持差，Composition API 天然适配 TS，类型推导更友好；
3. **灵活性**：
   Options API 固定选项结构，灵活性低；Composition API 可自由组合逻辑，支持按需导入，适配复杂业务场景。

### 题目 2：项目中如何选择 Composition API 和 Options API？

**标准答案**：

核心原则是「按项目规模、业务复杂度和团队技术栈选择」：

1. **选 Options API 的场景**：
   * 小型组件 / 简单业务（通用按钮、弹窗、表单单项）：语法简单、上手快，无需复杂逻辑拆分；
   * 快速原型开发 / 个人小项目：降低开发成本，无需封装 Composables；
   * 团队以 Vue2 为主，迁移成本高：更贴近 Vue2 写法，学习成本低。
2. **选 Composition API 的场景**：
   * 大型项目 / 复杂业务（数据大屏、电商订单系统）：同一业务逻辑聚合管理，便于协作和维护；
   * 需复用通用逻辑（分页、权限、表单校验）：Composables 比 Mixin 更清晰，无命名冲突；
   * 技术栈包含 TypeScript：天然支持 TS 类型推导，开发体验更好；
   * 需精细化控制响应式（ref/reactive/toRefs 灵活使用）：提供更细粒度的响应式 API。

### 题目 3：Composition API 解决了 Options API 的哪些痛点？（高频追问）

**标准答案**：

Composition API 核心解决了 Options API 在「代码复用」「代码组织」「类型支持」三大核心痛点：

1. **代码复用痛点**：
   Options API 的 Mixin 存在「命名冲突」「来源不清晰」「逻辑耦合」问题；Composables 函数按需导入，代码来源清晰，无命名冲突，逻辑解耦。
2. **代码组织痛点**：
   Options API 中同一业务逻辑的代码分散在多个选项中（碎片化代码），代码量越大越难维护；Composition API 将同一功能的状态、方法、监听逻辑聚合，可读性大幅提升。
3. **类型支持痛点**：
   Options API 依赖 `this`，TS 难以推导其类型，需大量手动注解；Composition API 无需 `this`，TS 可自动推导响应式数据类型，适配性更好。
4. **其他痛点**：
   Options API 的 `this` 指向易混淆（嵌套函数 / 异步操作），Composition API 无此问题；Options API 灵活性低，Composition API 可自由拆分 / 组合逻辑。

### 题目 4：Composition API 中的 setup 函数执行时机？和 Options API 生命周期的对应关系？（进阶题）

**标准答案**：

1. **setup 执行时机**：
   setup 是 Composition API 的入口函数，在 `beforeCreate` 钩子之前执行，此时组件实例尚未创建，`this` 为 `undefined`，无法访问 Options API 中的 `this.xxx`。
2. **生命周期对应关系**（Vue3 提供 Composition API 风格的生命周期钩子）：
   表格


   | Options API 钩子                                                                                       | Composition API 钩子（需导入） |
   | ------------------------------------------------------------------------------------------------------ | ------------------------------ |
   | beforeCreate                                                                                           | setup 函数内部（替代）         |
   | created                                                                                                | setup 函数内部（替代）         |
   | beforeMount                                                                                            | onBeforeMount                  |
   | mounted                                                                                                | onMounted                      |
   | beforeUpdate                                                                                           | onBeforeUpdate                 |
   | updated                                                                                                | onUpdated                      |
   | beforeUnmount                                                                                          | onBeforeUnmount                |
   | unmounted                                                                                              | onUnmounted                    |
   | 核心逻辑：setup 覆盖`beforeCreate`和`created`，其他生命周期需导入对应钩子函数，且需在 setup 内部注册。 |                                |

### 题目 5：Composition API 比 Options API 性能更好吗？（易错题）

**标准答案**：

**性能上无本质差异**，二者的核心区别在「代码组织和开发体验」，而非性能：

1. Vue3 性能提升的核心是响应式系统优化（Proxy 替代 Object.defineProperty），与使用哪种 API 无关；
2. Composition API 不会带来额外性能开销，也不会比 Options API 更快，二者最终编译为相同的底层代码；
3. 间接优势：Composition API 支持按需导入，可减少打包体积；逻辑聚合便于代码拆分和懒加载，提升项目加载性能；
4. 总结：性能不是选择二者的关键，核心看业务场景和开发体验。

## 三、核心总结

1. **核心区别**：Options API 按「功能类型」组织代码，适合简单场景；Composition API 按「业务逻辑」组织代码，适合复杂场景，适配 TS、复用性更强；
2. **场景选择**：小型 / 简单项目用 Options API，大型 / 复杂项目 / TS 项目用 Composition API；
3. **面试答题关键**：先讲设计思想差异，再谈核心痛点解决，最后结合业务场景给出选择依据，避免只罗列区别而不落地。

