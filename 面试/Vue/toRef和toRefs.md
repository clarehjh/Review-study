---
theme: channing-cyan
---
# 核心概念

toRef/toRefs 仅用于解决「解构 / 单独提取属性」时的响应式丢失问题，只有当需要解构 reactive 对象，或单独提取对象的某个属性传递时，才需要 toRef/toRefs 来保留与原对象的响应式关联；

reactive/ref 是**创建**响应式数据，而 toRef/toRefs 是**保留**已有响应式数据的关联关系，二者的核心目标完全不同：

- reactive/ref：让「普通数据」变成「响应式数据」；
- toRef/toRefs：让「已有的响应式数据」在解构 / 单独传递时，不丢失与原对象的响应式关联。

### 用代码示例拆解（最易理解）

#### 场景 1：直接解构 reactive 对象（无 toRefs）—— 响应式丢失

vue

```
<template>
  <div>{{ name }} {{ age }}</div>
  <button @click="changeName">修改名字</button>
</template>

<script setup>
import { reactive } from 'vue'

// 1. 用 reactive 创建响应式对象（本身是响应式的）
const user = reactive({
  name: '张三',
  age: 20
})

// 2. 直接解构——得到的是普通值，丢失响应式关联
const { name, age } = user

// 3. 修改解构后的 name，原对象 user.name 会变，但页面不会更新！
const changeName = () => {
  name = '李四' // 这里甚至会报错：Assignment to constant variable
  // 即使改成 let { name, age } = user，修改 name 也只是修改普通变量，页面不更新
}
</script>
```

**关键问题**：reactive 创建的 user 本身是响应式的，但解构后 `name/age` 只是「普通变量的赋值」，和原响应式对象 user 断开了关联 —— 修改解构后的变量，无法触发页面更新。
reactive 创建的 user 本身是响应式的，但解构后 `name/age` 只是「普通变量的赋值」，和原响应式对象 user 断开了关联 —— 修改解构后的变量，无法触发页面更新。

#### 场景 2：用 toRefs 解构 —— 保留响应式关联

vue

```
<template>
  <div>{{ name }} {{ age }}</div>
  <button @click="changeName">修改名字</button>
</template>

<script setup>
import { reactive, toRefs } from 'vue'

const user = reactive({
  name: '张三',
  age: 20
})

// 1. 用 toRefs 转换后解构——得到的是 ref 类型，和原对象保持关联
const { name, age } = toRefs(user)

// 2. 修改 ref 的 .value，原对象 user.name 同步变化，且页面更新
const changeName = () => {
  name.value = '李四' // 页面会更新为「李四 20」，user.name 也变成李四
}
</script>
```

**核心逻辑**：toRefs 并没有 “新增” 响应式，而是把 reactive 对象的每个属性「包装成 ref」，且这个 ref 的 `.value` 直接指向原对象的属性 —— 相当于给原响应式属性做了一个 “响应式别名”，解构后修改这个别名，依然能触发原对象的响应式更新。

#### 场景 3：toRef 的作用（单个属性的 “响应式别名”）

vue

```
<script setup>
import { reactive, toRef } from 'vue'

const user = reactive({
  name: '张三',
  age: 20
})

// 只给 name 属性创建响应式别名
const nameRef = toRef(user, 'name')

// 修改 nameRef.value，原对象同步更新，触发响应式
nameRef.value = '李四'
console.log(user.name) // 输出：李四
</script>
```

**适用场景**：不需要解构所有属性，只需要单独传递某个属性（比如把 name 传给子组件），且希望子组件修改该属性时，同步更新父组件的原对象。

### 面试中如何回答这个问题（标准答案）

“toRef/toRefs 并不是将非响应式数据转为响应式，而是为**已有的响应式对象**的属性创建「响应式引用」：

1. reactive/ref 的作用是「创建」响应式数据，让普通数据具备响应式能力；
2. 直接解构 reactive 对象会丢失响应式（因为解构得到的是普通值），而 toRef/toRefs 能让解构后的属性依然和原响应式对象保持关联 —— 修改解构后的 ref 属性，会同步更新原对象，且触发页面更新；
3. 简单说：reactive/ref 是 “造响应式”，toRef/toRefs 是 “保响应式关联”。”

## 实际开发中真正需要用 toRef/toRefs 的场景

#### 场景 1：组合式函数（Composables）返回响应式对象，解构使用

这是 **toRefs 最核心、最常用** 的场景！Vue3 推荐用组合式函数抽离复用逻辑，这类函数通常返回 reactive 对象，调用方解构时必须用 toRefs 保留响应式。

javascript

运行

```
// composables/useUser.js（复用逻辑）
import { reactive, toRefs } from 'vue'

export function useUser() {
  const user = reactive({
    name: '张三',
    age: 20,
    token: 'xxx'
  })

  const updateName = (newName) => {
    user.name = newName
  }

  // 关键：返回时用 toRefs 包装，让调用方能解构
  return {
    ...toRefs(user), // 把 user 的属性转为 ref
    updateName
  }
}

// 组件中使用
import { useUser } from '@/composables/useUser'

const { name, age, updateName } = useUser()
// 解构后的 name 是 ref，修改 .value 或调用 updateName 都能触发响应式更新
console.log(name.value) // 张三
updateName('李四')
console.log(name.value) // 李四
```

**如果不用 toRefs**：调用方解构得到的 `name` 是普通字符串，修改 / 更新都不会触发响应式，组合式函数的复用逻辑就失效了。

#### 场景 2：子组件接收父组件传递的响应式对象属性，需单独修改

父组件传递 reactive 对象给子组件，子组件只需要其中一个属性，且要修改该属性并同步回父组件：

vue

```
<!-- 父组件 -->
<template>
  <Child :name="user.name" />
</template>
<script setup>
import { reactive } from 'vue'
import Child from './Child.vue'

const user = reactive({ name: '张三' })
</script>

<!-- 子组件（不好的写法） -->
<script setup>
const props = defineProps(['name'])
// props.name 是普通值，无法直接修改，也无法同步回父组件
</script>

<!-- 子组件（正确写法：用 toRef） -->
<script setup>
import { toRef } from 'vue'

const props = defineProps(['user']) // 父组件改为传递整个 user 对象
const nameRef = toRef(props.user, 'name') // 保留响应式关联

// 修改 nameRef.value 会同步更新父组件的 user.name
const changeName = () => {
  nameRef.value = '李四'
}
</script>
```

#### 场景 3：解构 reactive 对象，且需要保持响应式（少量场景）

比如组件内定义了复杂的 reactive 对象，为了代码简洁想解构使用，又不想丢失响应式：

vue

```
<script setup>
import { reactive, toRefs } from 'vue'

const form = reactive({
  username: '',
  password: '',
  remember: false
})

// 解构后代码更简洁，且保留响应式
const { username, password, remember } = toRefs(form)

// 绑定到表单时，直接用 ref 即可（模板自动解包）
</script>
<template>
  <input v-model="username" />
  <input v-model="password" type="password" />
  <input v-model="remember" type="checkbox" />
</template>
```

### 二、为什么面试高频问（哪怕业务中用得少）？

1. **考察响应式原理的理解深度**：能答出 toRef/toRefs 的使用场景，说明你不是只 “会用” Vue3，而是理解「响应式丢失的本质」（解构切断引用链），这是区分 “初级” 和 “中级” 前端的关键。
2. **考察组合式 API 的使用经验**：toRef/toRefs 是组合式函数（Composables）的 “标配”，面试官通过这个问题，能判断你是否真正用 Vue3 做过模块化、可复用的开发。
3. **避坑能力**：很多新手会踩 “解构 reactive 对象丢失响应式” 的坑，能说出 toRef/toRefs 的作用，说明你遇到过并解决过这类问题。

### 三、总结

1. **开发视角**：

   - 日常简单业务（如单组件内定义响应式、不解构）：几乎用不到 toRef/toRefs；
   - 复杂业务（组合式函数复用、父子组件传递响应式属性、解构复杂对象）：toRef/toRefs 是刚需，能大幅提升代码质量；
2. **面试视角**：

   - 核心答题逻辑：先承认 “日常开发中不是高频 API”，再说明 “在组合式函数、解构响应式对象场景下是必需的”，最后解释 “作用是保留响应式关联，解决解构丢失响应式的问题”；
   - 关键：结合组合式函数的例子回答，能体现你的实际开发经验，加分效果显著。

**注意：**toRef 直接关联父组件响应式属性** vs **emit 通知父组件修改**，核心都是遵循「单向数据流」原则，只是实现思路和适用场景不同。**


| 方案                | 核心逻辑                                | 优点                        | 缺点                                   | 适用场景                                                   |
| ------------------- | --------------------------------------- | --------------------------- | -------------------------------------- | ---------------------------------------------------------- |
| toRef 关联属性      | 子组件直接操作父组件属性的 “响应式引用” | 代码更简洁，少写 emit/watch | 子组件直接关联父组件数据，逻辑耦合稍高 | 简单场景（仅修改单个属性，父子组件逻辑紧密）               |
| emit 通知父组件修改 | 子组件只发通知，父组件自己修改          | 单向数据流更清晰，耦合度低  | 代码稍繁琐（需写 emit/watch）          | 复杂场景（修改属性前需校验、联动其他数据，或父子组件解耦） |

### 关键原则：都符合「单向数据流」

很多人会误以为 “子组件用 toRef 修改父组件属性” 违背单向数据流，但其实**并没有**：

- 单向数据流的核心是「子组件不能直接修改 props 本身」（比如 `props.user.name = '李四'` 是禁止的）；
- toRef 创建的是「props.user.name 的引用」，本质上修改的是父组件的响应式对象属性，而非 props 本身，完全符合 Vue 的设计原则。

### 四、面试中如何回答这个问题（标准答案）

“是的，这两种方式都是可行的，且都符合 Vue 单向数据流的设计原则：

1. 用 toRef 的方式更简洁，通过创建父组件属性的响应式引用，子组件修改引用时同步更新父组件，适合简单的属性修改场景；
2. 用 emit 通知父组件修改的方式，子组件只负责触发事件，父组件自己处理修改逻辑，单向数据流更清晰，耦合度更低，适合复杂场景（比如修改前需要校验、修改后需要联动其他数据）；
3. 实际开发中可以根据场景选择：简单场景用 toRef 简化代码，复杂场景用 emit 保证代码解耦。”

### 总结

1. 两种方案等价且都合规，核心区别是「代码简洁性」和「组件耦合度」的权衡；
2. toRef 适合简单场景，emit 适合复杂场景 / 需要解耦的场景；
