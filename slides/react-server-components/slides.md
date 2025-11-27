---
theme: seriph
title: React Server Components
info: |
  ## React Server Components
  深入了解 RSCs 的運作原理與優勢
# class: text-center

defaults:
  class: bg-neutral-800 text-white
drawings:
  persist: false
transition: slide-left
# 全域預設，把每張 slide 的 color 預設為 neversink 的 dark 色系
# defaults:
#   color: dark

mdc: true
fonts:
  # basically the text
  sans: Robot
  # use with `font-serif` css class from UnoCSS
  serif: Robot Slab
  # for code blocks, inline code, etc.
  mono: Fira Code
---

<h1>React Server Components</h1>

深入了解 RSCs 的運作原理與優勢

<div class="abs-br m-6 flex items-center">
  <div class="text-sm">2025-12-09</div>
  <a href="https://github.com/WeiLocus" target="_blank" class="slidev-icon-btn">
    <carbon:logo-github />
  </a>
</div>

---

# 什麼是 React Server Components（RSCs)？


<div class="my-8 p-4 bg-blue-500 bg-opacity-10 rounded">
💡 RSCs 引入一種在<strong>伺服器上運行</strong>的新型組件
</div>

- React Server Components 是 React 生態系統的趨勢
- Next.js 已全面支持 RSCs
- 在 React 19 是穩定的，但建議釘選到特定的 React 版本

<div class="absolute bottom-3 right-3 flex gap-4">
  <a
    href="https://react.dev/reference/rsc/server-components"
    target="_blank"
    rel="noopener noreferrer"
    class="inline-block"
  >
    server-components
  </a>
</div>

---

# RSCs 可以做到

<div class="grid grid-cols-3 gap-4 mt-8">


<div class="p-4 border rounded hover:shadow-lg transition">
<div class="text-3xl mb-2">📁</div>
<h3 class="font-bold mb-2">讀取檔案系統</h3>
<p class="text-sm">直接讀取 .md、.json、.txt 等檔案</p>
</div>

<div class="p-4 border rounded hover:shadow-lg transition">
<div class="text-3xl mb-2">⚡</div>
<h3 class="font-bold mb-2">抓取靜態內容</h3>
<p class="text-sm">Build time 就完成資料獲取</p>
</div>

<div class="p-4 border rounded hover:shadow-lg transition">
<div class="text-3xl mb-2">🗄️</div>
<h3 class="font-bold mb-2">直接存取資料層</h3>
<p class="text-sm">像後端一樣直接操作資料庫</p>
</div>

</div>

<v-click>

<div class="mt-12 text-center text-3xl">
這些組件在 <strong class="text-red-500">build time</strong> 運行
</div>

</v-click>

---

# 抓取靜態資料的傳統做法

<div class="grid grid-cols-2 gap-6">

<div>

```js {2-3|6-9}
// bundle.js
import marked from 'marked'; // 35.9K (11.2K gzipped)
import sanitizeHtml from 'sanitize-html'; // 206K (63.3K gzipped)

function Page({ page }) {
  const [content, setContent] = useState('')
  useEffect(() => {
    fetch(`/api/content/${page}`).then((data) => {
      setContent(data.content)
    })
  }, [page])

  return <div>{sanitizeHtml(marked(content))}</div>
}
```

</div>
<div>


### 兩次請求的問題

1. **第一次請求**: 下載 JavaScript bundle
   - 包含 marked (11.2K)
   - 包含 sanitize-html (63.3K)
   - 總共 **75K gzipped**

2. **第二次請求**: useEffect 中 fetch 內容

</div>
</div>

---

# 抓取靜態資料 RSC 做法 

<div class="grid grid-cols-2 gap-6">

<div>

```js {all}
import marked from 'marked'
import sanitizeHtml from 'sanitize-html'

async function Page({ page }) {
  const content = await file.readFile(`${page}.md`)

  return <div>{sanitizeHtml(marked(content))}</div>
}
```

</div>
<div>

- 套件在**伺服器端執行**
- 不打包到前端
- Build time 就讀取內容
- 首次請求就有 HTML

<div class=" w-100 mt-4 p-3 bg-green-500 bg-opacity-10 rounded text-sm">
📦 Bundle size: <strong>0 K</strong>
</div>
</div>
</div>

---

# 提供高效率的應用程式

<div class="mt-8" />
React Server Components 藉著使用 prop 來將資料從伺服器組件傳給瀏覽器內的用戶端互動組件，提供高效率的應用程式

(Server Component 先幫你抓好資料，再把資料用 props 傳給 Client Component，讓用戶在瀏覽器中直接使用這些資料做互動)

---

# 伺服器組件怎麼運作

<div class="my-8 p-4 bg-blue-500 bg-opacity-10 rounded">
💡 React 組件只是一個 回傳 React 元素的函式
</div>


```js
const Component = () => <div>hi!</div>
```

對應到 JavaScript 物件，架構如下：



```js {all}
{
  $$typeOf: Symbol("react.element"),
  type: "div",
  props: {
    children: [{
      $$typeOf: Symbol("react.element"),
      props: { children: "hi!" }
    }]
  }
}
```

---

# 伺服器組件的好處

<div class="grid grid-cols-2 gap-6">

<div>

1. 只在伺服器執行，伺服器是我們可以掌控計算能力的機器，效能更容易預測。
2. 在安全的伺服器環境中執行，不必擔心權杖或其他安全資訊外流。
3. 伺服器組件可以是非同步的，可以等待它們在伺服器上完成執行，再透過網路與用戶端分享它們
   -  Server Component 能寫 async/await，React 會自動幫你「暫停等待」非同步資料。
   -  運用 Suspense + Promise，可以邊抓資料邊顯示部分內容，其他慢慢載入。

</div>
<div>

```js {all|4-5|7|12-14}
import db from './database'

async function Page({ id }) {
  // 暫停等待資料
  const note = await db.notes.get(id)
  // 不等待,先傳 Promise
  const commentsPromise = db.comments.get(note.id)

  return (
    <div>
      {note}
      <Suspense fallback={<p>Loading Comments...</p>}>
        <Comments commentsPromise={commentsPromise} />
      </Suspense>
    </div>
  )
}
```

</div>
</div>

---

# 伺服器組件的好處
<div class="mt-6" />

4. 減少用戶端程式碼包大小
5. 自動程式碼切分: 伺服器可以根據資料和權限判斷是否需要某個用戶端組件。如果組件不需要，則該組件的 JavaScript 程式碼塊將完全不會被下載
6. 消除網路瀑布與提升資料獲取效率: 因為伺服器組件能夠直接在伺服器環境中執行資料獲取邏輯

---

# 伺服器組件與伺服器算繪(Server rendering)

<div class="mt-4" />

**伺服器組件和伺服器算繪 是 <span v-mark.underline.orange>兩個獨立的程序</span>**

<div class="grid grid-cols-2 gap-6">

<div>

第一個階段，RSCs renderer : 

將伺服器組件轉成一棵 React 元素樹 (Element Tree)



```js
<div>
  <h1>Hi!</h1>
  <p>I Like React</p>
</div>
```

<!--用戶端組件會透過 'use client' 標記，RSCs renderer 會停止深入，並在元素樹中留下一個佔位符 (Placeholder) 和對應的模組參考 -->

</div>
<div>

```js
{
  $$typeOf: Symbol("react.element"),
  type: "div",
  props: {
    children: [
      {
        $$typeOf: Symbol("react.element"),
        type: "h1",
        props: {
          children: "hi!"
        }
      },
      {
        $$typeOf: Symbol("react.element"),
        type: "p",
        props: {
          children: "I like React!"
        }
      },
    ],
  },
}
```

</div>
</div>

---

# 伺服器組件與伺服器算繪(Server rendering)

<div class="mt-4" />
<div>

第二階段，伺服器算繪 將這棵 React 元素樹「轉換成可以用網路串流傳到用戶端的標記」

細節步驟是：
- 在伺服器上，這棵元素樹被進一步序列化為字串 或 串流
  - <kbd>renderToString</kbd> 把 React 元素樹渲染成整個 HTML 字串
  - <kbd>renderToPipeableStream</kbd> 把 React 元素樹轉換成 Node.js 串流
- 將序列化的結果，一個字串化的大 JSON 物件，在 Next.js 稱為 RSC Payload(一種精簡的二進制格式)，將它發送到用戶端 

<div class="text-[16px] px-6">
<a href="https://vercel.com/guides/how-to-optimize-rsc-payload-size#what-is-in-the-rsc-payload">RSC payload</a> 包含：
  <li>伺服器組件的渲染結果</li>
  <li>需要在瀏覽器端運行的 Client Component，Payload 只放一個「位置標記」和它的 JavaScript 檔案參考</li>
  <li>Server Component 要傳給 Client Component 的 props（資料)</li>
</div> 


- 用戶端的 React 讀取這個解析後的 JSON ，像平常一樣算繪它

</div>

---

用伺服器端的程式碼來描述：    

<div class="grid grid-cols-3 gap-4">

<div class="col-span-2">

```js {7|10|19}
// server.js
const app = express();
app.use(express.static(path.join(__dirname, "build")));

app.get("*", async (req, res) => {

  const rscTree = await turnServerComponentsIntoTreeOfElements(<App />);

  // Render the awaited server components to a string
  const html = ReactDOMServer.renderToString(rscTree);

  res.send(`
    <!DOCTYPE html>
    <html>
      <head>
        <title>My React App</title>
      </head>
      <body>
        <div id="root">${html}</div>
        <script src="/static/js/main.js"></script>
      </body>
    </html>
  `);
});

app.listen(3000, () => {
  console.log("Server listening on port 3000");
});

```

</div>

<div class="text-sm">

1. 伺服器算繪組件的關鍵:
`turnServerComponentsIntoTreeOfElements`
，返回的 `rscTree` 是一個包含所有已算繪伺服器組件的 React 元素樹。

2. 伺服器算繪器 程序，將 `rscTree` 轉換為用戶端可用的格式
`const html = ReactDOMServer.renderToString(rscTree);`
透過 `renderToString` 將接收到的 React 元素樹，序列化 (serialized) 成一個完整的 HTML 字串

3. `res.send()`，伺服器將完整的 HTML 結構發送給用戶端瀏覽器

</div>
</div>

---

# turnServerComponentsIntoTreeOfElements 底層
`const rscTree = await turnServerComponentsIntoTreeOfElements(<App />);`

核心邏輯是一個大型的遞迴 if/else 判斷樹，它根據輸入的 jsx（即 JSX 節點或組件）類型來決定下一步的操作。

jsx 的類型可能是

- 純值：string、number、boolean
- 陣列
- 物件（區分 React 元素、自訂組件和一般資料物件）

---

### turnServerComponentsIntoTreeOfElements 程式碼

```js {1|2-5|7-9|10-23|12-18|19-21}
async function turnServerComponentsIntoTreeOfElements(jsx) {
  if (
    typeof jsx === "string" || typeof jsx === "number" || typeof jsx === "boolean" || jsx == null
  ) {
    return jsx;  // 對純值類型不需要做什麼事情
  }
  if (Array.isArray(jsx)) {
    return await Promise.all(jsx.map(renderJSXToClientJSX(child))); // 處理陣列裡面每一個項目
  }
  if (jsx != null && typeof jsx === "object") {   // 處理物件
    // 如果物件是 React element
    if (jsx.$$typeof === Symbol.for("react.element")) {
      // 像 <div />這樣的內建 DOM 元素
      if (typeof jsx.type === "string") {...}
        
      // 如果是自訂組件，像是 <Footer />
      if (typeof jsx.type === "function") {...}
      throw new Error("Not implemented.");
    } else {
      // 任意的物件，但不是 React element，遍歷每個值  
        return Object.fromEntries(...);
    }
  }
  throw new Error("Not implemented");
}
```

<!--
純字串處理和陣列都直接用講的

文字：如果是看到一個 `<h1>Hi!</h1>` ，只是一個字串，維持原樣 return 

陣列：
例如 React Fragment 的內容或多個子節點
舉例：fragment 用陣列來表示子元素：有內建 DOM 元素和 自訂組件
-->

---

jsx = 陣列

例如 React Fragment 的內容或多個子節點

<div class="grid grid-cols-2 gap-4">

<div>

舉例：fragment 用陣列來表示子元素：

有內建 DOM 元素和 自訂組件 
```js
[
  <div>hi</div>,
  <h1>hello</h1>,
  <span>love u</span>,
  (props) => <p id={props.id}>loorm ipsum</p>
]
```

</div>
<div class="mt-3">

用函式遞回處理每個子元素，使用 `await Promise.all` 來等待所有子項的解析完成，確保所有子項目都被解析
```js
if (Array.isArray(jsx)) {
  return await Promise.all(jsx.map(renderJSXToClientJSX(child)));
}
```
</div>
</div>

---

jsx = 物件

透過 `$$typeof` 屬性來判斷，如果是 `Symbol.for("react.element")`就是 React 元素。

<div class="grid grid-cols-5 gap-4">

<div class="col-span-3">

- type = string：例如 "div"、"span"，爲一般的內建的 DOM 元素，RSCs renderer 不會對此元素本身進行進一步的執行，而是直接返回它，會對其 props 進行遞迴處理，確保 prop 內部的任何 children 都會被解析。
- type = function：是自訂組件，例如`<Footer />`，取出組件的函式 (Componen) 和屬性 (props)，呼叫執行`await Component(props)`。由於伺服器組件可能是非同步的，透過 await 等待非同步操作完成， 組件執行後，會返回下一層的 JSX 結構（returnedJsx）。函式會遞迴呼叫自身來處理這個新的返回值，直到整個樹結構被解析成純值或內建 DOM 元素為止。

</div>

<div class="col-span-2">

```js {1|2-7|8-13}
if (jsx.$$typeof === Symbol.for("react.element")) {
  if (typeof jsx.type === "string") {
    return {
        ...jsx,
        props: await renderJSXToClientJSX(jsx.props),
    };
  }
  if (typeof jsx.type === "function") {
    const Component = jsx.type;
    const props = jsx.props;
    const returnedJsx = await Component(props);
    return renderJSXToClientJSX(returnedJsx);
  }
  throw new Error("Not implemented.");
} else {...}
```

</div>

</div>

<!--
type = function 敘述太長，再內化一下，看到時候要不要改成用講的
-->

---

#### jsx = 物件

<div class="grid grid-cols-2 gap-4">

<div class="mt-4">

- 一般物件：遍歷該物件的所有鍵值對`(Object.entries(jsx))`，並對每個值遞迴呼叫函式，並回傳結果。

</div>

<div>

```js {2-3|5-13}
if (jsx.$$typeof === Symbol.for("react.element")) {
  if (typeof jsx.type === "string") {...}
  if (typeof jsx.type === "function") {...}
  throw new Error("Not implemented.");
} else {
    return Object.fromEntries(
          await Promise.all(
          Object.entries(jsx).map(async ([propName,value]) => [
          propName,
          await renderJSXToClientJSX(value),
      ])
    )
  );
}
```

</div>

</div>

<div class="my-8 p-4 bg-blue-500 bg-opacity-10 rounded">
💡 把 <code>turnServerComponentsIntoTreeOfElements</code> 函式執行後的結果作為參數，傳給 <code>renderToString</code> 或 <code>renderToPipeableStream</code> 函式，把它<span v-mark.underline.orange>序列化</span>成瀏覽器能夠直接解析和渲染的 HTML 結構，發送到用戶端瀏覽器。
</div>

---

# 什麼是序列化?

<div class="mt-8">

<div class="my-8 p-4 bg-blue-500 bg-opacity-10 rounded">
💡 伺服器端渲染流程中，將 React 元素轉換成字串的過程
</div>

<div class="mt-8" />

典型的 React 應用程式流程<br />
透過 `React.createElement` 或 JSX 語法建立 React 元素

```jsx
const element = <h1>Hello world</h1>
```

<v-clicks>

<div class="mt-4">

- 這代表我們**期望看到**的渲染結果
- 還不是實際的 DOM 元素

</div>

</v-clicks>

</div>

---

# 什麼是序列化?

<div class="grid grid-cols-2 gap-8 mt-8">

<div>

使用 `renderToString` 之類的函式來進行算繪

```jsx
const element = <h1>Hello world</h1>

const htmlString = ReactDOMServer.renderToString(element)
```


序列化過程:

1. 遍歷 React 元素樹
2. 為每個元素生成相應的 HTML
3. 全部串接在一起

預期結果: 拿到 HTML 字串

htmlString 會是 `<h1>Hello world</h1>`

</div>

<div>

這個 HTML 字串可以:
- 發送到用戶端
- 作為網頁的初始標記

<v-clicks>

用戶端載入流程

1. 接收 HTML 字串作為初始標記
2. 載入 JavaScript bundle
3. Hydrate DOM
   - 附加事件處理程序
   - 填入動態內容
</v-clicks>
</div>

</div>

---

# 序列化的目的
序列化確保伺服器端與用戶端結構完全匹配，React 才能正確進行調和 (reconcile) 和 比較 (diff)

<div class="p-4 bg-yellow-50 dark:bg-yellow-900/20 rounded mt-4">

1. <span v-mark.underline.orange>因`$$typeof` 屬性無法被序列化</span>，RSC 架構使用了一個特殊的序列化過程，將不可傳輸的 Symbol 值替換成可傳輸的字串表示（例如將 `$$typeof` 的 Symbol 值替換為 "react.element" 字串）
2. <span v-mark.underline.orange>比對靜態 HTML 的 DOM 元素結構 (伺服器端) 與 React 組件透過 jsx 來定義的結構 (用戶端)是否相符</span>，若相符，即可產生一致且可預測的初始算繪，確保了初始載入期間的正確性與效率

</div>

<div class="grid grid-cols-2 gap-8 mt-4">

<div class="px-6 py-3 rounded-2xl border-2 border-green-400 bg-green-50 dark:bg-green-900/20">
  <h3 class="text-xl font-bold text-green-600 mb-3 flex items-center gap-2">
    ✅ 結果相符
  </h3>
  <ul>
    <li>流暢的使用者體驗</li>
    <li>正確且高效的渲染</li>
    <li>事件監聽器正確附加</li>
    <li>無視覺閃爍</li>
  </ul>
</div>

<div class="px-6 py-3 rounded-2xl border-2 border-red-400 bg-red-50 dark:bg-red-900/20">
  <h3 class="text-xl font-bold text-red-600 mb-3 flex items-center gap-2">
    ❌ 結果不一致
  </h3>
  <ul>
    <li>渲染結果閃爍</li>
    <li>版面錯位</li>
    <li>事件監聽器失效</li>
  </ul>
</div>

</div>

---

# 序列化 `$$typeof` 解決方案: Replacer 函數

<div class="mt-8" />

將 React 元素樹轉換成 HTML 字串時，**不能只用 `JSON.stringify`**

React 借助 replacer 函數

```js
JSON.stringify(object, replacer)
JSON.parse(object, replacer)
```

<div class="mt-6" />

- 接收每個鍵(key)和值(value)
- 可以自定義序列化行為
- **將 Symbol 替換為可序列化的字串**

---

# 序列化實作

<div class="mt-8" />

將 Symbol 轉換為字串

```js {all|2-4|5}
JSON.stringify(jsxTree, (key, value) => {
  if (key === "$$typeof") {
    return "react.element"; // <- 轉換為字串
  }
  return value; // <- 返回所有其他值
});
```

<div class="mt-6" />


<div class="bg-green-50 dark:bg-green-900/20 p-4 rounded">

原本無法傳輸的 React 元素樹，成功轉換為**字串化的 JSON 物件** (在 Next.js 則是 RSC Payload)

可以透過網路發送給用戶端

</div>

---

# 反序列化實作

<div class="mt-8" />

用戶端: 將字串還原為 Symbol

```js {all|2-4|5}
JSON.parse(serializedJsxTree, (key, value) => {
  if (key === "$$typeof") {
    return Symbol.for("react.element"); // <- 還原為 Symbol
  }
  return value; // <- 返回所有其他值
});
```

<div class="mt-6" />


<div class="bg-green-50 dark:bg-green-900/20 p-4 rounded">

還原為 React 識別 React 元素所需的**特殊符號**

</div>

---

# RSCs 如何實現 Navigation
讓狀態在路由轉換之間保持存在

<div class="mt-8" />

❌ 不好的使用者體驗: 按下連結後，網頁會重新載入

在伺服器組件裡的 app 裡有一個連結：`<a href="/blog">Blog</a>`


<div class="my-8 p-4 bg-blue-500 bg-opacity-10 rounded">
💡 使用 RSCs 實現軟導航 (Soft Navigation)

<div class="pl-4">

- 不重新載入整個頁面
- 改為只向伺服器請求「對應頁面的 React 元素樹資料」
- 用戶端根據新的元素樹，產生更新後的 React Element Tree，並局部更新畫面

</div>
</div>

使用者點擊連結時，向伺服器傳送目標 URL，伺服器會將該網頁的 JSX 樹 送回來給我們，在瀏覽器內的 React 使用新 JSX 樹來重新算繪整個網頁，讓我們得到一個新網頁，而不需要重新載入整個網頁。

---

# 用戶端程式碼

<div />

在連結加上事件監聽器，阻止連結的預設行為，呼叫 navigate 函式
```js
window.addEventListener("click", (event) => {
  if (event.target.tagName !== "A") {
    return;
  }
  event.preventDefault();
  navigate(event.target.href);
});
```

**關鍵點**:
- 監聽所有點擊事件
- 檢查是否為 `<a>` 元素
- 阻止預設的頁面跳轉
- 轉交給自定義的 navigate 函式處理

---

# navigate 函式拆解

<div class="grid grid-cols-2 gap-4">

<div>

**1. 向伺服器發送請求**
- 透過特定標頭 <code>"jsx-only": true</code> 告訴伺服器，用戶端需要的是新的 React 元素樹，不是完整 HTML

**2. 反序列化階段**
- 用戶端將 jsxTree 反序列化為 React 元素
- 還原 `$$typeof` Symbol

**3. 重新渲染**
```js
root.render(element);
```

- 將新獲得的 React 元素（新頁面的 JSX 樹）傳遞給應用程式的根節點

</div>
<div class="col-span-1">

````md magic-move
```js {all|2-5|6-10|12}
async function navigate(url) {
  const response = await fetch(url, {
    headers: { "jsx-only": true }
  });
  const jsxTree = await response.json();
  const element = JSON.parse(jsxTree, (key, value) => {
    if (key === "$$typeof") {
      return Symbol.for("react.element");
    }
    return value;
  });
  root.render(element);
}
```
```js
async function navigate(url) {
  const response = await fetch(url)
  const jsxTree = await response.json();
  const element = deserialize(jsxTree)
  root.render(element);
}
```
````

</div>
</div>

---

# 伺服器端處理請求
<div />

讓伺服器收到 jsx-only 標頭後，只回應下一頁的 JSX 樹物件，而不是整個 HTML 字串

```js {2-13}
app.get("*", async (req, res) => {
  const jsxTree = await turnServerComponentsIntoTreeOfElements(<App />);
  
  if (req.headers["jsx-only"]) {
    // 軟導航:只回傳 JSX 樹
    res.end(
      JSON.stringify(jsxTree, (key, value) => {
        if (key === "$$typeof") {
          return "react.element";
        }
        return value;
      })
    );
  } else {
    // 首次載入:回傳完整 HTML
    // ...
  }
});
```

---

# Root 的角色

<div />

**首次載入時**: 

```js
const root = hydrateRoot(document,<App /> );
```

從 React 取得 一個 Root
- 用戶端使用 `hydrateRoot(document, <App />)`，讓伺服器產生的 HTML 具有互動性
  - `hydrateRoot`以參數來接收 React 根組件和 DOM 容器

**後續軟導航時**:

使用 Root 來將新的元素算繪
- 收到新元素樹後使用 `root.render(element)`， 更新畫面

---

# 軟導航 VS. 傳統導航


<div class="grid grid-cols-2 gap-8 mt-6">

<div class="px-6 py-3 rounded-2xl border-2 border-green-400 bg-green-50 dark:bg-green-900/20">
  <h3 class="text-xl font-bold text-green-600 mb-3 flex items-center gap-2">
    軟導航 (Soft Navigation)
  </h3>
  <ul>
    <li>只更新必要的部分</li>
    <li>保留應用程式狀態</li>
    <li>流暢的使用者體驗</li>
    <li>更快的頁面切換</li>
  </ul>
</div>

<div class="px-6 py-3 rounded-2xl border-2 border-red-400 bg-red-50 dark:bg-red-900/20">
  <h3 class="text-xl font-bold text-red-600 mb-3 flex items-center gap-2">
    傳統導航
  </h3>
  <ul>
    <li>整個頁面重新載入</li>
    <li>重新下載所有資源</li>
    <li>失去應用程式狀態</li>
    <li>白屏閃爍</li>
  </ul>
</div>

</div>

---

# React Server Components 需要留意的限制
並非所有組件都可以成為伺服器組件

當功能需要有互動性，就無法寫成伺服器組件

例如：Counter 記數器組件，使用 `useState` 更新狀態 count 狀態，需要靠使用者點擊
```js
function Counter() {
  const [count, setCount] = useState(0);
  return (
    <div>
      <h1>Hello friends, look at my nice counter!</h1>
      <p>About me: I like pie! Sign my guest book!</p>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>+</button>
    </div>
  );
}
```

---

# 無法成為伺服器組件：使用 useState & onClick

<div class="grid grid-cols-2 gap-10 mt-4">

<div>

useState

```js
const [count, setCount] = useState(0);
```

- useState 是 純用戶端 API，伺服器不知道 count 的初始值
- 無法在伺服器渲染出正確的初始 HTML
- 伺服器端 state 是 多用戶端共享的
- 但 count 應該是 單一使用者本地的狀態
風險：可能導致 多用戶端之間的狀態洩漏
</div>

<div>

onClick

```js
<button onClick={() => setCount(count + 1)}> + </button>
```

- 伺服器 沒有互動性
- onClick 的函式 無法被序列化
- 伺服器無法把函式 傳送到瀏覽器執行

</div>
</div>

---

# 解決方案: 拆分伺服器部分和用戶端

<div class="mt-8"/>

**優點**: 減小需要被打包的 JavaScript Bundle 大小，得到更快的載入時間和更好的效能
<div class="grid grid-cols-2 gap-4">

<div>

伺服器端組件

```js
function ServerCounter() {
  return (
    <div>
      <h1>Hello friends, look at my nice counter!</h1>
      <p>
          About me: I like to count things and I'm a counter....
      </p>
      <InteractiveClientPart />
    </div>
  );
}
```

</div>

<div>

用戶端組件：在檔案最上方加上 `"use client"` 指令

```js
"use client";

function InteractiveClientPart() {
  const [count, setCount] = useState(0);
  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}> + </button>
    </div>
  );
}
```
</div>
</div>

---

# React 如何區分組件類型?

<div class="mt-8"/>

React 藉著 bundler 建構工具來支援區分「伺服器組件」和「用戶端組件」，**生成獨立的模組圖**

**伺服器模組圖**
- 程式碼不會被打包放入 bundle 中

**用戶端模組圖**
- 開頭為 `"use client"` 指令的檔案都會被放入 bundle
- 在瀏覽器需要時下載和執行

---

# 何時該匯入和執行用戶端組件?

<div class="mt-8 p-4 bg-blue-500 bg-opacity-10 rounded">
💡 模組參考機制
</div>

在 Counter 例子中

- `InteractiveClientPart` 組件是用戶端組件
- 伺服器會替它預留一個**位置 (placeholder)**
- 對應到用戶端具體模組的**參考 (reference)**

**標記方式**:

`Symbol(react.module.reference)` 告訴 React 這是一個用戶端組件的參考
```js
$$typeof: Symbol.for("react.module.reference")
```

---

# 伺服器渲染的組件樹結構
伺服器會為用戶端組件建立一個模組參考

<!-- ![module reference](/module-reference.png) -->
<img src="/module-reference-dark.png" class="w-[80%] rounded-xl" />

---

# Bundler 的角色

<div />

**伺服器端**:
1. 在自己的環境中先將整個網頁的 UI 結構（組件樹）全部渲染完成
2. 遇到需要互動的用戶端組件時,留下一個「空缺」
3. 放入一個**模組參考**作為佔位符
4. 即使是用戶端組件的子組件,也會在伺服器上遞迴渲染
5. 產生一棵完整的樹

**用戶端瀏覽器**:
1. 拿到已經渲染好的結構
2. 下載對應的 JavaScript Bundles
3. 將這些「空缺」填滿
4. 讓網頁具備互動功能

---

# 概念總結

<h3 class="my-4">伺服器組件的限制</h3>

<div class="pl-4 space-y-2">
  <div>❌ 具有互動性的 React Hooks，例如 <code>useState</code></div>
  <div>❌ 與生命週期有關的邏輯，例如 <code>useEffect</code></div>
  <div>❌ 用事件處理器，例如 <code>onClick</code>、 <code>onChange</code></div>
  <div>❌ 用 Browser-only API，例如<code>localStorage</code>、 <code>window</code> 和 <code>Navigator.geolocation</code></div>
</div>

<h3 class="my-4">解決策略</h3>
<div class="pl-4">

  - 使用 `"use client"` 指令標記用戶端組件
  - 清楚拆分伺服器與用戶端邏輯
  - 利用模組參考機制連接兩者

</div>
---

# 釐清常見的誤解

<div class="bg-red-50 dark:bg-red-900/20 p-4 rounded my-6">❌ 用戶端組件只在用戶端執行
</div>

<h3 class="my-4">Server rendering 時</h3>

- 伺服器組件在伺服器上執行，輸出代表 React 元素的物件
- <span v-mark.underline.orange>用戶端組件在伺服器上執行</span>，輸出代表 React 元素的物件
- 在伺服器，有一個代表來自用戶端和伺服器組件的所有 React 元素的大型物件
- 它被轉換成一個字串並送到用戶端
- 接下來，伺服器組件永遠不會在用戶端執行
- 用戶端組件僅在用戶端執行

---

# RSCs - Client Component 和 Server Component 的差別

<div class="text-[16px]">

| 功能項目    | Client Components <br/> (`"use client"`)  | Server Components                             |
| ---------- | ----------------------- |------------------------------------------- |
| State / Hooks     | ✅              | ❌           |
| Props             | ✅              | ✅ 但傳給 Client Components 時必須可序列化，不能是 functions 或 classes |
| Data fetching     | 建議搭配資料抓取函式庫 React Query    | ✅ 推薦使用，可在 component 內使用 `async/await`                        |
| Can import       | 只能匯入 **Client Components**（無法往回引入 Server Components） | 可以匯入 **Client** 和 **Server Components**                      |                   |
| **When re-render?**  | 當 **state 改變** 時重新渲染    | 當 **URL 改變（navigation）** 時重新渲染         |

</div>

---

# 處理伺服器組件的規則

<div class="grid grid-cols-2 gap-4 mt-6 light:text-black">

<div class="p-4 rounded-xl bg-blue-100 dark:bg-blue-900/20">
  <h3 class="font-bold text-lg">序列化至上</h3>
  <p>所有 props 都必須可序列化，不能是函式或其他不可序列化的值。</p>
</div>

<div class="p-4 rounded-xl bg-blue-100 dark:bg-blue-900/20">
  <h3 class="font-bold text-lg">無效的 Hook</h3>
  <p>伺服器端沒有 DOM，也不支援副作用 Hook。</p>
</div>

<div class="p-4 rounded-xl bg-blue-100 dark:bg-blue-900/20">
  <h3 class="font-bold text-lg">狀態不是狀態</h3>
  <p>伺服器組件的狀態在用戶端不保留，應改為用戶端組件。</p>
</div>

<div class="p-4 rounded-xl bg-blue-100 dark:bg-blue-900/20">
  <h3 class="font-bold text-lg">用戶端不能匯入伺服器組件</h3>
  <p>伺服器組件應由伺服器父層傳入，而非直接 import。</p>
</div>

</div>

---

# 序列化至上

<div />

所有的 prop 都必須是可以序列化的

❌ prop 不能是函式 或是 classes
```js
function ServerComponent() {
  return <ClientComponent onClick={() => alert('hi')} />
}
```

**為什麼?**

- 函式屬於 JavaScript 中的**非純量**
- 無法將函式的程式碼或邏輯轉換為可傳輸的字串
- 不可序列化的值無法跨網路傳輸

---

# 無效的Hook

<div />

**伺服器環境沒有** 互動性、DOM、視窗(Window)

**不支援**:
- 有副作用的 Hook
- 依賴狀態、效果或 browser-only API 的 Hook

**例如**: `useState`, `useEffect`

---

# 狀態不是狀態

- 伺服器組件在伺服器上渲染
- 組件中的狀態可能在用戶端是共享的，**洩漏風險高**

**解決方案**:

需要透過 `useState` 或 `useReducer` 來取得狀態的組件,適合做成**用戶端組件**

---

# 用戶端組件不能匯入伺服器組件

<div />

❌ 錯誤做法

伺服器組件僅在伺服器環境執行，如果在用戶端組件直接 import 伺服器組件,可能匯入在用戶端 runtime 環境中無法使用的東西

<div class="grid grid-cols-2 gap-4">

<div>

```js
"use client";
import { ServerComponent } from "./ServerComponent";

function ClientComponent() {
  return (
    <div>
      <h1>Hey everyone, check out my great server component!</h1>
      <ServerComponent />
    </div>
  );
}
```

</div>
<div>

```js
// ServerComponent
import { readFile } from "node:fs/promises";

export async function ServerComponent() {
    const content = await readFile("./some-file.txt", "utf-8");
    return <div>{content}</div>;
}
```

`readFile` 函式和 `node:fs/promises` 模組**不能在瀏覽器中使用**

</div>

</div>

---

# 用戶端組件不能匯入伺服器組件

<div />

✅ 正確做法:
- Bundler 只關注 `import` 陳述句
- 由伺服器組件作為父層，將伺服器子組件透過 **props 或 children** 傳入用戶端組件。

<div class="grid grid-cols-2 gap-4">

<div>

用戶端組件
```js
"use client";

function ClientComponent({ children }) {
  return (
    <div>
      <h1>Hey everyone, check out my great server component!</h1>
      {children}
    </div>
  );
}
```
</div>

<div>

父組件為伺服器組件
```js
import { ServerComponent } from "./ServerComponent";

async function TheParentOfBothComponents() {
  return (
    <ClientComponent>
      <ServerComponent />
    </ClientComponent>
  );
}
```

</div>

</div>

---

# 伺服器操作 (Server Actions)
React Server Components 搭配 `'use server'`，讓用戶端可以呼叫伺服器端函式，我們稱為「伺服器操作」

兩種寫法來定義伺服器操作
1. 將 `'use server'` 指令放在檔案最上方
   - 整個檔案都是 server-only 模組
2. 在 `async function`內的第一行寫上 `'use server'`，我們會稱這個函式為 Server functions
   - 因為網路請求通常是非同步的，`'use server'` 只能在`async function`中使用
   - 想在同一檔案中混合 server 與 client 代碼時使用


<div class="mt-6 p-4 rounded-xl bg-yellow-200 dark:bg-yellow-900/40">
  <div>主要設計用於「修改」伺服器端的狀態，例如處理表單提交。</div>
  <div>不建議將 Server Function 用於資料獲取 (data fetching)，因為它無法快取回傳值</div>
</div>

---

# 伺服器操作 — 範例
<div />

寫法1: 將 `"use server"` 指令放在檔案最上方，將檔案中的所有匯出都標記為可在任何地方使用的伺服器操作，包括在用戶端程式碼中匯入
```js
"use server";

export async function addToCart(data) {...}
export async function removeFromCart(id) {...}
```

寫法2: 提示 React 和 bundler 這個函式可以從用戶端程式碼呼叫，但**只能在伺服器上執行**

```js
async function requestUsername(formData) {
  'use server';
  const username = formData.get('username');
  // ...
}
export default App() {
  <form action={requestUsername}>
    <input type="text" name="username" />
    <button type="submit">Request</button>
  </form>
}
```

---

# `'use server'` 使用要點

- 只能在伺服器端檔案中
- 只能用於非同步函式
- 用來更新伺服器端的狀態


常見誤解
<div class="bg-red-50 dark:bg-red-900/20 p-4 rounded">❌ Server Components 是透過 'use server' 來表示，但 'use server' 指令是用於 伺服器函式
</div>

<div class="absolute bottom-3 right-3">
  <a
    href="https://react.dev/reference/rsc/use-server#"
    target="_blank"
    rel="noopener noreferrer"
    class="inline-block"
  >
    use-server
  </a>
</div>

---

# 表單與異動

<div />

`requestUsername` 是一個傳給 `<form>`的伺服器函式

當使用者提交表單時，React 會讓這個表單的「提交行為」自動對應到伺服器上的這個函式。 `requestUsername` 發送網路請求，並提供 `formData` 作為參數。



```js {all|2-4|10}
// App.js
async function requestUsername(formData) {
  'use server';
  const username = formData.get('username');
  // 處理資料...
}

export default function App() {
  return (
    <form action={requestUsername}>
      <input type="text" name="username" />
      <button type="submit">Request</button>
    </form>
  );
}
```

---

# Server Actions 的工作流程


1. 定義一個單獨的 Server Action 函數
```js
   async function handleRequest(formData) { 'use server'; ... }
```

2. 將 Server Function 傳遞給 `<form>` 的 action 屬性
3. 當使用者提交表單時:
   - 框架自動阻止默認提交
   - React 自動將表單的 FormData 作為第一個參數傳給 Server Function
4. React 自動發送網路請求到伺服器
   - Server Function 在伺服器上執行
   - 能夠直接存取後端資源(例如資料庫)
5. Server Function 執行成功後
   - React 自動重新渲染受影響的伺服器元件樹
   - 在用戶端進行調和

---

# 在「JS bundle 還沒載入」時也能提交表單？

<div />

當使用者按下 submit：
瀏覽器會執行預設的 HTML 表單提交行為，
直接發送 POST 請求到伺服器，這整個過程不需要瀏覽器端的 JavaScript。

這是一種漸進增強 (Progressive Enhancement)的網頁設計策略

**核心原則**:

1. 確保網頁的基本內容和核心功能能在**所有瀏覽器和設備上**都能良好地展現和使用
   - 包括最老舊或功能最少的

2. 額外的 CSS、Script、高級功能應被視為**可選的增強**
   - 即使它們加載失敗或不被支持
   - 也不會影響網站的基礎可用性

透過 Server Actions，React 可以漸進增強表單

<!--
React 在伺服器渲染 (SSR) 時，<form> 是伺服器直接渲染出來的 HTML。

被轉換成：

`<form action="/_actions/requestUsername" method="POST">`
或類似的 URL（具體實現取決於框架，例如 Next.js）。

這種表單是 瀏覽器原生就會運作的。
當你按下「送出」，瀏覽器會：
把資料包成 FormData
送到伺服器
然後伺服器執行對應的 requestUsername 函式
最後回傳一個新頁面（或結果）
👉 這整個流程 不需要任何 JavaScript，純靠 HTML 就能跑。
就算使用者的網路很慢、React 的 JS bundle 還在載（還沒下載完）完）


等瀏覽器把 React 的 JavaScript 載完之後，React 才會「接管」畫面。
這時候：
React 會攔截表單的提交；
改成用 JavaScript（fetch）直接呼叫伺服器；
不需要整頁重整；
可以即時更新畫面（像 SPA 那樣）。
這就叫 漸進式增強 (progressive enhancement)：
-->

---

# 使用 Transition 呼叫伺服器操作

<div />

在非表單的情境下使用伺服器操作時，搭配 useTransition 可以改善使用者體驗，顯示載入中、優化狀態更新過成和處理意外的錯誤。

<div class="grid grid-cols-2">

<div>

當按下「Like」按鈕時，觸發 onClick 函式，用 startTransition 包住伺服器請求。

</div>
<div>

```js {all|6|9-14|18-20}
"use client";
import incrementLike from "./actions";
import { useState, useTransition } from "react";

function LikeButton() {
  const [isPending, startTransition] = useTransition();
  const [likeCount, setLikeCount] = useState(0);   
  const onClick = () => {
    startTransition(async () => {
      const currentCount = await incrementLike();
      setLikeCount(currentCount);
    });
  };
  return (
  <>
    <p>Total Likes: {likeCount}</p>
    <button onClick={onClick} disabled={isPending}>
      Like
    </button>;
    </>
  );
}
```


</div>

</div>

<!--
當按下「Like」按鈕時，觸發 onClick 函式，用 startTransition 包住伺服器請求。

過程中 isPending 會變為 true，UI 可以根據這個狀態顯示「載入中」或 disable 按鈕。
等待伺服器傳回新的 currentCount 後更新 likeCount。
-->

---

# Server Actions 的優勢

<div class="grid grid-cols-2 gap-4 mt-6 light:text-black">

<div class="p-4 rounded-xl bg-yellow-200 dark:bg-yellow-900/40">
  <h3 class="font-bold text-lg">減少 Bundle 大小</h3>
  <p>Server Function 的程式碼不會被下載到用戶端</p>
</div>

<div class="p-4 rounded-xl bg-yellow-200 dark:bg-yellow-900/40">
  <h3 class="font-bold text-lg">漸進增強</h3>
  <p>可以在 JavaScript 載入前就提交表單</p>
</div>

<div class="p-4 rounded-xl bg-yellow-200 dark:bg-yellow-900/40">
  <h3 class="font-bold text-lg">減少延遲</h3>
  <p>直接在伺服器端存取資料源，減少網路往返延遲。</p>
</div>

<div class="p-4 rounded-xl bg-yellow-200 dark:bg-yellow-900/40">
  <h3 class="font-bold text-lg">簡化架構</h3>
  <p>不需要建立額外的 API 路由</p>
</div>

</div>

---

# 表單操作: CSR VS. Server Actions 比較表

<div class="text-[16px]">

| CSR | Server Actions | 關鍵價值 |
|---------|----------------|----------|
| 所有狀態更新、API 請求、錯誤處理皆在用戶端 | 使用 `'use server'` 標記的資料修改邏輯僅存在伺服器端 | 降低用戶端複雜度 |
| 手動使用 `useState` / `useEffect` / fetch 並管理 Pending / 成功 / 失敗等多階段狀態 | 可結合 `useTransition` 等 Hook，自動處理 Pending 轉換 | 自動化狀態流程、減少程式 |
| 提交需透過 `onSubmit + preventDefault()`，手動發送 fetch、序列化 FormData | Server Function 可直接作為 `<form action>`，React 自動傳遞 FormData 並支援 JS 未載入時仍可正常提交 | 原生支援 Progressive Enhancement，降低事件綁定負擔 |
| 前端需手動呼叫 API;若涉及多層請求，易產生網路瀑布 | 函數呼叫即是請求，Server Function 能直接存取後端，避免中間 API 路徑 | 減少延遲、降低重複傳輸開銷 |
| 表單邏輯、錯誤處理、驗證等皆須打包進前端 bundle | Server Function 端邏輯不會傳至前端,實現零額外 bundle 成本 | 明顯減少下載體積、提升初始載入速度 |

</div>

---

# SSR 和 RSC 之間的關係

RSC 沒有取代 SSR，而是補充了SSR，需要透過框架來整合，例如 Next.js

<div class="text-[16px]">

|  | server-side rendering | React Server Components |
|---------|----------------|----------|
| 組件 | 都是 client component | 預設是 server component，需要 Hooks、狀態 (State) 或 瀏覽器 API 的組件要用 'use client ' 標記成用戶端組件 |
| 序列化產物 | 將所有組件渲染成靜態 HTML 字串 (或串流)，用於首頁快速預覽 | 產生 RSC payload |
| 程式碼傳輸 | 所有組件的 JS Bundle 都會傳輸到用戶端 | 伺服器端組件的原始碼不會再傳到用戶端，RSC payload 包含了已渲染的 SC 結構結果、以及用戶端組件的模組參考作為 Placeholder |
| 水合 | 所有組件的程式碼都必須在用戶端下載並再次執行，以附加事件監聽器並恢復互動性 | 只有用戶端組件的程式碼會被下載，進行水合以實現互動性 |

</div>

---

# 章節重點回顧 — Takeaway

1. 伺服器組件（RSCs）把資料抓取與渲染移到伺服器：能減少前端 bundle 體積，並在首次載入就回傳已渲染的 HTML，提升效能與首次可見內容（TTFB/CLS）。

2. 嚴格區分 Server vs Client：有互動性的邏輯（如 useState、onClick、DOM 操作）必須放在用戶端組件（`"use client"`）；伺服器組件只處理可序列化的資料與靜態輸出。

3. 序列化與模組參考是關鍵：伺服器會把 React 元素樹序列化送到用戶端（replacer/parse 還原 $$typeof），對於 client component 以模組參考作為佔位，用戶端再載入對應 bundle 注入互動部分。

4. 軟導航（Soft Navigation）可降低全頁重載：攔截連結並只請求 JSX/元素樹，反序列化後用 root.render 更新畫面，保留應用狀態且更流暢。

5. Server Actions 與安全性：使用 `'use server'` 的伺服器函式讓用戶端能直接觸發伺服器邏輯（表單/資料修改），同時保持敏感資源在伺服器端，減少 client bundle 並利於漸進增強。

---

# 複習問題

<v-clicks>

1. React 伺服器組件的主要價值是什麼？
  - React 伺服器組件能讓應用程式更快、更輕，因為伺服器端執行的程式碼不會被下載到瀏覽器，減少 JavaScript 體積。
它可以直接在伺服器上存取資料庫、檔案或 API，效能更穩定，也可以用 async/await 處理非同步。
整體來說，就是把「渲染成本」和「程式碼體積」從用戶端轉移到伺服器上。

2. 用戶端組件能否匯入伺服器組件，為什麼？
  - 不行，因為伺服器組件只在伺服器上執行，用戶端環境（瀏覽器）根本沒有那些 Node.js 模組（例如 fs）。
當用戶端在執行時，伺服器渲染早就結束了，不能再回頭呼叫伺服器程式。
不過可以用「組合」的方式：讓伺服器組件在外層，把用戶端組件包進去，透過 props 傳遞內容。
3. 伺服器組件和傳統的 client-only app 之間有哪些取捨？
  - 伺服器組件適合不需要互動、只要呈現資料的部分。
如果組件需要狀態管理（useState）、事件（onClick）、DOM 操作或瀏覽器 API，就必須是用戶端組件。
要問自己：「這個組件在首次渲染後還需要做事嗎？」
如果答案是「要」（像監聽事件、用 localStorage、操作 DOM），那就不能放伺服器端。 

</v-clicks>

---

# 複習問題

<v-clicks>

4. 什麼是模組參考？React 在調和過程中如何處理它們？
  - React 在打包時會建立兩張模組圖：伺服器圖和用戶端圖。
當伺服器在渲染 RSC 樹時，遇到 client component（有 "use client"），它不會渲染內容，而是放一個「模組參考」。
這個參考告訴用戶端：「到這裡時，去載入某個 client bundle 裡的組件。」
也就是說，伺服器只算好靜態內容，把互動部分的位子標記起來，交給用戶端接手。
調和時，React 會根據這些模組參考，把用戶端的互動部分「注入」到伺服器輸出的靜態樹中，
最終合併成一棵完整、可互動的 React 樹。
5. 伺服器操作如何讓 React 應用程式更方便？
  - Server Actions 讓用戶端可以直接呼叫伺服器上的函式，就像呼叫本地的 async 函式一樣。
只要在函式上加 'use server'，框架會自動幫你建立 API 端點、處理序列化和網路請求。
它讓表單提交或按鈕動作可以直接觸發伺服器邏輯，不用手動寫 /api/... 路由。

</v-clicks>
