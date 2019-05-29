# アプリを完成させよう

## クイズ結果ページを実装する

クイズの結果表示ページは、メイン部分に比べると簡単です。

まずは、QuizItemListコンポーネント内で「結果を見る」ボタンのクリック後にanswersステートに保存されているslugと各設問のanswer_slugとを比較して正答数をnumCorrectというステートに保存します。

まずは、正答数(numCorect)と設問数(totalSteps)を元に結果を表示する、`QuizFinished`コンポーネントを作成しましょう。

### QuizFinishedコンポーネントの実装

QuizFinishedコンポーネントでは、クイズの最初に戻るボタンがあります。ボタンを押すとPropsから渡された、resetQuizファンクションを呼び出しますが、このボタンの実装は後ほど行います。

```js
// src/components/QuizFinished/index.js

import React from 'react';
import PropTypes from 'prop-types';
import styled from '@emotion/styled/macro';
import { Button } from 'react-bootstrap';

const Wrapper = styled.div({
  display: 'flex',
  flexDirection: 'column',
  justifyContent: 'center',
  alignItems: 'center',
  height: '100%',
  width: '100%'
})

const DataWrapper = styled.div({
  margin: '15px 0',
  display: 'flex',
  flexDirection: 'column',
  justifyContent: 'center'
})

const Data = styled.h3({
  fontSize: '36px',
  weight: 'bold',
  letterSpacing: '0.1em',
})

const MainMessage = styled.h1({
  fontSize: '60px',
  color: '#ff94c5',
  fontWeight: 'bold',
  marginBottom: 40
})

const ButtonWrapper = styled.div({
  marginTop: 40
})

const QuizFinished = ({ totalSteps, numCorrect, resetQuiz }) => {
  const correctRate = Math.floor(numCorrect / totalSteps * 100)
  let mainMessage;
  if (correctRate === 100) {
    mainMessage = "すごい、全問正解！"
  } else if (correctRate >= 60) {
    mainMessage = "あぁ、惜しい！"
  } else {
    mainMessage = "うーん、もう少し！"
  }
  return (
    <Wrapper>
      <MainMessage>{mainMessage}</MainMessage>
      <DataWrapper>
        <Data>{totalSteps}問中、{numCorrect}問正解</Data>
      </DataWrapper>
      <DataWrapper>
        <Data>正答率{correctRate}%</Data>
      </DataWrapper>
      <ButtonWrapper>
        <Button onClick={resetQuiz} variant="outline-primary" size="lg">最初に戻る</Button>
      </ButtonWrapper>
    </Wrapper>
  );
}

QuizFinished.propTypes = {
  totalSteps: PropTypes.number.isRequired,
  numCorrect: PropTypes.number.isRequired,
  resetQuiz: PropTypes.func.isRequired
}

export default QuizFinished;
```

### QuizItemListコンポーネントの編集

クイズの正答数の確認は`finalize`ファンクションで行います。
またrenderファンクションをアップデートして、finalizeファンクションが呼び出された後に先程作成した`QuizFinished`ファンクションを呼び出します。

```js
// src/components/QuizItemList/index.js

...省略

export default class extends Component {

  state = {
    quizItems: [],
    answers: [],
    preparing: true,
    step: 0,
    totalSteps: 0,
    numCorrect: 0, // 正答数を保存
    finished: false, // 回答完了したかどうか
  }

  finalize = (e) => {
    e.preventDefault();
    const { answers, quizItems } = this.state;
    let numCorrect = 0;
    answers.forEach((a, i) => {
      const correctSlug = quizItems[i].data().answer_slug;
      if (a === correctSlug) numCorrect++;
    })
    this.setState(state => ({
      numCorrect,
      finished: true,
      step: state.step + 1
    }))
  }

  resetQuiz() {
    // 後ほど実装します
  }

  ...省略

  render() {
    const { quizId } = this.props;
    const {
      preparing,
      step,
      totalSteps,
      quizItems,
      answers,
      finished,
      numCorrect,
    } = this.state;
    const answer = answers[step];
    if (preparing) return <EmptyQuiz sentence="準備しています" />
    let preButton = <Button disabled={true} variant="outline-secondary">戻る</Button>
    // stepは0からスタートしているので、step === totalStepsは設問すべて答えられた後の状態を意味します。
    if (finished && step === totalSteps) {
      return (
        <QuizFinished
          resetQuiz={this.resetQuiz}
          totalSteps={totalSteps}
          numCorrect={numCorrect} />
      );
    }
    ...省略
  }
}
```

最後にresetQuizファンクションを実装しましょう。resetQuizファンクションは、QuizItemListとQuizの2つのコンポーネントを変更する必要があります。行うこと自体はstateを初期化するだけですので難しくありません。

まずはQuizItemListコンポーネントから実装していきましょう。

```js
// src/components/QuizItemList/index.js

resetQuiz = (e) => {
  e.preventDefault();
  // コンポーネントのステートを最初の状態に戻します。
  this.setState({
    quizItems: [],
    answers: [],
    preparing: true,
    step: 0,
    totalSteps: 0,
    numCorrect: 0,
    finished: false,
  })
  // Quizコンポーネントから渡されたresetQuizファンクションを呼び出します。
  this.props.resetQuiz();
}
```

次にQuizコンポーネントに同じ様にファンクションを追加します。

```js
// src/components/Quiz/index.js

...省略

export default class extends Component {
  ...省略
  resetQuiz = () => {
    this.setState({
      status: 'beforeStart'
    })
  }

  render() {
    ...省略
    if (status === 'started') {
      return (
        <Wrapper>
          <QuizBox>
            <QuizItemList 
              resetQuiz={this.resetQuiz}
              quizId={chosenQuizId}
              quiz={chosenQuiz} />
          </QuizBox>
        </Wrapper>
      );
    }
  }
}
```

これで全てのコンポーネントが完成しました。結果を確認してみましょう。公開されているアプリと同じ挙動をしているでしょうか? 

最後にこのレッスンで作成したアプリのコードは以下のリポジトリでご確認頂けます。もし上手くいかない場合はこちらのリポジトリを参考に修正を行ってください。

- [codegrit-react-lesson10-myquiz-app](https://github.com/codegrit-jp-students/codegrit-react-lesson10-myquiz-app)

次回のレッスンでは、作成したアプリケーションをFirebaseを利用して世界に向けて公開します。

## 更に学ぼう

- [Firebase公式ドキュメント](https://firebase.google.com/docs/firestore)
ドキュメントはメニューから日本語表示に出来ます。