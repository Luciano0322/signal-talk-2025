---
layout: center
---

# Fine-grained Reactivity 是什麼？

---

```mermaid
graph LR
Signal[Signal 狀態變動] --> Computed[Dependency Tracking 自動觸發]
Computed --> Effect[通知依賴此狀態的特定 Effect]
Effect --> DOM[特定 UI 節點精準更新]
```

<v-clicks class="list-disc" transtion="fade">

- 每個節點知道誰依賴自己。
- 當一個 signal 改變時，只更新有關聯的 effect。
- 不需要 Diff，不需要重建樹。

</v-clicks>

<v-click>

## React 重新算「整個結果」，Signal 只更新「必要過程」。

</v-click>

<v-click>

### 一個從 UI 出發，另一個從資料出發。

</v-click>