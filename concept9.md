# クイズ開始後のコンポーネントの基礎を作ろう

## クイズ開始後のコンポーネントの実装をしていく

Quizのメイン部分を実装する前に全体の構成を見ていきましょう。ここまでは全てのコンポーネントをQuizコンポーネントに書いていきましたがメイン部分はいくつかのコンポーネントに分けて作成していきます。

作成するコンポーネントと役割は以下の3つです。

- QuizItemList - 設問のリスト
- QuizItem - 一つの設問と選択肢のリストを扱うコンポーネント
- QuizOption - 一つの選択肢を扱うコンポーネント

これらを順番に作っていきましょう。

## QuizItemListコンポーネントを作る

QuizItemListでは、componentDidMount内で、設問一覧を取得します。そのためのクエリを`firebaseDb.js`に追加しましょう。

サブコレクションを取得するには、まず、そのサブコレクションの上位のコレクション(あるいはサブコレクション)のドキュメントを指定し、最後にサブコレクションを指定します。

ドキュメントを指定するには、以下のようにします。

```js
db.collection("quizes") // クイズコレクション一覧へのリファレンス
  .doc(quizId) // 特定のドキュメントをIDへのリファレンスを指定して取得する
```

さて、上のコードでリファレンスという言葉が出てきました。リファレンスとは日本語で「参照」という意味です。Cloud Datastoreに関していうと取得するデータに対する参照という意味になります。

Cloud Datastoreに対してクエリを投げる際には、最終的に`get()`ファンクションを利用して`querySnapshot`オブジェクトを取得します。この`get()`が呼び出されるまでは、データの呼び出しは行われず、データへのリファレンスのみを作成します。

リファレンスは次に見ていくようにどんどんつなげていくことが出来ます。つなげていくことで、次々とサブコレクションやドキュメントへのリファレンスを直感的に作ることが出来ます。

また、設問一覧はorderの値を利用して並び替えます。firebaseでは`.orderBy`というファンクションが用意されており、これを利用します。

最終的に、設問一覧を取得するためのコードは以下のようになります。

```js
...省略
export const getAllQuizItems = (quizId) => {
  return db.collection("quizes")
    .doc(quizId) // 特定のクイズを指定
    .collection('quiz_items') // 設問サブコレクションへのリファレンス
    .orderBy("order", "asc") // orderの値を利用して設問を並べ替える。
    .get();
}
```

このファンクションを利用して、Quizコンポーネントと同様にデータを取得します。


```js
// src/components/QuizItemList/index.js

import React, { Component } from 'react';
import PropTypes from 'prop-types';
import styled from '@emotion/styled/macro';
// QuizコンポーネントでexportしたEmptyQuizをimportする。
import { EmptyQuiz } from '../Quiz';
import { getAllQuizItems } from '../../lib/firebaseDb';

export default class extends Component {

  static propTypes = {
    quizId: PropTypes.string.isRequired,
  }

  state = {
    quizItems: [], // 設問のデータを保存するための配列
    answers: [], // クイズでの回答一覧を保存するための配列
    /*
      設問一覧のロードを行うのでtrue。名称はloadingでも良いが、
      よりクイズ準備中であることを明確にしたいのでpreparingにしている。
    */
    preparing: true,
    step: 0, // 現在の設問のorderを保存する
    totalSteps: 0 // プログレスバーの表示のために設問数を保存する
  }

  async componentDidMount() {
    const { quizId } = this.props;
    const snapshot = await getAllQuizItems(quizId);
    /* forEachでない方法で設問のデータを取得します。*/
    const quizItems = snapshot.docs;
    this.setState({
      quizItems,
      totalSteps: quizItems.length,
      answers: new Array(quizItems.length),
      preparing: false
    })
  }

  render() {
    const { quizId } = this.props;
    const {
      preparing,
    } = this.state;
    const answer = answers[step];
    if (preparing) return <EmptyQuiz sentence="準備しています" />
    return <div />
  }
}
```

## QuizItemコンポーネントを作る

設問を読み込むところまでが出来たので、読み込んだ設問を表示するためのコンポーネントQuizItemを作成していきましょう。

QuizItemコンポーネントでは、QuizItemListと同じ様にQuizItemを元に選択肢一覧の読み込みを行います。まずは`firebaseDb.js`にこのためのファンクションを追加します。

```js
// src/lib/firebaseDb.js

export const getAllQuizOptions = (quizId, itemId) => {
  return db.collection("quizes")
    .doc(quizId)
    .collection('quiz_items')
    .doc(itemId) // クイズアイテムをIDで指定
    .collection('options') // サブコレクションを呼び出す
    .orderBy("order", "asc") // クイズアイテムと同様にorderで並べ替え
    .get();
}
```

次にQuizItem.jsを作成します。設問一覧の取得中ですが、ローダーは表示せず空のコンポーネントを表示しています。

ローディング表示はユーザーの感じる体感時間を短くするという目的があります。このコンポーネントの場合、既に設問のタイトルはコンポーネントに表示出来るため、短時間の設問の読み込みはユーザーから見えなくても問題ないと考えられます。そのため、あえて短時間のローダーを見るストレスを回避する目的があります。

これに関しては、アプリの改善を考える際にユーザーに直接どちらが良いか聞いて決めるなどの方法も取れるかと思います。

```js
// src/components/QuizItem/index.js

/** @jsx jsx */
import { Component } from 'react';
import PropTypes from 'prop-types';
import styled from '@emotion/styled/macro';
import css from '@emotion/css/macro';
import { jsx } from '@emotion/core'
import { getAllQuizOptions } from '../../lib/firebaseDb';

const MainWrapper = styled.div({
  margin: '30px 0'
})

export default class extends Component {
  static propTypes = {
    itemId: PropTypes.string.isRequired,
    quizId: PropTypes.string.isRequired,
    step: PropTypes.number.isRequired,
    item: PropTypes.object.isRequired,
    answer: PropTypes.string,
  }

  state = {
    options: [],
    loading: true,
  }

  async componentDidMount() {
    const { quizId, itemId } = this.props;
    const snapshot = await getAllQuizOptions(quizId, itemId);
    const options = snapshot.docs;
    this.setState({
      options,
      loading: false,
    })
  }

  render() {
    const { step, item } = this.props;
    const { loading } = this.state;
    let mainPart;
    if (loading) mainPart = <div/>;
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

## QuizOptionコンポーネントを作成する

さて、次に設問の選択肢を表示するためのQuizOptionコンポーネントを作成しましょう。

このコンポーネントは既に読み込まれたデータを表示するのみです。また選択肢が選択された場合にそれを反映するためのオプションや、選択イベントが起こった際の対応を行うファンクションをPropsとして渡します。

```js
import React from 'react';
import PropTypes from 'prop-types';
import styled from '@emotion/styled/macro';

const Wrapper = styled.div({
  padding: '15px',
  backgroundColor: '#fff',
  borderRadius: 10,
  cursor: 'pointer',
  border: '2px solid #fff',
  ':hover': {
    border: '2px solid #ad97ef'
  },
  fontSize: '1.2em',
  marginBottom: '1.2em'
}, ({ selected }) => { // 選択された選択肢はボーダーを表示する。
  const styles = [];
  if (selected) styles.push({ border: '2px solid #ad97ef' })
  return styles;
})

const QuizOption = ({ item, selected, selectOption }) => {
  return (
    <Wrapper
      selected={selected}
      onClick={(e) => selectOption(item.slug, e)}>
      <div>{item.order + 1}: {item.title}</div>
    </Wrapper>
  );
}

QuizOption.propTypes = {
  item: PropTypes.object.isRequired, // 選択肢のデータ
  selected: PropTypes.bool.isRequired, // この選択肢が選択されているかどうか
  selectOption: PropTypes.func.isRequired // 選択肢のクリックを扱うファンクション
}

export default QuizOption;
```