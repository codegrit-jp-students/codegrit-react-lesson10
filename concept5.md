# アプリの実装準備

## アプリの実装の流れ

クイズアプリは、次の4つの状態を順番に遷移していきます。

1. クイズが読み込まれる前の状態
2. クイズが読み込まれ、まだスタートする前の状態
3. クイズがスタートし設問に答えている状態
4. 設問に全て答えて、結果を見ている状態

この順番に沿ってアプリを実装していきましょう。

## 開発の準備をする

まずは、開発の準備をしましょう。いつものようにcreate-react-appでアプリを作成し、必要なライブラリをインストールします。

```js
$ npx create-react-app my-quiz-app
$ cd my-quiz-app
$ yarn add firebase node-sass react-bootstrap @emotion/core @emotion/styled @emotion/css
```

また、React Bootstrapを利用するために、CSSもダウンロードし、`bootstrap.min.css`を`src/styles/`フォルダ下に加えましょう。

[Bootstrap - Download](https://getbootstrap.com/docs/4.3/getting-started/download/)

ファイルを加えたら、index.js内にこのCSSをインポートします。

```js
// src/index.js
import './styles/bootstrap.min.css'
```

これで一旦の準備完了です。

## CSSを設置する

今回のアプリでは、React Bootstrapを利用します。React BootstrapにはCSSが含まれていないので、CSSを別途ダウンロードして配置する必要があります。

レッスン5で行ったのと全く同じ様にBootstrapのCSSをダウンロードし、`bootstrap.min.css`を`src/styles/`フォルダ下に加えましょう。

[Bootstrap - Download](https://getbootstrap.com/docs/4.3/getting-started/download/)

ファイルをダウンロードしたら、index.scss内(index.cssの名前を変えてください)にこのCSSをインポートします。

```js
// src/index.scss
@import './styles/bootstrap/bootstrap';
```

また、今回のアプリ用にBootstrapの変数をアップデートし、最終的に`index.scss`は以下のようになります。

```scss
$text-color: #887f95;
$headings-color: #352a3a;
$btn-primary-bg: #ad97ef;
$btn-primary-color: #fff;
$primary: #ad97ef;

@import './styles/bootstrap/bootstrap';
```

ここまで出来たら、index.scssをindex.js内でインポートしておきましょう。

```js
// src/index.js

import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';
import './index.scss';
import * as serviceWorker from './serviceWorker';

ReactDOM.render(<App />, document.getElementById('root'));

// If you want your app to work offline and load faster, you can change
// unregister() to register() below. Note this comes with some pitfalls.
// Learn more about service workers: https://bit.ly/CRA-PWA
serviceWorker.unregister();
```