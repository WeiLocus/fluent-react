---
# try also 'default' to start simple
theme: seriph
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
background: https://cdn.jsdelivr.net/gh/slidevjs/slidev-covers@main/static/REjuIrs2YaM.webp
# https://cover.sli.dev

# some information about your slides (markdown enabled)
title: Fluent React - Conclusion
info: |
  ## Fluent React - Chapter 11
  深入 React 內部機制的旅程總結
# apply UnoCSS classes to the current slide
class: text-center
defaults:
  class: bg-neutral-800 text-white
# https://sli.dev/features/drawing
drawings:
  persist: false
# slide transition: https://sli.dev/guide/animations.html#slide-transitions
transition: slide-left
# enable MDC Syntax: https://sli.dev/features/mdc
mdc: true
# duration of the presentation
duration: 35min
fonts:
  sans: Robot
  serif: Robot Slab
  mono: Fira Code
---

# Fluent React Conclusion
從使用到精通的旅程


<div class="abs-br m-6 flex items-center">
  <div class="text-sm">2025-12-16</div>
  <a href="https://github.com/WeiLocus" target="_blank" class="slidev-icon-btn">
    <carbon:logo-github />
  </a>
</div>

<!--
The last comment block of each slide will be treated as slide notes. It will be visible and editable in Presenter Mode along with the slide. [Read more in the docs](https://sli.dev/guide/syntax.html#notes)
-->

---

# 從使用到理解 React

<div class="text-xl mt-12 leading-relaxed ">
學習 React 不只是掌握一種 library，更是培養 <span class="font-bold text-yellow-600">組件驅動開發、效能優化</span>，不斷適應「web 有持續改變的需求」的心智模式
</div>

---

# Our Timeline

<div class="mt-4 space-y-6">
  <div class="flex gap-6">
    <div class="flex flex-col items-center">
      <div class="w-12 h-12 rounded-full bg-blue-500 flex items-center justify-center text-xl font-bold">I</div>
      <div class="w-1 flex-1 bg-blue-500/30 mt-2"></div>
    </div>
    <div class="flex-1 pb-6">
        <div class="text-xl font-bold mb-2">基礎原則與核心概念</div>
        <div class="text-sm opacity-90 leading-relaxed">
        書中從 React 的起源開始，探索它為何存在、如何透過 JSX 這個宣告式語法讓我們用更直覺的方式描述 UI。<br />接著深入虛擬 DOM，React 以元素樹描述畫面，當狀態改變時，Fiber 調和器會配合調度器與 Render Lanes 將更新分段處理，並以批次更新的方式，只對必要的 DOM 進行變更，實現更高效的更新。
      </div>
    </div>
  </div>

  <div class="flex gap-6">
    <div class="flex flex-col items-center">
      <div class="w-12 h-12 rounded-full bg-green-500 flex items-center justify-center text-xl font-bold">II</div>
      <div class="w-1 flex-1 bg-green-500/30 mt-2"></div>
    </div>
    <div class="flex-1 pb-6">
      <div class="text-xl font-bold mb-2">底層機制與優化</div>
      <div class="text-sm opacity-90 leading-relaxed">
      探討進階模式——從高階組件(HOC)、render props 到 hooks 和 context，這些模式讓開發者能夠抽象邏輯、在組件間共享行為、更有效地管理狀態。我們也學到如何將 React 帶到伺服器端，透過 SSR 加速初始載入，並理解 hydration 的目的是讓應用程式有完整的互動功能。最後深入並行 React，掌握如何透過優先級管理讓應用保持流暢回應。
      </div>
    </div>
  </div>

  <div class="flex gap-6">
    <div class="flex flex-col items-center">
      <div class="w-12 h-12 rounded-full bg-purple-500 flex items-center justify-center text-xl font-bold">III</div>
    </div>
    <div class="flex-1">
      <div class="text-xl font-bold mb-2">框架與生態系統的進化</div>
      <div class="text-sm opacity-90 leading-relaxed">
      探討現代框架如何在 React 之上提供完整解決方案，如 Remix、Next.js，了解框架如何實施路由、資料提取、資料異動，深入 React Server Components，如何透過伺服器渲染減少 JS bundle。最後從 Vue、Solid、Qwik、Angular、Svelte 等框架中了解不同的反應性模型，以及 React 如何透過編譯器持續演進。
      </div>
    </div>
  </div>
</div>

---

# Takeaway

<div class="grid grid-cols-3 gap-2 mt-2">
  <div class="p-4 bg-neutral-700 rounded">
    <div class="text-blue-400 font-bold mb-2 tracking-wider">重新思考最佳實踐</div>
    <div class="text-sm">React 挑戰既有慣例，透過 JSX 和虛擬 DOM 徹底顛覆傳統做法</div>
  </div>
  <div class="p-4 bg-neutral-700 rounded">
    <div class="text-blue-400 font-bold mb-2 tracking-wider">了解 JSX 的工作原理</div>
    <div class="text-sm">JSX 證明了當現有工具有限制時，我們有能力創造新語言來突破框架</div>
  </div>
  <div class="p-4 bg-neutral-700 rounded">
    <div class="text-blue-400 font-bold mb-2 tracking-wider">受限並非壞事</div>
    <div class="text-sm">受限是發明之母，瀏覽器的局限性促成了 React 的創新</div>
  </div>
    <div class="p-4 bg-neutral-700 rounded ">
    <div class="text-blue-400 font-bold mb-2 tracking-wider">宣告式帶來更強大的功能</div>
    <div class="text-sm">React 將「UI 的描述（JSX）」和「UI 更新邏輯（Reconciler）」獨立，實現 "write once, run anywhere" 的 UI 開發方法，用相同的程式碼將內容渲染到 DOM、伺服器或原生平台</div>
  </div>
    <div class="p-4 bg-neutral-700 rounded">
    <div class="text-blue-400 font-bold mb-2 tracking-wider">模式演進思考</div>
    <div class="text-sm">從 HOC 、render props、hooks 到 context 都可以將邏輯抽象化，在組件之間共享行為。現在的開發模式可能都會成為下一代 UI 框架的基礎</div>
  </div>
    <div class="p-4 bg-neutral-700 rounded">
    <div class="text-blue-400 font-bold mb-2 tracking-wider">跳出瀏覽器</div>
    <div class="text-sm">伺服器渲染讓我們能在伺服器上渲染 React 組件，使用原生 fetch API 載入資料，用原生 HTML 表單處理使用者的輸入</div>
  </div>
  <div class="p-4 bg-neutral-700 rounded">
    <div class="text-blue-400 font-bold mb-2 tracking-wider">提升使用者體驗</div>
    <div class="text-sm">利用 React 並行功能，如 useTransition 能將工作延遲到"另一個宇宙"，內容變更就緒後再提交到 DOM</div>
  </div>
  <div class="p-4 bg-neutral-700 rounded">
    <div class="text-blue-400 font-bold mb-2 tracking-wider">發送最少量程式碼給使用者</div>
    <div class="text-sm">透過 bundler 將用戶端組件與伺服器組件分離，傳送更少的程式碼給使用者，提升載入速度</div>
  </div>
  <div class="p-4 bg-neutral-700 rounded">
    <div class="text-blue-400 font-bold mb-2 tracking-wider">跨框架學習與啟發</div>
    <div class="text-sm">從 Vue、Solid、Qwik 等框架中學習，每個框架都在解決相同的問題：如何打造快速、靈敏、具回應性的 UI，且具備出色的開發體驗</div>
  </div>
</div>

---

# 持續關注

<div class="grid grid-cols-2 gap-8 mt-12 text-left max-w-4xl mx-auto">

<div class="space-y-4">

### 追蹤可信來源
- React 官方文件 (react.dev)
- 核心團隊成員的社群媒體
- 社群創作者的內容

### 加入相關社群
- Reddit、Stack Overflow、GitHub、Discord

</div>

<div class="space-y-4">

### 參加活動
- React 相關會議
- 線上、線下 Meetup
- 觀看技術分享影片

### 實踐經驗
- 嘗試不同框架
- build in public : 分享作品、像法、觀點

</div>
</div>

---
layout: center
---

<style>
.typewriter-loop {
  font-size: 2.2rem;
  white-space: nowrap;
  overflow: hidden;
  animation: typing 2.2s steps(14, end) infinite alternate,
             blink .7s step-end infinite;
}

@keyframes typing {
  from { width: 0 }
  to { width: 16ch }
}

</style>

<div class="flex flex-col items-center gap-6">
  <div class="text-6xl font-bold">Thank you</div>
  <div class="typewriter-loop">🐾🐾🐾🐾🐾🐾🐾🐾🐾🐾🐾🐾🐾🐾🐾🐾 </div>
</div>
