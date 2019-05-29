# 設問間の移動とプログレスバーを作ろう

## 設問の移動とプログレスバーの実装

次にすることは設問の移動とプログレスバーの表示です。これができればクイズの回答部分は完成です。

### QuizItemListの編集

- **stepNextとstepBackファンクションの実装**

QuizItemListでは、stepNextとstepBackファンクションを作成します。この2つのファンクションは、stepステートのアップデートを行います。

```js
// src/components/QuizItemList/index.js

...省略

export default class extends Component {
  ...省略

  stepBack = (e) => {
    e.preventDefault();
    this.setState(state => ({
      step: state.step - 1
    }))
  }

  stepNext = (e) => {
    e.preventDefault();
    this.setState(state => ({
      step: state.step + 1
    }))
  }

  ...省略
}
```

次にrenderファンクションを実装していきます。

- **renderファンクションの実装**

renderファンクションの中では、現在表示している設問がどの位置にあるかによってボタンの表示の変更と、`disabled`のステータスを変更します。

具体的には、以下の6つの状況があります。

1. 最初の設問で回答されていない
戻るボタン、次へボタンともにクリックできない。

2. 最初の設問で回答されている
戻るボタンはクリックできない、次へボタンはクリックできる。

3. 2番目以降の設問で、最後の設問でない、回答されていない
戻るボタンはクリック出来る、次へボタンはクリックできない。

4. 2番目以降の設問で、最後の設問でない、回答されている
戻るボタン、次へボタンともにクリックできる。

5. 最後の設問で回答されていない
次へではなく、「結果を見る」ボタンを表示する。クリックは出来ない。戻るボタンはクリック出来る。

6. 最後の設問で回答されている
次へではなく、「結果を見る」ボタンを表示する。両方のボタンがクリック出来る。

この6つの状況を元に、renderファンクションを実装します。

```js
// src/components/QuizItemList/index.js

...省略
export default class extends Component {
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
    if (step !== 0) preButton = (
      <Button onClick={this.stepBack}
        variant="outline-secondary">戻る</Button>
    );
    let nextButton;
    if (step + 1 === totalSteps) {
      const isDisabled = !!(typeof answer === 'undefined')
      nextButton = <Button 
        onClick={this.finalize}
        disabled={isDisabled}
        variant="outline-primary">結果を見る</Button>
    } else {
      const isDisabled = !!(typeof answer === 'undefined')
      nextButton = <Button 
        onClick={this.stepNext}
        disabled={isDisabled} 
        variant="outline-primary">次へ</Button>
    }
    return (
      <Wrapper>
        <QuizItem 
          selectOption={this.handleUpdateAnswer}
          answer={answer}
          quizId={quizId}
          step={step} 
          itemId={quizItems[step].id}
          item={quizItems[step].data()} />
        <ButtonsWrapper>
          {preButton}
          {nextButton}
        </ButtonsWrapper>
      </Wrapper>
    );
  }
}
```

### QuizItemコンポーネントの編集

さて、これだけを実装しても上手くいきません。stepが変わってもQuizItemコンポーネントで表示する選択肢の内容が変更されないからです。

そのため、QuizItemコンポーネントでは`componentDidUpdate`を利用して、渡される`quizItem`のIDが変更された際に、選択肢のデータを取得するようにしなければなりません。

ここで`componentDidMount`内と`componentDidUpdate`内では、同様にデータの読み込み、及びステートの変更を行うので、この処理は別の`loadDataAndSet`に移動します。

これを実装していきましょう。

```js
// src/components/QuizItem/index.js
...省略

export default class extends Component {

  ...省略

  componentDidMount() {
    this.loadDataAndSet();
  }

  async componentDidUpdate(prevProps) {
    const { itemId } = this.props;
    // 渡されるitemIdが変わった場合に、データ取得を行う。
    if (prevProps.itemId !== itemId) {
      // 読み込み前に、選択肢のデータを空にし、ロード中にステートを変更する。
      this.setState({
        options: [],
        loading: true,
      })
      this.loadDataAndSet();
    }
  }

  loadDataAndSet = async () => {
    const { quizId, itemId } = this.props;
    const snapshot = await getAllQuizOptions(quizId, itemId);
    const options = snapshot.docs;
    this.setState({
      options,
      loading: false,
    })
  }
  ...省略
}
```

次にプログレスバーの表示です。プログレスバーはReact Bootstrapに用意されているものを利用するので簡単に実装出来てしまいます。

```js
// src/components/QuizItemList/index.js

import { ProgressBar, Button } from 'react-bootstrap';

...省略

export default class extends Component {
  ...省略

  render() {
    const { quizId } = this.props;
    const { 
      preparing, 
      step, 
      totalSteps,
      quizItems,
      answers,
    } = this.state;

    ...省略

    /* 
      React Bootstrapのプログレスバーはnowというpropsにパーセンテージを入力します。
      パーセンテージは現在表示している設問の順番と、設問全部の数から得られます。
    */
    const percentage = Math.round(step / totalSteps * 100);
    return (
      <Wrapper>
        <ProgressBar now={percentage} striped variant="primary" />
        <QuizItem 
          selectOption={this.handleUpdateAnswer}
          answer={answer}
          quizId={quizId}
          step={step} 
          itemId={quizItems[step].id}
          item={quizItems[step].data()} />
        <ButtonsWrapper>
          {preButton}
          {nextButton}
        </ButtonsWrapper>
      </Wrapper>
    );
  }
}

```

では最後にクイズ結果ページを作成し、アプリを完成させましょう。