---
layout: center
transition: fade
---

# 實驗：同樣的更新，不同的代價
## React 重算整張圖 vs Signal 只動受影響節點

---
layout: two-cols
transition: slide-left
mdc: true
---

### React
```jsx {all|2|3|7|13-21|14|15|17|2|3|7|13|all}
export default function App() {
  const [count, setCount] = useState(0);
  console.log("App rendered");
  return (
    <>
      <Header />
      <Counter count={count} setCount={setCount} />
      <Footer />
    </>
  );
}

function Counter({ count, setCount }) {
  console.log("Counter rendered");
  const clickEvt = () => setCount(count + 1);
  return (
    <button onClick={clickEvt}>
      {count}
    </button>
  );
}

```

::right::

### Signal
```tsx {all|1|4|8|14-21|15|17-19|all}
const { get: count, set: setCount } = signal(0)

function App() {
  console.log("App rendered") // 只在初次 render
  return (
    <>
      <Header />
      <Counter />
      <Footer />
    </>
  )
}

function Counter() {
  console.log("Counter rendered") // 只有 count 改變時
  return (
    <button onClick={() => setCount(count() + 1)}>
      {count()}
    </button>
  )
}
```
---

### 概念對比

```mermaid
graph TD
  subgraph React
    A[State Changed] --> B[Re-render App]
    B --> C[Re-render Children]
    C --> D[VDOM Diff & Patch]
  end
```

```mermaid
graph TD
  subgraph Signal
    X[Signal Changed] --> Y[Notify Dependents]
    Y --> Z[Run Precise Effect]
    Z --> DOM[Update Target DOM Node]
  end
```

<v-clicks transition="fade"> 

- React：**Render → Diff → Commit**（整體重建，結果正確） 
- Signal：**Track → Compute → Flush**（依賴驅動，更新精準） 
- 這不是單純「效能優化」，而是**心智模型的改變**。 

</v-clicks>
 