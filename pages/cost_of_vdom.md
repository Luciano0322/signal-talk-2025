---
layout: default
transition: slide-left
---

# 假設的代價：Virtual DOM 的成本

---

- Virtual DOM 讓 UI 一致性更高，但同時：
  - 帶來額外的運算負擔  
  - 導致無法預期的效能尖峰  
  - 在大型專案中成為渲染瓶頸
- 即使有 memo / useCallback / useMemo...  
  我們仍在對抗同一個問題：**重算的浪費**

---

```mermaid
graph LR
Data -->|更新| Component
Component --> VirtualDOM
VirtualDOM -->|Diff + Patch| DOM
```
<v-click>
React 一直努力讓這條路變得更快，
但從沒改變過「需要比對」這件事。
</v-click>
