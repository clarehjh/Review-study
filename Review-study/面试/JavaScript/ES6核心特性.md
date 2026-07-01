
# ES6+ 核心高频面试题（15k + 前端岗位版）

## 说明

本文聚焦 15k + 前端岗位高频考察的 ES6 + 核心知识点（解构、箭头函数、Set/Map、Proxy/Reflect、Symbol），答案兼顾**准确性**与**面试表达逻辑**，既覆盖基础考点，也包含区分度高的进阶问题，可直接用于面试备考。

---

## 一、解构赋值

### 1. 数组解构和对象解构的核心区别是什么？（高频基础）

#### 解答：

表格


| 维度       | 数组解构                            | 对象解构                                       |
| ---------- | ----------------------------------- | ---------------------------------------------- |
| 匹配规则   | 按**索引 / 位置**匹配，与顺序强相关 | 按**属性名**匹配，与顺序无关                   |
| 变量名约束 | 可自定义，无需和元素名关联          | 需与属性名一致（或通过别名`key: alias`自定义） |
| 典型场景   | 提取数组固定位置的值、交换变量      | 提取对象属性、函数参数解构                     |

#### 示例：

javascript

运行

```
// 数组解构（按位置）
const [a, b] = [1, 2]; // a=1, b=2
const [, c] = [1, 2]; // 跳过第一个元素，c=2

// 对象解构（按属性名）
const { name: userName, age = 18 } = { name: "张三" }; // userName="张三", age=18

// 交换变量（数组解构经典场景）
let x = 1, y = 2;
[x, y] = [y, x]; // x=2, y=1
```

### 2. 函数参数使用解构赋值有哪些优势？（进阶）

#### 解答：

1. **简化参数处理**：无需反复通过 `obj.key` 提取属性，代码更简洁；
2. **便捷设置默认值**：直接在解构位置定义默认参数，避免函数内部判断；
3. **提升可读性**：参数结构清晰，一眼能看出函数依赖的属性；
4. **适配 TypeScript**：便于结合类型注解约束参数结构，符合 TS 最佳实践。

#### 示例：

javascript

运行

```
// 未使用解构：参数处理繁琐
function getUserInfo(options) {
  const name = options.name;
  const age = options.age || 18;
  console.log(name, age);
}

// 使用解构：简洁且自带默认值
function getUserInfo({ name, age = 18 } = {}) { // 解构+默认空对象，避免传undefined报错
  console.log(name, age);
}
getUserInfo({ name: "李四" }); // 李四 18
```

### 3. 解构赋值的默认值什么时候生效？（面试陷阱题）

#### 解答：

解构的默认值**仅当被解构的属性 / 元素值为 `undefined` 时生效**，`null`、`0`、`''` 等假值不会触发默认值。

#### 示例：

javascript

运行

```
const [a = 1] = [undefined]; // a=1（触发默认值）
const [b = 1] = [null]; // b=null（不触发）
const { c = 1 } = { c: 0 }; // c=0（不触发）
const { d = 1 } = { d: '' }; // d=''（不触发）
```

---

## 二、箭头函数

### 1. 箭头函数与普通函数的核心区别？（必考核心）

#### 解答：

表格


| 维度       | 箭头函数                                                               | 普通函数                                                        |
| ---------- | ---------------------------------------------------------------------- | --------------------------------------------------------------- |
| this 绑定  | 词法绑定（定义时继承外层作用域的 this），无法通过 call/apply/bind 修改 | 动态绑定（调用时由执行上下文决定），可通过 call/apply/bind 修改 |
| 构造函数   | 不能作为构造函数（无 prototype，new 调用报错）                         | 可以作为构造函数（new 调用生成实例）                            |
| arguments  | 无 arguments 对象，需用...rest 参数替代                                | 有 arguments 对象（类数组，保存所有入参）                       |
| 原型       | 无 prototype 属性                                                      | 有 prototype 属性                                               |
| 生成器函数 | 不能定义为生成器（无法加\*）                                           | 可以定义为生成器函数                                            |

#### 示例：

javascript

运行

```
// this绑定差异
const obj = {
  fn1: function() { console.log(this); }, // 调用时指向obj
  fn2: () => { console.log(this); } // 定义时外层是全局，指向window
};
obj.fn1(); // obj
obj.fn2(); // window

// 箭头函数无arguments
const fn1 = () => console.log(arguments); // 调用报错：arguments is not defined
const fn2 = (...args) => console.log(args); // 用rest参数替代，args是真数组
fn2(1,2,3); // [1,2,3]

// 箭头函数不能new
const Fn = () => {};
new Fn(); // 报错：Fn is not a constructor
```

### 2. 哪些场景不适合使用箭头函数？（进阶）

#### 解答：

箭头函数的 `this` 特性决定了以下场景不适用：

1. **对象方法**：会丢失对象上下文，this 指向外层（如 window）；
2. **DOM 事件回调**：无法通过 this 获取触发事件的 DOM 元素；
3. **构造函数**：new 调用会报错；
4. **原型方法**：原型链中 this 指向异常；
5. **需要动态修改 this 的场景**：如防抖 / 节流中需保留原上下文（注：防抖函数内部是箭头函数包裹，但核心执行函数需用 apply 绑定 this）。

#### 示例：

javascript

运行

```
// 错误场景1：对象方法
const btn = {
  text: "点击",
  click: () => {
    console.log(this.text); // this指向window，text为undefined
  }
};

// 错误场景2：DOM事件回调
document.querySelector('button').addEventListener('click', () => {
  console.log(this); // this指向window，而非按钮元素
});
```

### 3. 箭头函数的 this 能通过 bind 修改吗？（面试陷阱题）

#### 解答：

**不能**。

箭头函数的 this 是「词法作用域绑定」，在定义时就固定为外层作用域的 this，call/apply/bind 仅能传递参数，无法修改其 this 指向。

#### 示例：

javascript

运行

```
const fn = () => console.log(this);
const obj = { name: "张三" };
fn.bind(obj)(); // 依然输出window，而非obj
fn.call(obj, 1, 2); // 仅传递参数1、2，this仍为window
```

---

## 三、Set/Map（含 WeakSet/WeakMap）

### 1. Set 的核心特性及典型应用场景？（高频）

#### 解答：

##### 核心特性：

1. 存储**唯一值**（无重复元素），支持所有原始类型 + 引用类型（NaN 在 Set 中视为相等）；
2. 无序集合，无索引，无法通过下标访问；
3. 遍历支持：for...of、forEach、keys/values/entries（keys 和 values 返回值相同）；
4. 增删查效率高（O (1) 时间复杂度）。

##### 典型场景：

1. **数组去重**（最经典）：`[...new Set(arr)]`；
2. **判断元素是否存在**：`set.has(item)` 比 `arr.indexOf(item)` 更高效；
3. **求数组交集 / 并集 / 差集**：结合 Set 的 has 方法快速实现；
4. **去重字符串**：`[...new Set(str)].join('')`。

#### 示例：

javascript

运行

```
// 数组去重
const arr = [1, 2, 2, 3, NaN, NaN];
const uniqueArr = [...new Set(arr)]; // [1,2,3,NaN]

// 求交集
const a = new Set([1,2,3]);
const b = new Set([2,3,4]);
const intersect = [...a].filter(item => b.has(item)); // [2,3]
```

### 2. Map 与普通对象（{}）的核心区别？（必考）

#### 解答：

表格


| 维度     | Map                                   | 普通对象（{}）                                    |
| -------- | ------------------------------------- | ------------------------------------------------- |
| 键的类型 | 支持任意类型（对象、函数、Symbol 等） | 仅支持字符串 / Symbol（非字符串键会被转为字符串） |
| 遍历顺序 | 严格按「插入顺序」遍历                | ES6 后部分浏览器按插入顺序，但非标准，不依赖      |
| 大小获取 | 直接通过 size 属性（O (1)）           | 需手动遍历统计（Object.keys (obj).length，O (n)） |
| 性能     | 高频增删查场景性能更优                | 适合少量数据，高频操作性能一般                    |
| 原型污染 | 无（原型链独立）                      | 可能受 Object.prototype 污染                      |

#### 示例：

javascript

运行

```
// Map支持对象作为键
const objKey = { id: 1 };
const map = new Map();
map.set(objKey, "value");
console.log(map.get(objKey)); // "value"

// 普通对象无法用对象作为键（会转为字符串）
const obj = {};
obj[objKey] = "value";
console.log(obj["[object Object]"]); // "value"
```

### 3. WeakSet/WeakMap 的核心特性及应用场景？（进阶，区分度高）

#### 解答：

##### 核心特性（以 WeakMap 为例，WeakSet 类似）：

1. **弱引用**：键仅支持对象类型，且对键的引用是 “弱引用”，不影响垃圾回收（当键对象无其他强引用时，会被 GC 回收，WeakMap 自动删除对应键值对）；
2. **不可遍历**：无 size 属性，无法通过 for...of/forEach 遍历，也无 keys/values/entries 方法；
3. **操作受限**：仅支持 get/set/delete/has 方法，无 clear 方法。

##### 应用场景：

1. **缓存数据**：缓存对象的计算结果，对象销毁后缓存自动清理，避免内存泄漏；
2. **DOM 元素关联数据**：给 DOM 元素绑定额外数据，元素移除后数据自动回收；
3. **私有属性**：模拟对象的私有属性（如 Vue3 的响应式实现中用 WeakMap 存储依赖）。

#### 示例：

javascript

运行

```
// WeakMap示例：缓存对象数据
const cache = new WeakMap();
function getObjData(obj) {
  if (cache.has(obj)) {
    return cache.get(obj); // 命中缓存
  }
  const data = /* 复杂计算逻辑 */;
  cache.set(obj, data);
  return data;
}

// 当obj无其他引用时，cache中的键值对会被自动清理
let obj = { id: 1 };
getObjData(obj);
obj = null; // 解除强引用，GC会回收obj，cache中对应数据也被删除
```

---

## 四、Proxy/Reflect

### 1. Proxy 的核心作用及常用拦截器？（15k + 必问）

#### 解答：

##### 核心作用：

Proxy 是 ES6 提供的「对象代理器」，能创建目标对象的代理，**拦截并自定义对象的底层操作**（元编程），相比 Object.defineProperty 功能更全面、更灵活，是 Vue3 响应式的核心实现方案。

##### 常用拦截器：

表格


| 拦截器                            | 拦截的操作                    | 典型场景                |
| --------------------------------- | ----------------------------- | ----------------------- |
| get(target, key, receiver)        | 读取属性（obj.key/obj [key]） | 依赖收集（Vue3 响应式） |
| set(target, key, value, receiver) | 赋值属性（obj.key = value）   | 触发更新（Vue3 响应式） |
| deleteProperty(target, key)       | 删除属性（delete obj.key）    | 监听属性删除            |
| has(target, key)                  | 判断属性存在（key in obj）    | 自定义 in 操作逻辑      |
| apply(target, thisArg, args)      | 调用函数（fn ()）             | 函数调用拦截、防抖节流  |
| construct(target, args)           | 构造函数调用（new Fn ()）     | 拦截 new 操作           |

#### 示例（简易响应式）：

javascript

运行

```
const reactive = (target) => {
  return new Proxy(target, {
    get(target, key) {
      console.log(`读取${key}，收集依赖`);
      return Reflect.get(target, key); // 配合Reflect执行原始操作
    },
    set(target, key, value) {
      console.log(`设置${key}=${value}，触发更新`);
      return Reflect.set(target, key, value);
    }
  });
};

const obj = reactive({ name: "张三" });
obj.name; // 读取name，收集依赖
obj.name = "李四"; // 设置name=李四，触发更新
```

### 2. Reflect 的核心作用及与 Proxy 的关系？（进阶）

#### 解答：

##### 核心作用：

1. **函数式操作对象**：将 Object 的命令式操作（如 delete、in）转为函数式操作，统一 API 风格；
2. **统一返回值**：操作结果以布尔值返回（成功 true / 失败 false），避免 Object 操作抛出错误；
3. **配合 Proxy 使用**：Reflect 的方法参数与 Proxy 拦截器参数完全对应，能安全执行目标对象的原始操作，且保留 this 绑定；
4. **避免原型污染**：Reflect.get/set 可通过 receiver 参数保证 this 指向正确。

##### 与 Proxy 的关系：

Proxy 负责「拦截操作」，Reflect 负责「执行原始操作」，二者是**配套设计**的最佳实践：

* Proxy 拦截后，通过 Reflect 执行目标对象的原始操作，而非直接操作 target（避免破坏上下文）；
* 所有 Proxy 拦截器都有对应的 Reflect 方法，参数完全一致。

#### 示例：

javascript

运行

```
// 不使用Reflect：直接操作target，可能丢失上下文
const proxy = new Proxy({ name: "张三" }, {
  get(target, key) {
    return target[key]; // 直接访问，receiver失效
  }
});

// 使用Reflect：保留上下文
const proxy = new Proxy({ name: "张三" }, {
  get(target, key, receiver) {
    return Reflect.get(target, key, receiver); // 正确传递receiver
  }
});
```

**核心差异：this 指向 obj 时操作 “原始对象”，绕开 Proxy；指向 proxy2 时操作 “代理对象”，所有行为都被 Proxy 拦截；
实际影响：指向 proxy2 才能发挥 Proxy 的 “代理 / 监控 / 自定义” 能力，指向 obj 会让 Proxy 形同虚设；
关键用法：在 Proxy 拦截器中用 Reflect.get/set 传递 receiver，就是为了让 this 始终指向 Proxy 实例，保证代理逻辑不失效。
简单记：this 指向谁，就相当于 “直接和谁打交道”—— 指向 obj 是和原对象直接聊，指向 proxy2 是通过代理聊，代理能帮你过滤 / 修改聊天内容。**
### 3. Proxy 对比 Object.defineProperty 有哪些优势？（核心考点）

#### 解答：

表格


| 维度     | Object.defineProperty                                            | Proxy                                          |
| -------- | ---------------------------------------------------------------- | ---------------------------------------------- |
| 拦截范围 | 仅能拦截 get/set（属性级）                                       | 支持 13 种拦截操作（对象 / 函数 / 数组全场景） |
| 数组支持 | 无法直接拦截数组下标赋值、push/pop 等方法（Vue2 需重写数组方法） | 天然支持数组所有操作拦截                       |
| 新增属性 | 无法监听（Vue2 需\$set 手动处理）                                | 自动监听新增属性                               |
| 删除属性 | 无法监听（Vue2 需\$delete 手动处理）                             | 可通过 deleteProperty 拦截                     |
| 嵌套对象 | 需递归遍历手动绑定，性能一般                                     | 可配合递归实现深层代理，更灵活                 |
| 错误处理 | 操作不可配置属性会抛出错误                                       | 可通过 Reflect 返回布尔值，优雅处理            |

---

## 五、Symbol

### 1. Symbol 的核心特性及应用场景？（高频）

#### 解答：

##### 核心特性：

1. **新原始类型**：ES6 新增的第七种原始类型（其余：string/number/boolean/null/undefined/object）；
2. **唯一性**：每个 Symbol 实例唯一，即使描述符相同也不相等（Symbol ('a') !== Symbol ('a')）；
3. **不可枚举**：作为对象属性名时，不会被 for...in/Object.keys/JSON.stringify 遍历；
4. **可描述**：通过 Symbol.description 获取描述符（ES2019 新增）；
5. **全局注册表**：Symbol.for (key) 可创建 / 复用全局 Symbol（相同 key 返回同一个实例）。

##### 应用场景：

1. **避免属性名冲突**：定义对象的唯一属性（如第三方库的扩展属性）；
2. **模拟私有属性**：利用不可枚举特性，实现对象的 “私有” 属性；
3. **自定义内置行为**：通过内置 Symbol（如 Symbol.iterator、Symbol.toPrimitive）改写对象默认行为；
4. **定义常量**：避免常量值重复（如状态码、枚举值）。

#### 示例：

javascript

运行

```
// 特性1：唯一性
const s1 = Symbol("test");
const s2 = Symbol("test");
console.log(s1 === s2); // false

// 特性2：不可枚举
const obj = {
  [s1]: "value",
  name: "张三"
};
console.log(Object.keys(obj)); // ["name"]
console.log(obj[s1]); // "value"（仅能通过Symbol本身访问）

// 特性3：全局注册表
const s3 = Symbol.for("global");
const s4 = Symbol.for("global");
console.log(s3 === s4); // true

// 应用：自定义迭代器（Symbol.iterator）
const obj = { a: 1, b: 2 };
obj[Symbol.iterator] = function* () {
  yield this.a;
  yield this.b;
};
for (let val of obj) {
  console.log(val); // 1, 2
}
```

### 2. Symbol.for () 与 Symbol () 的区别？（面试陷阱题）

#### 解答：

表格


| 维度       | Symbol()                | Symbol.for(key)                     |
| ---------- | ----------------------- | ----------------------------------- |
| 全局注册表 | 不纳入全局注册表        | 纳入全局注册表                      |
| 唯一性     | 每次调用生成新实例      | 相同 key 复用同一个实例             |
| 反向查找   | 无（无法通过 key 获取） | 可通过 Symbol.keyFor (sym) 获取 key |

#### 示例：

javascript

运行

```
const s1 = Symbol("test");
const s2 = Symbol.for("test");
const s3 = Symbol.for("test");

console.log(s1 === s2); // false
console.log(s2 === s3); // true

// 反向查找key
console.log(Symbol.keyFor(s2)); // "test"
console.log(Symbol.keyFor(s1)); // undefined（Symbol()创建的无key）
```

---

## 核心考点总结（面试速记）

1. **解构赋值**：核心是 “按规则提取数据”，重点掌握函数参数解构、默认值生效条件；
2. **箭头函数**：this 词法绑定（不可改）、无 arguments、不能当构造函数；
3. **Set/Map**：Set 去重、Map 任意键 + 有序，WeakMap/WeakSet 弱引用避免内存泄漏；
4. **Proxy/Reflect**：Proxy 拦截操作（Vue3 响应式），Reflect 安全执行操作，对比 Object.defineProperty 的优势；
5. **Symbol**：唯一性、不可枚举，Symbol.for () 全局复用，内置 Symbol 自定义对象行为。
