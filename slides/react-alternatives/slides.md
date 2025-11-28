---
theme: seriph
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
# background: https://cover.sli.dev
title: React Alternatives
info: |
  ## React Alternatives
  Presentation slides for developers.

defaults:
  class: bg-neutral-800 text-white light:text-black
# https://sli.dev/features/drawing
drawings:
  persist: false
# slide transition: https://sli.dev/guide/animations.html#slide-transitions
transition: slide-left
# enable MDC Syntax: https://sli.dev/features/mdc
mdc: true
fonts:
  # basically the text
  sans: Robot
  # use with `font-serif` css class from UnoCSS
  serif: Robot Slab
  # for code blocks, inline code, etc.
  mono: Fira Code

# duration of the presentation
# duration: 35min
---

# React Alternatives

探索現代前端框架的反應性系統

<div class="abs-br m-6 flex items-center">
  <div class="text-sm">2025-12-09</div>
  <a href="https://github.com/WeiLocus" target="_blank" class="slidev-icon-btn">
    <carbon:logo-github />
  </a>
</div>

---

# 本章大綱


<div class="p-4 rounded-2xl border my-6">
  Part 1: 介紹各框架反應性
</div>
<div class="p-4 rounded-2xl border mb-6">
  Part 2: 比較粗回應性和細回應性
</div>
<div class="p-4 rounded-2xl border mb-6">
  Part 3: React 的未來
</div>
<div class="p-4 rounded-2xl border">
  Part 4: 框架選擇與總結
</div>

---
layout: section
class: text-center
---

# Part 1: 

# Framework Reactivity Systems
探索各框架如何實現狀態驅動 UI 更新

<div class="flex justify-center gap-6 pt-10">
<img src="/vue.svg" />
<img src="/angular.svg" />
<img src="/svelte.svg" />
<img src="/solid.svg" />
<img src="/qwik.svg" />
</div>

---

# Vue.js - Proxy-Based Reactivity

<div>

**Vue 2**: `getter` / `setter`

**Vue 3**: `reactive` 函式使用 `Proxy` 攔截物件的 get/set 操作， `ref` 函式使用 `getter` / `setter`

<div class="mt-8"/>

Vue 3 核心概念

<span v-mark.underline.orange>`reactive()` - 處理物件型別資料的響應性</span>

接收一個 JavaScript 物件，並返回該物件的 Proxy 代理物件。


<span v-mark.underline.orange>`ref()` -處理基本型別資料的響應性，也可以接受物件型別</span>

將一個值（如數字或字串）包裝在一個具有 `.value` 屬性的物件中。

</div>

<div class="absolute bottom-3 right-3">
<img src="/vue.svg" width="20" height="20" />
</div>

---

<div>

`reactive`
<p class="pl-8 text-sm">

讀取屬性：內部呼叫 `track(target, key)`，「追蹤」當前執行的副作用（effect）與資料屬性的依賴關係。  
修改屬性： 內部呼叫 `trigger(target, key)`，「觸發」與該屬性相關的副作用函式（effect）進行更新。

</p>

`ref`
<p class="pl-8 text-sm"> 

讀取： 呼叫 `track(refObject, 'value')`，追蹤該 ref 與副作用函式的依賴<br />
修改： 呼叫 trigger(refObject, 'value')，觸發相依於該 ref 的副作用函式重新執行

</p>
</div>

<div class="grid grid-cols-2 gap-6 mt-2">
<div> 

```js [reactive - proxy] 
function reactive(obj) {
  return new Proxy(obj,{
    get(target, key) {
      track(target, key)  // 紀錄相依的邏輯和資料
      return target[key]
    },
    set(target, key, value) {
      target[key] = value
      trigger(target, key) // 執行相依邏輯和更新資料
    }
  })
}

```

</div>
<div>

```js [ref - getter + setter]
function ref(value) {
  const refObject = {
    get value() {
      track(refObject, 'value') // 紀錄相依的邏輯和資料
      return value
    },
    set value(newValue) {
      value = newValue
      trigger(refObject, 'value') // 執行相依邏輯和更新資料
    }
  }
  return refObject
}
```
</div>

</div>

<div class="absolute bottom-3 right-3">
<img src="/vue.svg" width="20" height="20" />
</div>

<!--
無論是 reactive 還是 ref，它們都圍繞著兩個內部功能展開：「追蹤 (Track)」和「觸發 (Trigger)」。
-->

---

# Vue.js - 範例

<div class="grid grid-cols-2 gap-6">
<div>

使用 `count.value` 取得 或 修改值

```js
import { ref, watchEffect } from "vue";

const count = ref(0);

// 自動追蹤依賴並更新
watchEffect(() => {
  document.body.innerHTML = 
    `count is: ${count.value}`;
});

// 修改值自動觸發更新
count.value++;
```

</div>
<div>

`ref()`、`computed()` 和 `watchEffect()` API<br />都是 Vue 的 Composition API 一部分

許多其他框架都引入與 Vue 的 Composition API 的 refs 類似的回應式基本元素，<span v-mark.underline.orange>統稱為「signal」</span>

</div>

</div>

<div class="absolute bottom-3 right-3">
<img src="/vue.svg" width="20" height="20" />
</div>

---

# 什麼是 Signals?
Signals 是一種簡單的響應式基本元素 (Reactive Primitive)，它們本質上就像是可變的變數

<div class="grid grid-cols-2 gap-6">

<div>

- 類似 Vue 的 refs 的 回應式基本元素(Reactivity Primitive)
- 提供依賴追蹤的「值容器」
- 細回應性

</div>

<div>

```javascript [Angular example]

const count = signal(0);  // 創建 signal

const value = count();    // 取得值

count.set(1);             // 設定新值


```

</div>

</div>

**核心概念**:

一個 Signal 由三個核心部分組成
- 值 (value)： 儲存狀態。
- 讀取器 (getter)： 用於讀取當前值，並在執行時自動且隱式地註冊正在執行的函式（即觀察者/訂閱者）作為其依賴關係。
- 修改器 (setter)： 用於修改值，並在值改變時通知所有已訂閱的反應 (Reactions/Effects)。

<!--
Signals是反應系統中最重要的部分。它們由一個 getter、setter 和一個value組成

雖然在學術論文中或 SolidJS 中它們被稱為 Signals，但在其他採用 FGR 概念的函式庫中，它們有許多別名：

Observables：例如 KnockoutJS。
Atoms / Subjects：在其他響應式系統中。
Refs：例如 Vue 的 ref()，它在概念上與 Signals 屬於同一類型的響應式基礎元件
-->

---

# Signals 的演進

### 時間軸
<div class="mt-8 grid grid-cols-4 gap-4">
  <div class="p-4 bg-gray-500 bg-opacity-10 rounded text-center">
    <div class="pb-2 border-b border-gray-200">2010</div>
    <div class="mt-2">
      <div>Knockout Observables</div>
      <span class="text-xs">(最早的響應式 JS 函式庫)</span>
      <div class="mt-4">Meteor Tracker</div>
      <span class="text-xs">(訊號/細粒度響應性的概念)</span>
    </div>
  </div>
  <div class="p-4 bg-purple-500 bg-opacity-10 rounded text-center">
    <div class="pb-2 border-b border-gray-200">2010s</div> 
    <div class="mt-2">
      <div>Vue Options API</div>
      <div>MobX</div>
      <span class="text-xs">(React 狀態管理程式庫)</span>
    </div>
  </div>
  <div class="p-4 bg-blue-500 bg-opacity-10 rounded text-center">
    <div class="pb-2 border-b border-gray-200">2021</div> 
    <div class="mt-2">
      <div>Solid </div>
      <div>createSignal</div>
      <span class="text-xs">(將細回應性系統與 類似 React 的 JSX 模型 結合)</span>
    </div>
  </div>
  <div class="p-4 bg-green-500 bg-opacity-10 rounded text-center">
    <div class="pb-2 border-b border-gray-200">現代</div> 
    <div class="mt-2">
      <div>Angular Signals</div>
      <div>Svelte 5 Runes </div>
    </div>
  </div>
</div>


Ryan Carniato (Solid 作者) 將 2010 年的概念帶回現代，改變整個前端生態系

---

# Angular - Change Detection
由 Google 開發和維護


核心概念：變更檢測系統

檢查應用程式的狀態是否改變，是否需要更新 DOM

- 每個組件都有變更檢測器，負責使用 **Zone.js** 檢查 view 的變化
- 每次偵測到「可能有變化」時，Angular 會從上到下遍歷組件，尋找變更
- 高度優化且可配置

Angular 16: 放棄 dirty-checking，引入 Signal API

<div class="absolute bottom-3 right-3">
  <img src="/angular.svg" width="20" height="20"/>
</div>

<!--
在 Angular 18 中，Signals 已經進入「穩定（stable）」階段。
-->

---

# Angular - Signal 範例


```js
const count = signal(0);    // Signals API

count();                    // 取得值
count.set(1);               // 設定新值
count.update((v) => v + 1); // 根據之前的值進行更新

const state = signal({ count: 0 });
state.mutate((o) => {
  o.count++;
});
```

和 Vue Refs 相比
- `()` 比 `.value` 簡潔，但更新值的寫法較冗長
- 無 ref-unwrapping （參考解包），vue 的狀態值在 template 中可以直接寫`<p>{{ count }}</p>`，而不是 `count.value`，angular一定透過 `()` 取得值

<div class="absolute bottom-3 right-3">
  <img src="/angular.svg" width="20" height="20"/>
</div>

---

# Svelte
Svelte 4 - compile-time reactivity

<div class="grid grid-cols-2 gap-6">

<div>

### 核心概念

- Svelte 是一種編譯器
- 無 Virtual DOM
- 產生程式碼直接更新 DOM
- 編譯期回應性，所有「響應式行為」都是靠 編譯器在編譯階段靜態分析

## Reactive Statements
使用 let、=、export 關鍵字和 $: 標籤

</div>

<div>

````md magic-move
```js
<script>
let count = 0;

function increment() {
  count += 1;  // 自動更新 DOM
}
</script>

<div>{count}</div>
<button on:click={increment}>
  Click me
</button>
```
```js {3-5|12}
<script>
let count = 0;
let doubleCount = 0;

$: doubleCount = count * 2;

function increment() {
  count += 1;  // 自動更新 DOM
}
</script>

<div>{doubleCount}</div>
<button on:click={increment}>
  Click me
</button>
```
````

<div class="mt-4 p-4 rounded-xl bg-yellow-200 dark:bg-yellow-900/40">
在 Svelte 中這種回應性是自動的，不需要呼叫 setter 函式或使用任何特殊的 API 來更新  
</div>

</div>
</div>

<div class="absolute bottom-3 right-3 flex gap-4">
<a href="https://www.youtube.com/watch?v=rv3Yq-B8qp4">Svelte in 100 Seconds</a>
<img src="/svelte.svg" width="20" height="20"/>
</div>
<div class="absolute bottom-3 right-3">
</div>

<!--
{count} 語法會在 count 改變時自動更新，看起來看 React 很像

$counter 是一個 語法糖 (syntax sugar)，代表 get(counter)，同時自動訂閱與解除訂閱。
-->

---

# Svelte
Svelte 5 - Runes

<div class="grid grid-cols-2 gap-6">

<div>

**Svelte 4 問題**:
- 捷思法(heuristic) 僅適用於組件頂層的 let 宣告
- 重構困難，程式碼在 `.svelte` 檔案內（具備反應性）與在 `.js` 或 `.ts` 檔案內（缺乏反應性）的行為不同
- 將狀態邏輯封裝並在多個組件間共享，必須使用 Store API，這種方式要求邏輯必須遵循嚴格的 Store 合約，處理複雜的狀態邏輯不好用

</div>

<div>

**Runes 是 Svelte 5 的新響應式系統**:
- 使用函式語法來實現反應性
- 反應性由 signal 驅動
- 取代 heuristic，明確的 reactivity 標記，使用 `$state()` 符號宣告哪些值具有反應性
- 讓響應式邏輯能在 `.svelte`、`.js`、或 `.ts` 檔案中以一致的方式運作

```js
let count = $state(0); // 反應性狀態

let doubled = $derived(count * 2); // 計算值

// 狀態變化時執行副作用
$effect(() => {
  console.log(count);
});
```

</div>

</div>

<div class="absolute bottom-3 right-3">
<img src="/svelte.svg" width="20" height="20"/>
</div>

---

# Svelte 5 - 跨檔案 Reactivity

<div class="grid grid-cols-2 gap-4">

<div>

Svelte 4 (Stores)
```js
// counter.js
import { writable } from "svelte/store";

export function createCounter() {
  const { subscribe, update } = writable(0); 
  return {
    subscribe,
    increment: () => update((n) => n + 1)
  };
}
```
```html
<script>
import { createCounter } from './counter.js';
  const counter = createCounter();
</script>

<button on:click={counter.increment}>
  clicks: {$counter}
</button>
```

</div>

<div>

Svelte 5 (Runes)
```js
// counter.js
export function createCounter() {
  let count = $state(0); // 標記 count 是 reactive data
  return {
    get count() { return count },
    increment: () => count += 1
  };
}
```
```html
<script>
import { createCounter } from './counter.js';
  const counter = createCounter();
</script>

<button on:click={counter.increment}>
  clicks: {counter.count}
</button>
```

</div>

</div>

<div class="absolute bottom-3 right-3">
<img src="/svelte.svg" width="20" height="20"/>
</div>

---

# Svelte 5 - Runtime Reactivity

<div class="grid grid-cols-2 gap-6">

<div>

Svelte 4 的問題
```js
export let width;
export let height;

// ✅ $: area = width * height;

const multiplyByHeight = 
  (width) => width * height;

// ⚠️ 只看到 width，height 改變時不會重算！
$: area = multiplyByHeight(width);
```

**編譯時**依賴追蹤限制:
- 用「函式封裝」或「間接引用」，編譯器就看不到那些依賴。不知道函式 `multiplyByHeight()` 用了 height。
- 重構困難

</div>

<div>

Svelte 5 的解決方案
```js
let { width, height } = $props();

// ✅ runtime reactivity
const area = $derived(width * height);

$effect(() => {
  console.log(area);
});
```

**執行期**依賴追蹤:
- 透過 runtime signals 自動追蹤所有依賴
- 容易重構

</div>

</div>

<div class="absolute bottom-3 right-3">
<img src="/svelte.svg" width="20" height="20"/>
</div>

---

# Solid - Fine-Grained Reactivity
大小只有 7kb

<div class="grid grid-cols-2 gap-6">

<div>

### 核心概念
- 沒有 Virtual DOM
- 基於反應式基本元素(reactive primitives) <br />反應式系統中，最基本、最小的可追蹤單位
- 使用細回應性系統自動追蹤依賴項目，直接更新 DOM
- **Signals** 是反應式系統中的核心元素

建立回應性
```js
const [count, setCount] = createSignal(0);
//     ^ getter  ^ setter
count();      // 去得當下的count值
setCount(1);  // 更新count值
```

</div>
<div>
<img src="/solid-performance.png">
</div>
</div>

<div class="absolute bottom-3 right-3 flex gap-4">
<a href="https://www.youtube.com/watch?v=hw3Bx5vxKl0">Solid in 100 Seconds</a>
<img src="/solid.svg" width="20" height="20"/>
</div>

---

# Solid - Fine-Grained Reactivity

<div class="grid grid-cols-2 gap-6">

<div >

```js
function Counter() {
  const [count, setCount] = createSignal(0);
  const increment = () => setCount((prev) => prev + 1);
  return (
    <div>
      <span>Count: {count()}</span>
      {/* Only `count()` is updated when the button is clicked. */}
      <button type="button" onClick={increment}>
        Increment
      </button>
    </div>
  );
}
```

- `createSignal` 建立回應性基本元素
- `setCount` 被呼叫時，觸發依賴 count 的任何部分的更新，不需要重新呼叫 Counter 組件

</div>

<div>

<img src="https://app.eraser.io/workspace/maDvFw5OryuPJOwSLyK9/preview?elements=cry9JT4nroFQ4rRxzOpvCg&type=embed" />


**Solid 與 React**差異：
- Component function 只執行一次
- `count()` 會在 `setCount` 被呼叫時改變，稱為細回應性，與 React 粗回應性不同
- 細回應性可以把沒必要的更新減到最少，避免差異比對的步驟
</div>

</div>

<div class="absolute bottom-3 right-3">
<img src="/solid.svg" width="20" height="20"/>
</div>

---

# Qwik - Resumability & O(1) framework
resumability 和 優先載入，確保最重要的組件盡快發揮效用

<div class="grid grid-cols-3 gap-6">

<div class="col-span-1">

### 核心概念

**Resumability (可恢復性)**

- Server 序列化初始狀態
- Client 立即可互動
- 按需載入 components

**O(1) Framework**
- 初始載入恆定 (~1 kB)
- 不隨應用大小增長
- prefetching，lazy-loaded，components 需要時才解析

</div>

<div class="col-span-2 mt-10">

<img src="https://cdn.builder.io/api/v1/image/assets%2FYJIGb4i01jvw0SRdL5Bt%2F04681212764f4025b2b5f5c6a258ad6e?format=webp&width=945" alt="Hydration VS. Resumability"/>
<a href="https://qwik.dev/docs/concepts/resumable/">
<span class="text-gray-500">Hydration VS. Resumability</span>
</a>

</div>

</div>


<div class="absolute bottom-3 right-3">
<img src="/qwik.svg" width="20" height="20"/>
</div>

---

# Hydration VS. Resumability (Qwik)

### Hydration：重播 (Replayable)
Server → HTML → JS 下載 → 重播 → 可互動

Hydration 必須完成三個核心任務，才能使應用程式可互動
<div class="p-4 bg-blue-200 dark:bg-blue-500 dark:bg-opacity-10 rounded">

-  **Listeners**：重新定位並綁定事件監聽器  
-  **Component Tree**：重建元件樹結構  
-  **Application State**：恢復應用狀態 (store / data)
</div>


1️⃣ 下載所有 JavaScript Bundle <br />
2️⃣ 程式碼必須在瀏覽器中再次執行，以重建組件結構和確定監聽器的確切位置
<div class="mt-3 font-bold text-orange-700 text-lg">
⚠️ 較長的初始化時間與「不可互動期 (uncanny valley)」

</div>

<div class="absolute bottom-3 right-3">
<img src="/qwik.svg" width="20" height="20"/>
</div>

---

# Hydration VS. Resumability (Qwik)

### Resumability：恢復 (Resumable)

將應用程式的執行視為一個可以暫停和恢復的單一流程 

- Qwik 會將應用程式運行的所有必要資訊（包括State、Listeners，以及組件在 DOM 中的結構邊界 Component Boundaries）完整序列化
- **HTML 直接包含恢復資訊** ，不需要執行任何組件邏輯來重建
- **Qwikloader** 設置單一全域事件監聽器，當事件發生時，Qwikloader 透過解析 HTML 中序列化的資訊
  ```js
  <button on:click="./chunk.js#handler_symbol">click me</button>
  ```

  - **Lazy-load**： 對應的觸發事件程式碼區塊才會被按需載入、解析和執行
- 序列化包含了組件邊界和狀態的依賴關係，Qwik 能夠做到任何組件都可以獨立恢復，而不需要依賴其父組件的程式碼在用戶端被載入或執行

<div class="mt-3 pl-6 font-bold text-green-700 text-lg">
即時可互動 + 按需載入 +  O(1) 啟動
</div>

<div class="absolute bottom-3 right-3">
<img src="/qwik.svg" width="20" height="20"/>
</div>

<!--
(例如 `on:click="./chunk.js#handler_symbol"`)，精確知道應該載入哪個程式碼區塊 (chunk) 和執行哪個符號 (symbol) 來響應事件
-->

---

# 都是序列化，和 RSC 有什麼不同？

<div class="text-md">

| 項目          | Resumability（Qwik）  | React Server Components（RSC）   |
| ------------ | ---------------------- | ------------------------------- |
| 序列化的目標  |  將應用邏輯、事件監聽器、組件邊界與所有狀態序列化至 HTML，讓用戶端「知道如何執行下一步」。  |  序列化 Server Component 的渲染結果與 Client Component 的初始 Props，讓用戶端「知道要渲染什麼」與「元件的輸入是什麼」。 |
| 互動性載入  | 使用 Qwikloader 設置單一全域事件監聽器。<br />事件處理器以 QRL 形式序列化於 DOM 屬性中<br />（例如 `on:click="./chunk.js#handler_symbol"`），<br />僅在使用者互動時才 按需載入 (Lazy-load) 對應程式碼。 | 互動性元件需標記為 `"use client"`，其 JavaScript 通常在頁面載入時下載並執行，進行水合以附加事件處理器。  |
| 元件依賴性  | 組件邊界資訊被序列化，使任何組件可獨立恢復，<br />不需父組件程式碼在用戶端載入或執行。  | Client Component (CC) 必須由 Server Component (SC) 渲染並傳遞 Props。SC 不在 Bundle 中，但 CC 仍需水合，且其 Props 依賴 SC 的輸出。  |

</div>


<div class="absolute bottom-3 right-3">
<img src="/qwik.svg" width="20" height="20"/>
</div>

---

# Common Patterns
共同目標: 提供完整的開發體驗
<div class="space-y-2">

<div class="px-4 py-3 rounded-2xl bg-blue-100 dark:bg-blue-900/30 flex gap-30 items-center">
  <div class="font-bold w-38">基於組件的架構</div>
  <ul class="text-[16px]">
    <li>將 UI 拆解為獨立組件，封裝狀態與邏輯</li>
    <li>促進程式碼重用、關注點分離與維護性</li>
  </ul>
</div>

<div class="px-4 py-3 rounded-xl bg-blue-100 dark:bg-blue-900/30 flex gap-30 items-center">
<div class="font-bold w-38">宣告式語法</div>
  <ul class="text-[16px]">
    <li>各個框架用自己的模板語言 撰寫宣告性 UI，框架負責更新 UI 以配合該狀態</li>
  </ul>
</div>

<div class="px-4 py-3 rounded-xl bg-blue-100 dark:bg-blue-900/30 flex gap-30 items-center">
  <div class="font-bold w-38">狀態更新</div>
  <ul class="text-[16px]">
    <li>虛擬 DOM 差異比對 (React、Vue)</li>
    <li>組件編譯成命令式程式碼 (Svelte)</li>
    <li>Signals 成為主流趨勢</li>
  </ul>
</div>

<div class="px-4 py-3 rounded-xl bg-blue-100 dark:bg-blue-900/30 flex gap-30 items-center">
  <div class="font-bold w-46">
    元件生命週期方法<br />生態系與工具鏈
  </div>
  <ul class="text-[16px]">
    <li>用生命週期方法或 hook 來處理組件創建、更新、銷毀階段，執行副作用與資源清理</li>
    <li> 支援現代 JavaScript/TypeScript 和工具，包括ES6語法、模組和 Webpack、Babel 等建置工具</li>
  </ul>
</div>

</div>

---
layout: section
class: text-center
---

# Part 2
# Fine-Grained VS. Coarse-Grained

深入理解兩種 Reactivity 模型的本質差異

---

# 更新的「粒度」- 局部 vs 整體

<div class="grid grid-cols-2 gap-8 mt-8">
  <div class="p-5 rounded-2xl bg-orange-100 dark:bg-orange-900/30 border border-orange-200 dark:border-orange-700">
    <h2 class="text-lg font-bold text-orange-800 dark:text-orange-300">Fine-Grained Reactivity</h2>
    <div class="mt-4 space-y-3 text-gray-700 dark:text-gray-300 leading-relaxed">
      <p><strong class="text-orange-700 dark:text-orange-400">狀態改變時：</strong></p>
      <ul class="list-disc">
        <li>只更新確切<span v-mark.underline.orange>需要變化的 UI 部分</span></li>
        <li>Component function 不重新執行</li>
        <li>精確的訂閱機制</li>
      </ul>
      <p><strong class="text-orange-700 dark:text-orange-400">框架：</strong></p>
      <ul class="list-disc list-inside">
        <li>Vue</li>
        <li>Svelte</li>
        <li>Solid</li>
        <li>Signals 系統</li>
      </ul>
    </div>
  </div>

  <div class="p-5 rounded-2xl bg-blue-100 dark:bg-blue-900/30 border border-blue-200 dark:border-blue-700">
    <h2 class="text-lg font-bold text-blue-800 dark:text-blue-300">Coarse-Grained Reactivity</h2>
    <div class="mt-4 space-y-3 text-gray-700 dark:text-gray-300 leading-relaxed">
      <p><strong class="text-blue-700 dark:text-blue-400">狀態改變時：</strong></p>
      <ul class="list-disc">
        <li><span v-mark.underline.blue>重新執行整個 component function</span></li>
        <li>透過 diffing 決定實際 DOM 更新</li>
        <li>更廣泛的更新範圍</li>
      </ul>
      <p><strong class="text-blue-700 dark:text-blue-400">框架：</strong></p>
      <ul class="list-disc list-inside">
        <li>React</li>
      </ul>
    </div>
  </div>
</div>

<!--
傳統的回應性系統，當程式碼執行時，計算之間的依賴關係會被「自動跟蹤」。
當回應性依賴項目改變，依賴他的所有計算都會自動重新執行以及反映這個變化

React 以宣告的方式建構 UI，React 會處理如何實現
React 不會自動追蹤依賴項目和傳播變更，引入一種更明確的狀態更新機制，當狀態改變時不會立刻算繪更新，而是安排重新算繪，並在重新算繪期間，使用新狀態再次運行整個組件
-->

---

# React 為什麼是粗回應性？

React 的核心公式

<div class="mt-16">

<div class="text-center text-6xl font-bold mb-10">

v = f(s)

</div>

<div class="text-center text-3xl mb-8">

View = Function of State

</div>

<div class="mt-12 p-2 bg-green-100 dark:bg-green-900/40 bg-opacity-10 rounded-xl max-w-2xl mx-auto">

<div class="px-6">

**重點**:

View 等於 狀態的函式，狀態改變時，「重新執行函數」來套用任何必要的更新，而不是直接更新值

經過虛擬 DOM 差異比對和調和程序，這種明確設置狀態與重新算繪的模式，比回應性自動傳播變更「更容易預測」

</div>
</div>

</div>

---

# Counter 範例 - React (Coarse-Grained)

<div class="grid grid-cols-2 gap-6">

<div>

```js {all|4|6-8|12|all}
export default function Counter () {
  const [count, setCount] = useState(0);
  
  function increment() {
    setCount(count + 1);
  }
  
  return (
    <div>
      <p>{count}</p>
      <button onClick={increment}> + </button>
    </div>
  );
}
```

</div>

<div>

## 執行流程

<div class="space-y-3 mt-4">

1. `setCount` 被呼叫
2. 整個 `Counter` function **重新執行**
3. 包括 `useState` hook 也重新執行
4. 產生新的 Virtual DOM
5. Diff 並更新實際 DOM

</div>

<div class="mt-6 p-4 bg-green-100 dark:bg-green-900/40 bg-opacity-10 rounded-xl">

`count` 本身不是 reactive <br />
是 component 重新執行時產生的新值

</div>

</div>

</div>

---

# Counter 範例 - Solid (Fine-Grained)

<div class="grid grid-cols-2 gap-6">

<div>

```js {all|4|6-8|12}
import { createSignal } from "solid-js";

function Counter() {
  const [count, setCount] = createSignal(0);
  
  function increment() {
    setCount(count() + 1);
  }
  
  return (
    <div>
      <p>{count}</p>
      <button onClick={increment}> + </button>
    </div>
  );
}
```

</div>

<div>

## 執行流程

<div class="space-y-3 mt-4">

1. `setCount` 被呼叫
2. `Counter` function **不會重新執行**
3. `{count()}` 訂閱了 count signal
4. 只有 `<p>` 內文字節點更新
5. 無 Virtual DOM，無 diffing

</div>

<div class="mt-6 p-4 bg-green-100 dark:bg-green-900/40 bg-opacity-10 rounded-xl">

`count` 本身是 reactive  
變化自動傳播到訂閱者

</div>

</div>

</div>

---

# Dependent Values 比較 - React & Svelte 

<div class="grid grid-cols-2 gap-6">

<div>

```js [React] {all|5|10-12}
function ItemList() {
  const [items, setItems] = useState([
    "Apple", "Banana", "Cherry"
  ]);
  const count = items.length;
  return (
    <div>
      <p>{count} items:</p>
      <ul>
        {items.map((item) => (
          <li key="item">{item}</li>
        ))}
      </ul>
    </div>
  );
}
```
 
`setItems`被呼叫 → 整個 `ItemList` **重新執行** ，`count` 也被重新計算

</div>

<div>

```html [Svelte] {all|5}
<script>
let items = ['Apple', 'Banana', 'Cherry'];

// 自動追蹤依賴
$: count = items.length;
</script>

<p>{count} items:</p>
<ul>
  {#each items as item (item)}
    <li>{item}</li>
  {/each}
</ul>
```
 
`items` 改變 → `count` **自動重新計算**  
不需要重新執行 component

</div>

</div>

---
layout: section
class: text-center
---

# Part 3: React 的未來

React Forget 與 Signals

---

<div class="w-[80%] mx-auto p-4 bg-gradient-to-r from-blue-500 to-purple-500 bg-opacity-10 rounded-lg">

<div class="text-center text-xl">

不久的將來，基本上只有 React 使用 vDOM，

其他框架都將使用各種類型的 Signal

</div>

</div>

<div class="mt-8">

<v-clicks>
<div class="mb-8 text-xl text-center">為什麽 React 不採用 Signal ?</div>

<div class="w-[80%] mx-auto p-6 bg-gradient-to-r from-indigo-500 to-blue-500 bg-opacity-10 rounded-lg">

<div class="text-2xl font-bold mb-4 text-center">
<p>React 價值主張</p>
<p>「以宣告的方式來描述你的 UI，讓 React 去處理其他事項」</p>
</div>

<div class="text-center text-lg">

React 應該自動找出算繪 UI 的最佳方式<br />
開發者不需要思考 signal、memo 或任何優化細節

</div>

</div>
</v-clicks>

</div>

---

# React 處理昂貴的計算
粗回應性在遇到狀態變更時，整個組件會重新渲染，使 React 效能低於他該有的水準
<div class="grid grid-cols-2 gap-6">

<div>

```js {all}
import { ComponentWithExpensiveChildren } from "./ExpensiveComponent";

export default function Counter() {
  const [count, setCount] = useState(0);
  
  function increment() {
    setCount(count + 1);
  }
  
  return (
    <div>
      <p>{count}</p>
      <button onClick={increment}> + </button>
      <ComponentWithExpensiveChildren />
    </div> 
  );
}
```

</div>

<div>


<div class="space-y-4 mt-4">

**點擊按鈕時**:

1. `Counter` 重新執行
2. 即使它的 props/state 沒變，`<ComponentWithExpensiveChildren />`組件也被重新渲染

</div>

</div>

</div>

---

# React 的解決方案 - memo
在正確的時間和位置加入 memo，可以提供與 signal 相同的細回應性

<div class="grid grid-cols-2 gap-6">

<div>

```js {all|18-20|13}
import { ComponentWithExpensiveChildren } from "./ExpensiveComponent";

export default function Counter() {
  const [count, setCount] = useState(0);
  
  function increment() {
    setCount(count + 1);
  }
  return (
    <div>
      <p>{count}</p>
      <button onClick={increment}> + </button>
      <MemoizedComponentWithExpensiveChildren />
    </div>
  );
}

const MemoizedComponentWithExpensiveChildren = memo(
  ComponentWithExpensiveChildren
);
```

</div>

<div>

## memo 的作用

<div class="mt-4 space-y-4">

**效果**:
- Props 沒變就不 rerender
- 達到類似 Fine-Grained 的效果

**但是**:
- 需要**手動加 `memo`**

</div>
</div>
</div>

---

# React Forget，現稱為 React Compiler
React 19 is not the React Compiler


<div>

React Compiler 的主要目標是自動化記憶化，從而消除開發人員手動使用 `useMemo`、`useCallback` 和 `React.memo` 這些 Hooks 的需求

例如：不再需要記憶化 `<ComponentWithExpensiveChildren />`組件

Compiler 會自動判斷哪些計算結果或函式引用需要被快取，實現 細粒度的性能優化

</div>

<div class="absolute bottom-3 right-3 text-sm">
<a href="https://www.developerway.com/posts/react-compiler-soon">React Compiler & React 19 - forget about memoization soon?</a>
</div>

---

# React 規則 ⟹ Forget 優化
React 規則讓 React Forget 知道，如何為我們記憶化

<div class="grid grid-cols-2 gap-6 mt-2">

<div class="px-5 py-3 rounded-2xl bg-blue-100 dark:bg-blue-900/30 border border-blue-200 dark:border-blue-700">
<div class="text-2xl font-bold text-blue-800 dark:text-blue-300 mb-4">React 規則</div>

<div class="text-gray-700 dark:text-gray-300 text-sm">

<div>
<p class="text-lg font-bold text-blue-700 dark:text-blue-400">規則 1: 組件是純函式</p>
<p class="text-md pl-12">相同輸入 → 相同輸出</p>
</div>
<div>
<p class="text-lg font-bold text-blue-700 dark:text-blue-400">規則 2: Hooks 和事件處理可以不純</p>
<p class="text-md pl-12">useEffect、事件處理允許副作用</p>
</div>
<div>
<p class="text-lg font-bold text-blue-700 dark:text-blue-400">規則 3: 純函式中禁止</p>
<p class="text-md pl-12">修改外部變數、讀取可能改變的屬性</p>
</div>
<div>
<p class="text-lg font-bold text-blue-700 dark:text-blue-400">規則 4: 純函式中允許</p>
<p class="text-md pl-12">讀取 props 或 state、修改新建的物件</p>
</div>
<div>
<p class="text-lg font-bold text-blue-700 dark:text-blue-400">規則 5: 延遲初始化例外</p>
<p class="text-md pl-12">允許以初始化為目的的變異</p>
</div>

<div>
<p class="text-lg font-bold text-blue-700 dark:text-blue-400">規則 6: Render 後不可變</p>
<p class="text-md pl-12">建立的物件在 render 完成後不應變異</p>
</div>

</div>
</div>

<div class="px-5 py-3 rounded-2xl bg-green-100 dark:bg-green-900/30 border border-green-200 dark:border-green-700">
<div class="text-2xl font-bold text-green-800 dark:text-green-300 mb-4">Forget 如何利用規則</div>

<div class="space-y-3 text-gray-700 dark:text-gray-300 text-md">

<p><strong class="text-lg text-green-700 dark:text-green-400">規則賦予的能力：</strong></p>
<ul class="list-disc list-inside">
<li>規則 1 → 知道何時結果相同，可快取</li>
<li>規則 2 → 區分純/不純區域</li>
<li>規則 3 → 確保無隱藏依賴</li>
<li>規則 4 → 確保快取安全</li>
<li>規則 5 → 識別初始化模式</li>
<li>規則 6 → 確保結果可重用</li>
</ul>

</div>
</div>

</div>

<style>
.slidev-layout p {
  margin: 4px;
}
</style>

---
layout: section
class: text-center
---

# Part 4: 總結與框架選擇

做出適合的技術決策

---

# Reactivity Systems 總覽
 React 用 vDOM，其他框架都往 Signals 演進

<div class="text-[16px]">

| Framework | Reactivity 類型 | 底層機制 | 更新方式 | 特色 |
|-----------|----------------|----------|----------|------|
| **React** | 粗回應性 | Virtual DOM + Reconciliation | Component 重新執行 | 生態系大 |
| **Vue** | 細回應性 | Proxy (v3) / Getter-Setter (v2) | 自動追蹤依賴 | 簡單易學 |
| **Angular** | 變更檢測 → Signals | Zone.js → Signals | 檢測變化 / Signals | 完整框架 |
| **Svelte** | 細回應性 | Compiler → Signals (v5) | 編譯優化 / Runtime | 最少程式碼 |
| **Solid** | 細回應性 | Signals | 訂閱更新 | 極致效能 |
| **Qwik** | 細回應性 | Signals + Resumability | 按需載入 | O(1) 載入 |

</div>

---

# 框架選擇指南
沒有「最好」的框架，只有「最適合」的框架

<div class="grid grid-cols-3 grid-rows-2 gap-4">

<div class=" relative p-2 space-y-2 text-sm bg-zinc-900 rounded-xl">
  <h3>Use React if ...</h3>
  <div class="pl-4">

    ✅ 需要最大生態系統  
    ✅ 團隊已熟悉 React   
    ✅ 接受 粗回應性的取捨
    ✅ 會需要安裝第三方套件  

  </div>

  <div class="absolute bottom-3 right-3 opacity-50">
    <img src="/react.svg" width="40" height="40"/>
  </div>
</div>
<div class="relative p-2 pb-5 space-y-2 text-sm bg-zinc-900 rounded-xl">
  <h3>Use Vue if...</h3>
  <div class="pl-4">

    ✅ 想要簡單上手  
    ✅ 喜歡 template syntax  
    ✅ 需要漸進式採用  
    ✅ 中文資源豐富  
    ✅ 平衡效能與易用性

  </div>
  <div class="absolute bottom-3 right-3 opacity-50">
    <img src="/vue.svg" width="40" height="40"/>
  </div>
</div>

<div class="relative p-2 pb-5  space-y-2 text-sm bg-zinc-900 rounded-xl">
 <h3>Use Angular if...</h3>
  <div class="pl-4">

    ✅ Google 生態系整合
    ✅ 大型企業應用程式  
    ✅ 團隊偏好 TypeScript  
    ✅ 需要內建 routing / forms  
    ✅ 需要完整且龐大的框架

  </div>
  <div class="absolute bottom-3 right-3 opacity-50">
    <img src="/angular.svg" width="40" height="40"/>
  </div>
</div>

<div class="relative p-2 pb-5  space-y-2 text-sm bg-zinc-900 rounded-xl">
  <h3>Use Svelte if...</h3>
  <div class="pl-4">

    ✅ 追求最小 bundle size  
    ✅ 喜歡簡潔語法  
    ✅ 可接受較小生態系  
    ✅ 重視開發體驗  
    ✅ 應用程式強調效能

  </div>
  <div class="absolute bottom-3 right-3 opacity-50">
    <img src="/svelte.svg" width="40" height="40"/>
  </div>
</div>

<div class="relative p-2 pb-5  space-y-2 text-sm bg-zinc-900 rounded-xl">
  <h3>Use Solid if...</h3>
  <div class="pl-4">

    ✅ 無虛擬 DOM 開銷   
    ✅ 類似 React 但基於細回應性  
    ✅ 重視細回應性帶來的效能 

  </div>
  <div class="absolute bottom-3 right-3 opacity-50">
    <img src="/solid.svg" width="40" height="40"/>
  </div>

</div>

<div class="relative p-2 pb-5 space-y-2 text-sm bg-zinc-900 rounded-xl">
  <h3>Use Qwik if...</h3>
  <div class="pl-4">

    ✅ 初始載入效能至關重要  
    ✅ 需要即時互動  
    ✅ 想要 O(1) 載入特性  
    ✅ SEO 極度重要  
    ✅ 內容密集型網站

  </div>

  <div class="absolute bottom-3 right-3 opacity-50">
    <img src="/qwik.svg" width="40" height="40"/>
  </div>

</div>
</div>

---

# 總結

<div class="mt-6 space-y-4">

<div class="p-4 bg-neutral-700 rounded-xl">
  <div class="text-blue-400 font-bold mb-2 tracking-wider">Reactivity 模型決定效能特性</div>
  <div class="pl-2">Fine-grained (Signals) vs Coarse-grained (React) 各有權衡</div>
</div>
<div class="p-4 bg-neutral-700 rounded-xl">
  <div class="text-blue-400 font-bold mb-2 tracking-wider">React 不是傳統意義上的 reactive</div>
  <div class="pl-2"><code>v = f(s)</code> - 重新執行函數而非自動追蹤變化</div>
</div>
<div class="p-4 bg-neutral-700 rounded-xl">
  <div class="text-blue-400 font-bold mb-2 tracking-wider">沒有最好的框架</div>
  <div class="pl-2">根據專案需求、團隊能力、生態系選擇最適合的</div>
</div>

</div>

---

# 複習問題

<v-clicks>

1. React、Vue、Svelte、Solid 和 Angular 之間的回應性模型有何不同？這些差異對這些程式庫 / 框架的效能和開發體驗有何影響？
 - 主要分為粗回應性與細回應性模型。
React 屬於粗回應性，當狀態變更時，它會重新執行整個元件函式，然後再透過虛擬 DOM 來優化性能。
而 Vue、Svelte、Solid 則採用細回應性。它們使用 Signals 或編譯器，能夠精確追蹤狀態依賴。當資料改變時，系統會主動通知，只對 DOM 中變化的特定部分進行更新。且組件函式通常只需運行一次，因此運行效能更高，且開發者不必擔心手動記憶化的複雜性

2. 討論 Qwik 採用哪一種獨門手段來將效能最大化，他與我們討論過的其他 UI 程式庫 / 框架的方法有何不同？
 - Qwik 最大的不同在於它使用的是可恢復性，而非Hydration
伺服器端渲染後，用戶端必須下載所有元件程式碼，並重新執行程式碼，才能重建元件樹、事件監聽器，使應用程式變得可互動，Qwik 則跳過這個「重播」步驟。它在伺服器端執行時會暫停，並將應用程式的所有狀態、事件監聽器和元件樹結構資訊序列化到 HTML 中。用戶端啟動幾乎是瞬時的，它只載入一個極小的 Qwik Loader，然後直接從伺服器端暫停的位置恢復執行。

</v-clicks>

---

# 複習問題

<v-clicks>

3. React 在傳統意義上不是回應性的。詳細解釋這個說法，比較他與 vue 或 Svelte 等 程式庫中的推送式回應性模型。
- 在傳統的回應性中，組件會自動追蹤狀態的依賴關係。但 React 不會自動追蹤依賴項目和傳播變更，當 `useState` 狀態更新時，React 重新執行整個組件函式，產生新的虛擬 DOM，透過協調來比較新舊樹之間的差異，拉取需要變更的部分。這種方式雖然確保了 v = f(s)，但會造成不必要的組件重新執行。<br />推送式模型（如 Vue, Svelte, Solid）使用 Signals。當程式碼執行時，計算之間的依賴關係會被自動追蹤，當回應性依賴項目改變時，依賴它的所有計算都會自動重新執行來反映這個改變。
 
4. 什麼是 React Forget？它如何運作？比較它與 singal。
- React Forget 是一種 React 工具鏈，他會強制執行 React 規則，程式碼在運行時能夠自動跳過那些狀態或 Props 沒有改變的元件的重新執行，聰明的將「不會在應用程式的生命週期中改變的值記憶化」，解決 React 粗回應性帶來的效能問題。
- Signals 可以精準的推送更新，React Forget 處理了記憶化，但 React 在更新時仍需要遍歷元件樹並比較屬性，才能決定哪些元件可以跳過渲染。

</v-clicks>

---
layout: center
class: text-center
---

<h1 class="text-2xl font-bold text-center">學習資源</h1>
<div class="flex justify-center">
  <div class="text-left"> 
    <ul class="space-y-1">
      <li class="list-none">📚 <a href="https://angular.dev/">Angular.js</a> - angular.dev</li>
      <li class="list-none">📚 <a href="https://vuejs.org/">Vue.js</a> - vuejs.org</li>
      <li class="list-none">📚 <a href="https://www.solidjs.com/">Solid</a> - solidjs.com</li>
      <li class="list-none">📚 <a href="https://svelte.dev/">Svelte</a> - svelte.dev</li>
      <li class="list-none">📚 <a href="https://qwik.dev/">Qwik</a> - qwik.builder.io</li>
      <li class="list-none">📚 <a href="https://react.dev/">React</a> - react.dev</li>
    </ul>
  </div>
</div>
