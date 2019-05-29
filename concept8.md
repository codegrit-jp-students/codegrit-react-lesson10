# クイズ開始前までを実装しよう

## クイズ読み込み中の表示を実装する

今回のアプリではクイズは一個だけですが、一つのアプリとして考えたときに、複数のクイズからクイズを選べるようなアプリへと進化していくことが予測されます。

そのため、今回のアプリではクイズ一覧を一度に読み込み、最初のクイズ(テニスのクイズ)が自動的に選択されるという実装を行います。

クイズの一覧の取得は、componentDidMount内で行います。まずはここまでを実装してみましょう。

```js
import React, { Component } from 'react';
import PropTypes from 'prop-types';
import styled from '@emotion/styled/macro';
import { getAllQuizzes } from '../../lib/firebaseDb';

const Wrapper = styled.div({
  display: 'flex',
  justifyContent: 'center',
})

const QuizBox = styled.main({
  padding: 40,
  backgroundColor: '#faf7ff',
  border: '1px solid #B296F5',
  width: '100%',
  maxWidth: 900,
  height: 600,
  borderRadius: '10px',
})

export default class extends Component {
  state = {
    quizzes: null, // quizzesオブジェクトには全てのクイズを保存(今回のアプリは1個のみ)
    chosenQuizId: null, // 選択されたクイズのドキュメントID、今回は自動的にテニスのクイズを振り分ける。
    loading: true // 最初はクイズをロードするのでtrue
  }

  async componentDidMount() {
    const quizzes = {};
    /* getAllQuizzesはPromiseを返すのでasync/awaitでsnapshotを取得 */
    const snapshot = await getAllQuizzes();
    /* 
      snapshotオブジェクトに定義されているforEachファンクションを使います。
      ObjectのforEach文とは異なるので注意。
    */
    snapshot.forEach(doc => {
      quizzes[doc.id] = doc.data(); // quizzesオブジェクトにidをkeyにしてデータを入れます。
    })
    /* 
      将来的にクイズを選べるようなアプリになる余地を残しつつ、
      テニスクイズのIDを現在選ばれているIDとして保存しています。
    */
    const chosenQuizId = Object.keys(quizzes)[0];
    this.setState({
      chosenQuizId,
      quizzes,
      loading: false
    })
  }

  render() {
    const {
      loading
    } = this.state;
    if (loading) return (
      <Wrapper>
        <QuizBox>
          {/* <EmptyQuiz /> */}
        </QuizBox>
      </Wrapper>
    );
    // 一旦ローディング完了した後は何も表示しない。
    return <div/>
  }
}
```

次に、ローディング中のイメージを表示するためにEmptyQuizというコンポーネントを作成します。
ローディングイメージはこちらからダウンロード頂けます。

[loading.svg](https://firebasestorage.googleapis.com/v0/b/codegrit-images.appspot.com/o/codegrit-react%2FLesson10%2Floading.svg?alt=media&token=2564b8cc-6cb6-4ff2-8995-b1d04a9d9961)

```js
import { ReactComponent as Loader } from '../../images/loading.svg';
...省略

const Centered = styled.div({
  height: '100%',
  maxWidth: '100%',
  display: 'flex',
  flexDirection: 'column',
  justifyContent: 'center',
  alignItems: 'center'
})

export const EmptyQuiz = ({ sentence }) => (
  <Centered>
    <Loader height={60} width={60} />
    <p css={css`margin-top: 14px;`}>{sentence}</p>
  </Centered>
);

EmptyQuiz.propTypes = {
  sentence: PropTypes.string.isRequired
}

...省略
```

こちらはこれまでにも同じようなものを作ってきたのでおなじみかと思います。尚、EmptyQuizコンポーネントは、この後にクイズの設問一覧を読み込んでいるときにも利用するので、`export const EmptyQuiz`としています。

ここまでで出来たものを見てみましょう。Quizコンポーネントを編集してEmptyQuizコンポーネントを呼び出すことと、Topコンポーネント、Mainコーポネントを編集してQuizコンポーネントを呼び出すようにすることを忘れないようにしてください。

少しの間ローディング画面が表示された後に、何も表示されていない画面が出てくれば成功です。

## クイズがスタートする前の状態を実装する

では次にクイズが始まる前の状態を実装していきましょう。
まずは、スタートする前の状態のコンポーネントを`BeforeStart`という名前で作成します。

```js
// src/components/Quiz/index.js

/* 以下の2つの行を追加してください。 */
/** @jsx jsx */
import { jsx } from '@emotion/core';
...省略

const BeforeStart = ({quiz}) => (
  <Wrapper>
    <QuizBox>
      <Centered>
        <div css={css`
          text-align: center; 
          width: 70%;
        `}>
          <h2 css={css`margin-bottom: 0.5em;`}>{quiz.title}</h2>
          <p css={css`margin-bottom: 2em;`}>{quiz.description}</p>
          <Button 
            variant="outline-primary" 
            size='lg'>
            クイズスタート
          </Button>
        </div>
      </Centered>
    </QuizBox>
  </Wrapper>
);

BeforeStart.propTypes = {
  quiz: PropTypes.object.isRequired,
  handleStart: PropTypes.func.isRequired,
}

...省略
```

次にQuizコンポーネントを実装していきましょう。

Quizコンポーネントにはスタートする前の状態と、スタート後を判断するためにstatusというステートを追加します。後は、このステータスを元に先程作成した`BeforeStart`コンポーネントを呼び出します。

```js
...省略

export default class extends Component {
  state = {
    quizzes: null,
    chosenQuizId: null,
    status: 'beforeStart', // ステートを追加する
    loading: true
  }
  render() {
    const { 
      loading, 
      quizzes, 
      chosenQuizId, 
      status 
    } = this.state;
    if (loading) return (
      <Wrapper>
        <QuizBox>
          <EmptyQuiz sentence="クイズを読み込んでいます。" />
        </QuizBox>
      </Wrapper>
    );
    // 現在選ばれているIDを元にクイズのデータを取得しています。
    const chosenQuiz = quizzes[chosenQuizId];
    if (status === 'beforeStart') {
      return (
        <BeforeStart quiz={chosenQuiz} />
      );
    }
    return <div />
  }
}
```

次に、現状はクイズをスタートする前のファンクションがないので、Quizコンポーネントに`handleStart`ファンクションを追加しましょう。このファンクションは単純にstatusステートを変更するのみです。

```js
export default class extends Component {
  ...省略
  startQuiz = (e) => {
    e.preventDefault();
    this.setState({
      status: 'started'
    })
  }
  ...省略
}

```

`BeforeStart`コンポーネントへも、このファンクションを渡し、「クイズスタート」ボタンで呼び出します。

これでクイズが読み込まれた後に、クイズのタイトルと概要が表示されるところまでが完了しました。

![quiz-app-dev-2](https://firebasestorage.googleapis.com/v0/b/codegrit-images.appspot.com/o/codegrit-react%2FLesson10%2Fquiz-app-dev-2.png?alt=media&token=1e7b1b3a-ff1e-4019-9f44-e9c0c8bf0c9c)