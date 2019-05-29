# メイン以外の部分を実装しよう

## TOPページを作成する

さて、次に作成していくのはTOPページです。今回作成するのは1ページのみのアプリなのですが、将来的に複数のページを持つアプリになることも想定して、Topコンポーネントからヘッダー、フッター、そしてメインの部分を呼び出すようにします。

まずはステートを何も持たないHeader、Footerコンポーネントを作成しましょう。

### Topコンポーネントの準備

HeaderとFooterはTopコンポーネントから呼び出すので、まずは空のTopコンポーネントを作成し、App.jsからTop.jsを呼び出すようにしましょう。

```js
// src/Top.js
import React, { Component } from 'react'

class Top extends Component {
  render() {
    return <div/>
  }
}

export default Top;
```

```js
// src/App.js
import React from 'react';
import Top from './Top';

function App() {
  return (
    <div className="App">
      <Top />
    </div>
  );
}

export default App;
```

空のコンポーネントが出来たのでHeaderとFooterを作成します。

### Headerコンポーネントの作成

ヘッダーにはロゴを中心に配置しています。ロゴはこちらからダウンロード頂けます。

[myquiz.svg](https://firebasestorage.googleapis.com/v0/b/codegrit-images.appspot.com/o/codegrit-react%2FLesson10%2Fmyquiz.svg?alt=media&token=d9a20ca6-3ca3-41a9-9ae1-7c06b9547443)

イメージはsrc直下にimgというフォルダーを作成しダウンロードした画像を入れましょう。

次に`components`というフォルダーをsrc直下に作成しましょう。その中に`Header`というフォルダーを作成し、`index.js`というファイルを作りましょう。このファイルを作成していきます。

SVGをインポートする方法は覚えているでしょうか？ 以下のようにします。

```js
import { ReactComponent as Logo } from './images/myquiz.svg'
```

では、React Bootstrapのグリッドも利用しつつHeaderコンポーネントを作成します。ヘッダーの高さは50pxで、`border-bottom`でメイン部分と分けます。

```js
/** @jsx jsx */
import React from 'react';
import { Container, Row, Col } from 'react-bootstrap'
import { jsx } from '@emotion/core'
import { ReactComponent as Logo } from '../../images/myquiz.svg'

const Header = () => (
  <div css={{
    height: '50px',
    width: '100%',
    borderBottom: '1px solid rgb(228, 228, 228)'
  }}>
    <Container>
      <Row>
        <Col>
          <div css={{
            display: 'flex',
            justifyContent: 'center',
            alignItems: 'center',
          }}>
            <div className="logo-wrapper" css={{ padding: '11px 0' }}>
              <a href="/">
                <Logo height='28px' width='95px' />
              </a>
            </div>
          </div>
        </Col>
      </Row>
    </Container>
  </div>
)

export default Header
```

### フッター

つぎにフッターです。フッターもヘッダーとほぼ同じで、高さ50pxで"Made for CodeGrit React Course"という文章を中央に配置し、メイン部分と`border-top`で分けます。Headerと同じ様に`Footer`というフォルダーを作成し、index.jsを作成します。

```js
// src/components/Footer/index.js

/** @jsx jsx */
import React from 'react';
import { Container, Row, Col } from 'react-bootstrap';
import { jsx } from '@emotion/core';

const Footer = () => (
  <div css={{ 
    height: '50px', 
    width: '100%', 
    borderTop: '1px solid rgb(228, 228, 228)' }}>
    <Container>
      <Row>
        <Col>
          <div css={{
            display: 'flex',
            justifyContent: 'center',
            alignItems: 'center',
            padding: '11px 0'
          }}>
            <span>
              Made for CodeGrit React Course
            </span>
          </div>
        </Col>
      </Row>
    </Container>
  </div>
)

export default Footer
```

### TopコンポーネントにHeaderとFooterを配置する。

ヘッダー部分とフッター部分が出来たので、Topコンポーネントから呼び出すようにしましょう。

```js
// src/Top.js

import React, { Component } from 'react';
import Header from './components/Header';
import Footer from './components/Footer';

class Top extends Component {
  render() {
    return (
      <div>
        <Header />
        <Footer />
      </div>
    );
  }
}

export default Top;
```

ここまでを表示してみましょう。次のように見えていればオーケーです。

![quiz-app-dev-1](https://firebasestorage.googleapis.com/v0/b/codegrit-images.appspot.com/o/codegrit-react%2FLesson10%2Fquiz-app-dev-1.png?alt=media&token=13df00b3-08da-4ca0-83d0-ad40e9297889)

### メイン

Quizコンポーネントは、Mainコンポーネントから呼び出しますのでまずは空のMainコンポーネントを作成しましょう。

```js
import React, { Component } from 'react';
import styled from '@emotion/styled/macro';
import { Container, Row, Col } from 'react-bootstrap';

const Wrapper = styled.main({
  minHeight: '400px', // 最低の高さ
  margin: '50px 0', // ヘッダーとフッターの間に50pxのmarginを設定
  height: '100%',
})

class Main extends Component {
  render() {
    return (
      <Wrapper>
        <Container>
          {/* 
            画面中央にクイズ部分を置く。
            justify-content-md-centerは、グリッドのカラムを中央に置くためのクラスでbootstrapのcssに定義されている.
          */}
          <Row className="justify-content-md-center">
            <Col md={9} sm={12}>
              {/* <Quiz /> */}
            </Col>
          </Row>
        </Container>
      </Wrapper>
    );
  }
}

export default Main;
```