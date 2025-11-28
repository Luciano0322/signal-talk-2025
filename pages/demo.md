---
layout: center
transition: fade
---

# 最小的實作：按照前面的概念建立 Signals 機制

---

## 基礎機制
```js
export function signal(value) {
  const get = () => value;
  const set = (nextValue) => value = nextValue;
  return { get, set };
}
// 利用基礎閉包概念暫存資料
```

---

#### 加上一組管理依賴 → Signal
```js {all|10|4-7|14|20-23|all}
// 目前正在執行的 effect 堆疊
const effectStack = [];

function subscribe(running, subscriptions) {
  subscriptions.add(running);
  running.dependencies.add(subscriptions);
}

export function signal(value) {
  const subscriptions = new Set(); // 這個 signal 被哪些 effect 依賴

  const get = () => {
    const running = effectStack[effectStack.length - 1];
    if (running) subscribe(running, subscriptions);
    return value;
  };

  const set = (nextValue) => {
    value = nextValue;
    // 通知所有依賴這個 signal 的 effect
    for (const sub of [...subscriptions]) {
      sub.execute();
    }
  };
  return { get, set };
}
```

---

#### Effect 實作
```js {all|2-7|10-18|11|12|14|16|all}
// 這裡接著 signal 往下新增
function cleanup(running) {
  for (const dep of running.dependencies) {
    dep.delete(running);
  }
  running.dependencies.clear();
}

export function effect(fn) {
  const execute = () => {
    cleanup(running);          // 清除舊的依賴
    effectStack.push(running); // 標記現在這個 effect 執行
    try {
      fn();                    // 執行過程中，所有 get() 都會把這個 running 註冊
    } finally {
      effectStack.pop();       // 執行完，把他從 stack 踢掉
    }
  };
  const running = {
    execute,
    dependencies: new Set()
  };
  running.execute();
}
```

---

### Computed / Memo 概念實作
```js
// 衍生值：由其他 signal 推導出來的 signal
export function computed(fn) {
  // 建一個內部用的 signal，專門存結果
  const { get, set } = signal();
  // 用 effect 追蹤所有依賴，當依賴變動就重算
  effect(() => set(fn()));
  // 對外只暴露 get
  return get;
}
```

---

### 使用範例

```js
const count = signal(0);

const double = computed(() => count.get() * 2);

effect(() => {
  console.log("render →", count.get(), double());
});

count.set(1);
// render → 1 2

```

---
layout: two-cols
transition: slide-left
class: mx-2
---

### Schedule 概念

```js {all|19-21|all}
const effectStack = [];

function subscribe(running, subscriptions) {
  subscriptions.add(running);
  running.dependencies.add(subscriptions);
}

export function signal(value) {
  const subscriptions = new Set();
  const get = () => {
    const running = effectStack[effectStack.length - 1];
    if (running) subscribe(running, subscriptions);
    return value;
  };

  const set = (nextValue) => {
    value = nextValue;
    for (const sub of [...subscriptions]) {
      // 這裡現在是同步跑
      sub.execute();
      // 未來可以變成： schedule(sub)
    }
  };
  return { get, set };
}
```

::right::

可以抽出來變成

<v-click class="pt-8">

```js
function schedule(runner) {
  queue.add(runner);
  // 這裡可以決定：
  // - 立刻執行（同步）
  // - 放進 microtask
  // - 放到 requestAnimationFrame
}
```

</v-click>

---
layout: center
---

## Q & A

---
layout: center
---

## 謝謝大家的參與
