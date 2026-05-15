# Geoboard Pro (智能釘板)

A production-ready, interactive digital geoboard designed for math education. Built with a Single-file Architecture (單檔架構), it runs directly in any modern browser without Webpack, Node.js, or any Build Tools (打包工具).

## 🚀 Core Features (核心功能)

* **Zero-config Deployment (零配置部署)**: HTML 內嵌 React、Babel 及 Tailwind CSS，Plug & Play。
* **Mobile-first Touch Physics (行動端觸控物理)**: 針對 iOS/Android 觸控優化，支援流暢嘅 Drag & Drop (拖拽)，無 Double-tap Zoom (雙擊放大) 畫面干擾。
* **Dynamic SVG Rendering (動態向量渲染)**: 
    * Unit Loop (水滴形): `A` 及 `Q` 弧線模擬橡皮圈掛釘物理重力。
    * Stretched Band (兩點連線): Capsule Path (膠囊形) 模擬中空拉伸張力。
* **Board Variations (釘板變體)**: 支援 3x3、4x4、5x5、10x10，以及具備橫向 Offset (偏移) 嘅 Isometric (等邊三角) 佈局。
* **Interactive Fill (互動填色)**: 獨立 Palette (調色盤) 設定 Fill Color (填充色)，支援透明度疊加。
* **I18n Localization (多語言支援)**: 內建 ENG (英文)、繁 (繁體)、简 (簡體) 即時切換。
* **Canvas Export (畫布輸出)**: 透過 `XMLSerializer` 將 SVG 轉換為 1000x1000 高清 PNG 下載。

## 🧠 Underlying Logic (底層邏輯)

系統設計極度著重 Error Prevention (防呆) 及 Rendering Optimization (渲染優化)，以下為處理核心 Interaction (互動) 嘅機制：

| 模組 | 潛在問題 (Pain Points) | 解決方案 (Solutions) |
| :--- | :--- | :--- |
| **Touch Continuity (觸控連續性)** | React 條件渲染 (`isStretched && <g>`) 會導致 DOM Node Unmount (卸載)，觸發 Mobile Browser 強制 Cut 斷 `touchcancel`，令第一下 Drag 卡死。 | **DOM Retention (節點保留)**: 放棄條件渲染，將水滴形、兩點連線、多邊形同時 Render。利用 `opacity: 0` 及 `pointerEvents: 'none'` 控制顯示層，確保觸控目標永遠留喺 DOM 內。 |
| **Event Overlapping (事件重疊)** | Edge Hitbox (邊線判定區) 佔用 12px，容易覆蓋 Peg (釘子) 上方嘅 Vertex (頂點)，導致「想抆走角」變成「拉出新角」。 | **Event Hijacking (事件劫持)**: 喺 `handleStart` 加入 Distance Threshold (距離閾值)。點擊位置距離頂點 $\le 3\text{px}$ 時，強制攔截並判斷為 Vertex 操作。 |
| **Hitbox Isolation (判定區隔離)** | 實體 SVG Path `<polygon>` 會攔截點擊，或者因為 `fill="none"` 導致無法點擊線條中心。 | **Dual Path Rendering (雙軌渲染)**: 一條 `strokeWidth="16"` 嘅透明 `<path>` 專責接收 Events，另一條 `strokeWidth="2.5"` 負責 Visual (視覺) 呈現。 |
| **Memory Leak (內存溢出)** | 喺 iPad WebKit 上，`backdrop-blur` 疊加複雜 SVG 會觸發 GPU Rendering Crash (白屏)。 | 棄用 CSS 濾鏡，改用純色 `bg-black/60` Overlay (遮罩)，確保舊設備極致流暢。 |

## 🛠 Tech Stack (技術棧)

* **Core**: React 18 (UMD CDN)
* **Styling**: Tailwind CSS (CDN)
* **Icons**: Inline SVG Components (無外部依賴)
* **Transpiler**: Babel Standalone (In-browser JSX compilation)

## ⚡ Quick Start (快速啟動)

無須 `npm install`。

1. Clone repo 或直接下載 `index.html`。
2. 用任何現代 Browser (Chrome, Safari, Edge) 打開 `index.html` 即可使用。