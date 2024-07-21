# React + Vite + Tailwindcss, with Bunjs

## プロジェクトの作成

```bash
# プロジェクト作成
bun create vite@latest any_project_name

# 対話での選択は次の二つ
✔ Select a framework: › React
✔ Select a variant: › JavaScript + SWC

# プロジェクトに最低限のパッケージをインストール
cd any_project_name
bun install
```

---

## tailwind の導入と設定

```bash
# tailwdind をインストール
bun add -D tailwindcss autoprefixer
# 設定ファイルを作成
bunx tailwindcss init
```

tailwindcss 公式では postcss も導入したうえで postcss 用の設定ファイルも作成するが、vite.js が既に postcss に依存しており(インストール済みで)、postcss 用の設定も vite.config 内に書けるので、今回は上記の手順でいく。ファイル数を減らしたい。

逆に、tailwindcss 公式の手順なら、設定ファイルの編集手順が少ない、というのが利点だとは思う。

### vite.config.js に設定を追加

下記のように postcss 用の設定を追加する。

```js
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react-swc'

// ↓ 【追加A】下記2行。
import tailwindcss from "tailwindcss";
import autoprefixer from "autoprefixer";

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [react()],
  // ↓ 【追加B】css 設定オブジェクト
  css: {
    postcss: {
      plugins: [tailwindcss, autoprefixer],
    },
  },
})
```

### tailwind.config.js を編集

content 下の配列に、tailwindcss に参照させるファイル群を指定。

ここに指定したファイル上に文字列として登場した tailwind 用 class名が、ビルド時に css ファイルへ出力されることになる。

逆に、ここに指定していないファイルに tailwind 用 class 名をどれだけ記述しようとも、一切、反映されない。

```js
export default {
  // 【修正】: content 配列に要素を追加。
  content: [
    "./index.html",
    "./src/**/*.{js,ts,jsx,tsx}",
  ],
  // (以下略)
}
```

### src/index.css ファイル

```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

### src/App.jsx

```jsx
export default function App() {
  return <h1 className="text-3xl font-bold underline">Hello world!</h1>;
}
```

### 現在のディレクトリ構造

`tree`コマンドを打ち(-I でnode_modules を除外)、下記のように出力されればOK。不要ファイルやディレクトリは適当に削除。

```bash
tree -I "node_modules"

# 下記のように出力。
.
├── README.md
├── bun.lockb
├── index.html
├── package.json
├── public
├── src
│   ├── App.jsx
│   ├── index.css
│   └── main.jsx
├── tailwind.config.js
└── vite.config.js
```

### 開発サーバーを起動

```bash
bun run dev
```
