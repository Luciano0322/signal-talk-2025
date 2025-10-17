---
# try also 'default' to start simple
theme: seriph
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
background: https://cover.sli.dev
# some information about your slides (markdown enabled)
title: Reactivity 小技巧大變革：掌握 Signals 就這麼簡單！
info: |
  ## Fine-graind Reactivity talk
  Presentation signal for developers.

  Learn more at [my article](https://ithelp.ithome.com.tw/users/20129020/ironman/8219)
# apply UnoCSS classes to the current slide
class: text-center
# https://sli.dev/features/drawing
drawings:
  persist: false
# slide transition: https://sli.dev/guide/animations.html#slide-transitions
transition: slide-left
# enable MDC Syntax: https://sli.dev/features/mdc
mdc: true
---

# Reactivity 小技巧大變革

## 掌握 Signals 就這麼簡單！

<div @click="$slidev.nav.next" class="mt-12 py-1" hover:bg="white op-10">
  講者: Luciano<carbon:arrow-right />
</div>

<div class="abs-br m-6 text-xl">
  <button @click="$slidev.nav.openInEditor()" title="Open in Editor" class="slidev-icon-btn">
    <carbon:edit />
  </button>
  <a href="https://github.com/slidevjs/slidev" target="_blank" class="slidev-icon-btn">
    <carbon:logo-github />
  </a>
</div>


---
src: ./pages/personal_intro.md
---

---
src: ./pages/react_assumption.md
---

---
src: ./pages/cost_of_vdom.md
---

---
src: ./pages/new_assumption.md
---

---
src: ./pages/why_signals.md
---

---
src: ./pages/scheduler.md
---

---
src: ./pages/demo.md
---

---
src: ./pages/history.md
---

---
src: ./pages/push_vs_pull.md
---

---
src: ./pages/reactivity_three_paths.md
---

---
src: ./pages/signal_core_concept.md
---

---
src: ./pages/dependency_tracking.md
---
