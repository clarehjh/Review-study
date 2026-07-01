# 三. 异步编程（Promise/async-await/ 事件循环、宏微任务，手写 Promise.all/race）

## 一、核心概念梳理

### 1. 异步编程的必要性

JS 是**单线程**语言（同一时间只能执行一个任务），同步执行耗时操作（网络请求、定时器、DOM 操作）会阻塞主线程，导致页面卡死。异步编程让耗时操作 “后台执行”，不阻塞主线程，保证页面响应性。

### 2. 事件循环（Event Loop）+ 宏 / 微任务（面试核心）

#### （1）核心执行模型

- **执行栈**：同步代码按顺序入栈执行，执行完出栈；
- **任务队列**：异步任务完成后，回调函数进入对应队列，等待执行栈清空后执行。

#### （2）宏任务 vs 微任务

表格


| 类型   | 常见任务                                                                 | 执行优先级 |
| ------ | ------------------------------------------------------------------------ | ---------- |
| 宏任务 | script 整体代码、setTimeout/setInterval、DOM 事件、AJAX、Node.js fs 操作 | 低         |
| 微任务 | Promise.then/catch/finally、async-await、queueMicrotask                  | 高         |

#### （3）事件循环执行顺序（必背）

* **同步代码优先**：script 里能立刻执行的，先全干完；
* **微任务 “插队”**：同步干完后，所有微任务一次性清完，不留给宏任务；
* **宏任务 “挨个来”**：每次只执行 1 个宏任务，执行完就回头清微任务，循环。

```
同步代码（script） → 所有微任务（一次性清完） → 1个宏任务 → 所有微任务 → 1个宏任务 → …
```

**示例分析**：

javascript

运行

```
console.log('1（同步）');

setTimeout(() => {
  console.log('2（宏任务）');
  Promise.resolve().then(() => console.log('3（微任务）'));
}, 0);

Promise.resolve().then(() => {
  console.log('4（微任务）');
  setTimeout(() => console.log('5（宏任务）'), 0);
});

console.log('6（同步）');

// 执行顺序：1 → 6 → 4 → 2 → 3 → 5
// 分析：
// 1. 同步代码执行：输出 1、6；
// 2. 微任务队列执行 Promise.then：输出 4，同时添加 setTimeout(5) 到宏任务队列；
// 3. 执行第一个宏任务 setTimeout(2)：输出 2，添加 Promise.then(3) 到微任务队列；
// 4. 执行微任务队列的 3：输出 3；
// 5. 执行下一个宏任务 5：输出 5。
```

### 3. Promise 核心

#### （1）Promise 三种状态（不可逆）

- `pending`：初始状态；
- `fulfilled`：调用 `resolve` 后，代表成功；
- `rejected`：调用 `reject`/ 抛出错误后，代表失败。

#### （2）Promise 常用方法

表格


| 方法           | 作用                                                |
| -------------- | --------------------------------------------------- |
| then           | 接收成功 / 失败回调，返回新 Promise（支持链式调用） |
| catch          | 捕获 Promise 链错误（等价于`then(null, 失败回调)`） |
| finally        | 无论成功 / 失败都会执行，不改变 Promise 状态        |
| resolve/reject | 快速创建成功 / 失败的 Promise                       |
| all/race       | 静态方法，处理多个 Promise（面试高频手写）          |

### 4. async-await（Promise 语法糖）

- `async` 修饰的函数，返回值自动包装为 Promise（即使返回普通值，也会转为 `Promise.resolve(值)`）；
- `await` 仅能在 `async` 函数中使用，“暂停” 函数执行，等待右侧 Promise 完成：
  - 成功：返回 Promise 结果；
  - 失败：抛出错误（需用 `try/catch` 捕获）；
- 本质：底层仍基于事件循环和微任务，只是写法更接近同步代码。

**示例**：

javascript

运行

```
async function asyncFn() {
  try {
    const res1 = await Promise.resolve('成功1');
    console.log(res1); // 成功1
    const res2 = await new Promise(resolve => setTimeout(() => resolve('成功2'), 1000));
    console.log(res2); // 1秒后输出“成功2”
    return '最终结果';
  } catch (err) {
    console.error(err);
  }
}
asyncFn().then(res => console.log(res)); // 最终结果
```

## 二、手写核心代码（面试高频）

### 1. 手写 Promise.all

#### （1）核心规则

- 接收可迭代对象（通常是数组），元素可为 Promise / 普通值；
- 所有 Promise 成功 → 返回成功 Promise，结果为按输入顺序排列的数组；
- 任意一个 Promise 失败 → 立即返回失败 Promise，结果为第一个失败原因；
- 空数组 → 立即返回成功 Promise（结果为空数组）。

#### （2）实现代码

javascript

运行

```
function myPromiseAll(promises) {
  return new Promise((resolve, reject) => {
    // 处理参数：转为数组（兼容所有可迭代对象）
    const promiseArr = Array.from(promises);
    // 空数组直接resolve
    if (promiseArr.length === 0) return resolve([]);

    const results = []; // 存储成功结果（保持顺序）
    let resolvedCount = 0; // 已成功的Promise数量

    promiseArr.forEach((p, index) => {
      // 处理普通值：包装为成功的Promise
      Promise.resolve(p).then(
        (res) => {
          results[index] = res;
          resolvedCount++;
          // 所有Promise都成功时，resolve结果数组
          if (resolvedCount === promiseArr.length) resolve(results);
        },
        (err) => {
          // 任意一个失败，立即reject
          reject(err);
        }
      );
    });
  });
}

// 测试用例
const p1 = Promise.resolve(1);
const p2 = new Promise(resolve => setTimeout(() => resolve(2), 1000));
const p3 = 3; // 普通值
// 成功场景：1秒后输出 [1,2,3]
myPromiseAll([p1, p2, p3]).then(res => console.log(res));

// 失败场景：立即输出“出错了”
const p4 = Promise.reject('出错了');
myPromiseAll([p1, p4, p2]).catch(err => console.log(err));
```

### 2. 手写 Promise.race

#### （1）核心规则

- 接收可迭代对象，元素可为 Promise / 普通值；
- 返回新 Promise，状态由**第一个完成**（成功 / 失败）的 Promise 决定；
- 空数组 → 返回的 Promise 永远处于 pending 状态。

#### （2）实现代码

javascript

运行

```
function myPromiseRace(promises) {
  return new Promise((resolve, reject) => {
    const promiseArr = Array.from(promises);
    // 遍历所有Promise，谁先完成就执行对应的resolve/reject
    promiseArr.forEach((p) => {
      Promise.resolve(p).then(
        (res) => resolve(res), // 第一个成功，立即resolve
        (err) => reject(err)  // 第一个失败，立即reject
      );
    });
  });
}

// 测试用例
const p1 = new Promise(resolve => setTimeout(() => resolve('慢Promise'), 1000));
const p2 = Promise.resolve('快Promise');
// 立即输出“快Promise”
myPromiseRace([p1, p2]).then(res => console.log(res));

// 失败优先：500ms后输出“失败了”
const p3 = new Promise((_, reject) => setTimeout(() => reject('失败了'), 500));
myPromiseRace([p1, p3]).catch(err => console.log(err));
```

## Promise 核心方法实战示例（附场景记忆）

以下示例贴近真实开发场景，涵盖 Promise 所有核心方法的使用场景，可直接复制到浏览器控制台运行。

```javascript
// 模拟真实开发中常见的异步场景：接口请求、超时控制、批量操作
// 1. 模拟接口请求函数（返回Promise）
function requestApi(url, delay = 1000, isSuccess = true) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      if (isSuccess) {
        resolve(`✅ ${url} 请求成功，返回数据`);
      } else {
        reject(`❌ ${url} 请求失败：网络错误`);
      }
    }, delay);
  });
}

// ---------------------- 实例方法：.then()/.catch()/.finally() ----------------------
console.log('===== 实例方法：单个接口请求处理 =====');
// 场景：单个接口请求，处理成功/失败，最后清理加载状态
requestApi('/user/info', 800)
  .then(res => console.log(res)) // 成功回调
  .catch(err => console.error(err)) // 失败回调
  .finally(() => console.log('🔚 请求结束，关闭加载弹窗\n')); // 无论成败都执行

// ---------------------- 静态方法：快速创建 Promise ----------------------
console.log('===== 静态方法：resolve/reject 快速创建 =====');
// 场景1：已知结果，快速返回成功的Promise（比如缓存数据）
const cacheData = Promise.resolve('📦 缓存数据：用户信息');
cacheData.then(res => console.log(res));

// 场景2：主动抛出失败（比如参数校验失败）
const validateError = Promise.reject('⚠️ 参数错误：手机号为空');
validateError.catch(err => console.error(err) + '\n');

// ---------------------- 静态方法：批量处理 Promise ----------------------
console.log('===== 静态方法：all/race/allSettled/any =====');
// 1. Promise.all：场景 - 多个接口都成功才渲染页面（比如同时查用户+订单）
const p1 = requestApi('/user/info', 500);
const p2 = requestApi('/user/order', 800);
Promise.all([p1, p2])
  .then(res => console.log('📊 Promise.all：所有接口成功', res))
  .catch(err => console.error('📊 Promise.all：有接口失败', err));

// 2. Promise.race：场景 - 接口超时控制（超过2秒判定超时）
// 正确：封装一个“2秒后失败”的Promise
const timeoutPromise = () => {
  return new Promise((_, reject) => {
    setTimeout(() => {
      reject('⏰ Promise.race：请求超时（2秒）'); // 真正2秒后触发失败
    }, 2000);
  });
};
// 接口请求：3秒后返回结果
const apiRequest = requestApi('/goods/list', 3000);

// Promise.race：谁先完成（成功/失败）就用谁的结果
Promise.race([apiRequest, timeoutPromise()])
  .then(res => console.log(res)) // 若接口2秒内返回，走这里
  .catch(err => console.error(err)); // 若2秒到了接口还没返回，走这里（超时）

// 3. Promise.allSettled：场景 - 批量上传文件，统计成功/失败
const p3 = requestApi('/upload/1', 600, true);
const p4 = requestApi('/upload/2', 700, false); // 模拟上传失败
Promise.allSettled([p3, p4])
  .then(res => {
    console.log('📤 Promise.allSettled：所有上传完成，统计结果');
    const success = res.filter(item => item.status === 'fulfilled').length;
    const fail = res.filter(item => item.status === 'rejected').length;
    console.log(`成功：${success}个，失败：${fail}个`);
  });

// 4. Promise.any：场景 - 多服务器容错请求（只要一个成功就用）
const server1 = requestApi('/server1', 1000, false); // 服务器1失败
const server2 = requestApi('/server2', 800, true);  // 服务器2成功
const server3 = requestApi('/server3', 1200, false); // 服务器3失败
Promise.any([server1, server2, server3])
  .then(res => console.log('🔌 Promise.any：有服务器响应成功', res))
  .catch(err => console.error('🔌 Promise.any：所有服务器都失败', err));
```

```plaintext
===== 实例方法：单个接口请求处理 =====
✅ /user/info 请求成功，返回数据
🔚 请求结束，关闭加载弹窗

===== 静态方法：resolve/reject 快速创建 =====
📦 缓存数据：用户信息
⚠️ 参数错误：手机号为空

===== 静态方法：all/race/allSettled/any =====
📊 Promise.all：所有接口成功 [ '✅ /user/info 请求成功，返回数据', '✅ /user/order 请求成功，返回数据' ]
⏰ Promise.race：请求超时（2秒）
📤 Promise.allSettled：所有上传完成，统计结果
成功：1个，失败：1个
🔌 Promise.any：有服务器响应成功 ✅ /server2 请求成功，返回数据
```

### 核心场景对应表


| 方法                   | 类型     | 核心使用场景                                   |
| ---------------------- | -------- | ---------------------------------------------- |
| `.then()`              | 实例方法 | 单个异步任务（接口 / 定时器）的成功回调        |
| `.catch()`             | 实例方法 | 单个异步任务的失败捕获（参数错误 / 网络异常）  |
| `.finally()`           | 实例方法 | 异步任务收尾操作（关闭加载弹窗 / 清理资源）    |
| `Promise.resolve()`    | 静态方法 | 快速创建成功状态的 Promise（返回缓存数据）     |
| `Promise.reject()`     | 静态方法 | 快速创建失败状态的 Promise（主动抛出校验错误） |
| `Promise.all()`        | 静态方法 | 多个任务必须全部成功（多接口数据联动渲染）     |
| `Promise.race()`       | 静态方法 | 超时控制（接口请求防卡死 / 限时操作）          |
| `Promise.allSettled()` | 静态方法 | 批量任务需统计所有结果（批量上传 / 下载统计）  |
| `Promise.any()`        | 静态方法 | 多源容错（多服务器请求 / 多 CDN 加载）         |

## 三、面试答题技巧

1. **先搭框架**：回答异步编程时，按 “JS 单线程 → 异步必要性 → 事件循环（宏 / 微任务） → Promise/async-await 解决方案” 的逻辑梳理，建立整体认知；
2. **举例分析**：讲事件循环时，举包含同步、setTimeout、Promise.then 的示例，逐行分析执行顺序，体现理解深度；
3. **手写加注释**：写 Promise.all/race 时，关键步骤加注释（如 “按输入顺序存储结果”“处理普通值”），提升代码可读性；
4. **对比优缺点**：
   - Promise 解决了回调地狱，但链式调用仍有嵌套感；
   - async-await 更接近同步写法，可读性更高，但需用 try/catch 捕获错误。

## 四、核心总结

1. 事件循环核心：同步代码先执行 → 清空微任务队列 → 执行一个宏任务 → 重复，微任务优先级高于宏任务；
2. Promise.all 核心：全成功才成功（结果按输入顺序），一失败就失败；Promise.race 核心：谁先完成就以谁的状态为准；
3. async-await 是 Promise 语法糖，await 暂停函数执行，需配合 try/catch 捕获异步错误。
