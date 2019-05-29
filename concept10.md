# 設問の表示を実装しよう

## 最初の設問を表示する

さて、ここからは最初の設問の表示と選択肢の選択までを実装していきましょう。

### QuizItemListコンポーネントの編集

QuizItemListコンポーネントでは、`answers`ステートを利用して回答のデータの保持をしてます。選択肢がクリックされたときにこのステートをアップデートするために`handleChooseAnser`ファンクションを実装し、QuizItemとQuizOptionに対してこのファンクションを渡していきます。

```js
// src/components/QuizItemList/index.js

export default class extends Component {

  // 答え合わせのときのために選択肢のデータのslugを保存する。
  handleUpdateAnswer = (slug, e=null) => {
    if (e) e.preventDefault();
    let { answers, step} = this.state;
    answers[step] = slug
    this.setState({
      answers
    })
  }

  render() {
    const { quizId } = this.props;
    const {
      preparing,
      step,
      quizItems,
      answers,
    } = this.state;
    const answer = answers[step];
    return (
      <Wrapper>
        {/* 
          QuizItemコンポーネントを呼び出します。
        */}
        <QuizItem
          selectOption={this.handleUpdateAnswer}
          answer={answer}
          quizId={quizId}
          step={step}
          itemId={quizItems[step].id}
          item={quizItems[step].data()} />
      </Wrapper>
    );
  }
}
```

### QuizItemコンポーネントの編集

```js
// src/componenets/QuizItem/index.js

export default class extends Component {
  static propTypes = {
    itemId: PropTypes.string.isRequired,
    quizId: PropTypes.string.isRequired,
    step: PropTypes.number.isRequired,
    item: PropTypes.object.isRequired,
    answer: PropTypes.string,
    selectOption: PropTypes.func.isRequired
  }

  ...省略

  render() {
    const { step, item, answer, selectOption } = this.props;
    const { loading, options } = this.state;
    let mainPart;
    if (loading) mainPart = <div/>
    // 選択が既にされているかどうか
    const hasAnswer = typeof answer !== 'undefined';
    // 選択肢のデータのリストを元にQuizOptionコンポーネントを呼び出す。
    mainPart = options.map((option) => {
      const data = option.data();
      const selected = (hasAnswer && answer === data.slug); // 選択されているかどうかの判断
      return (
        <QuizOption
          key={`option-${option.id}`}
          selectOption={selectOption}
          item={data}
          selected={selected} />
      );
    });
    return (
      <div>
        <h3 css={css`margin-top: 30px;`}>第{step + 1}問: {item.title}</h3>
        <MainWrapper>
          {mainPart}
        </MainWrapper>
      </div>
    );
  }
}
```

QuizOptionは既に完成されているので変更は必要ありません。

最後にQuizコンポーネントからQuizItemListを呼び出すようにすればここまでは完成です。

```js
// src/components/Quiz/index.js
省略...

// Quizコンポーネントのrenderファンクションを編集します。
render() {
  省略...
  if (status === 'started') {
  return (
    <Wrapper>
      <QuizBox>
        <QuizItemList
          quizId={chosenQuizId}
          quiz={chosenQuiz} />
      </QuizBox>
    </Wrapper>
  );
}
```

ではここまでを実際に確認してみましょう。きちんと最初の設問と選択肢一覧が表示されているでしょうか？ また選択肢をクリックしたときに回答がちゃんと保存されているでしょうか？