# チャレンジ10

## 目的

- アプリの継続的アップデートを体験する

## チャレンジの取り組み方

1. スターターファイルのリポジトリを、フォークして自分のGitHubアカウントにリポジトリを作りましょう。
2. マイルストーンごとに要件に合うようにファイルを編集していきます。
3. 分からない部分があれば、テキストを復習して、再度チャレンジしてみましょう。
4. 再チャレンジしてしばらく考えても分からない場合はチャットでメンターに質問しましょう。
5. 完了したら、GitHubリポジトリをメンターにシェアしてください。(質問時に既にシェアしている場合は、レビューを直接依頼しましょう。)
6. メンターから課題レビューが届きます。
7. ビデオチャットの際は、分からない点を更に突っ込んで聞いたり、より良い書き方を聞いてみましょう。

## 概要

レッスン10ではクイズアプリを作成しました。このアプリでは、レッスン内で説明しましたが、将来的に複数のクイズからクイズを選択出来るような余地を残す設計をしました。このレッスンでは作成したアプリをアップデートしていくことで、複数のクイズからクイズを選択出来るようにしましょう。クイズはデータを入れるのが大変かと思いますので1つ追加するだけで構いません。

## 完成イメージ

![codegrit-react-ch10-image.gif](https://firebasestorage.googleapis.com/v0/b/codegrit-images.appspot.com/o/codegrit-react%2FLesson10%2Fcodegrit-react-ch10-image.gif?alt=media&token=97d1f54b-1e00-4c49-ba15-c3aa73033961)

## スターターファイル

- [codegrit-jp-students/codegrit-react-ch10-student](https://github.com/codegrit-jp-students/codegrit-react-ch10-student)

スターターファイルは用意しておりますが、レッスン10で作成したものと同一のものですので、自分で作成したコードを利用することをおすすめします。

上記のURL先のリポジトリをフォークして、コードを書き進めて行きましょう。

## マイルストーン１

### 要件

- Cloud Datastoreにクイズの情報を登録する。(自分でクイズを考えても構いません。)

1. quizコレクション

```js
{
  title: 'ワンピース好きなら知っていて当然？ キャラクタークイズ',
  description: '国民的マンガワンピースの登場キャラクターに関するクイズです。あなたは全問解けるかな？'
}
```

2. quiz_itemsサブコレクション

```js
[
  {
    order: 0,
    answer_slug: 'creek',
    title: 'ワンピースの出発の舞台であるイーストブルーでルフィ一味が戦った最後の相手は？'
  },
  {
    order: 1,
    answer_slug: 'buggy',
    title: '王下七武海の初期のメンバーに入っていないのは次の内どの人？'
  },
  {
    order: 2,
    answer_slug: '100m',
    title: 'ナミがまだ魚人海賊団の一員だったとき、自由になるために必要だった金額はいくら？'
  },
  {
    order: 3,
    answer_slug: 'taiyou',
    title: 'ルフィを含めて最悪の世代と呼ばれるメンバーが所属してない海賊団は次のうちどれ？'
  }
]
```

3. optionsサブコレクション

```js
quiz_item_0_options = [
  {
    order: 0,
    slug: 'black_cat',
    title: 'クロネコ海賊団'
  },
  {
    order: 1,
    slug: 'buggy',
    title: 'バギー海賊団'
  },
  {
    order: 2,
    slug: 'creek',
    title: 'クリーク海賊団'
  },
  {
    order: 3,
    slug: 'gyojin',
    title: '魚人海賊団'
  }
]

quiz_item_1_options = [
  {
    order: 0,
    slug: 'hancock',
    title: 'ボア・ハンコック'
  },
  {
    order: 1,
    slug: 'jimbe',
    title: 'ジンベエ'
  },
  {
    order: 2,
    slug: 'buggy',
    title: 'バギー'
  },
  {
    order: 3,
    slug: 'mihawk',
    title: 'ジュラキュール・ミホーク'
  }
]

quiz_item_2_options = [
  {
    order: 0,
    slug: '10m',
    title: '1000万ベリー'
  },
  {
    order: 1,
    slug: '100m',
    title: '1億ベリー'
  },
  {
    order: 2,
    slug: '500m',
    title: '5億ベリー'
  },
  {
    order: 3,
    slug: '1b',
    title: '10億ベリー'
  }
]

quiz_item_3_options = [
  {
    order: 0,
    slug: 'drake',
    title: 'ドレーク海賊団'
  },
  {
    order: 1,
    slug: 'bonny',
    title: 'ボニー海賊団'
  },
  {
    order: 2,
    slug: 'heart',
    title: 'ハートの海賊団'
  },
  {
    order: 3,
    slug: 'taiyou',
    title: 'タイヨウの海賊団'
  }
]
```

- QuizSelectorコンポーネントを作成する

QuizSelectorコンポーネントは、Quizコンポーネントでstatusが"beforeStart"のときにのみ表示します。そのため`BeforeStart`コンポーネントの中で、QuizSelectorコンポーネントを呼び出すようにします。

- QuizSelectorItemコンポーネントを作成する

選択できるそれぞれのクイズを表示するためにQuizSelectorItemコンポーネントを作成しましょう。

- selectQuizファンクションを実装する

クイズが選択されたときに、chosenQuizIdステートをアップデートして、Quizボックスに表示するクイズを変更しましょう。


## 評価

課題の後、以下の２つについてメンターにフィードバックをお願いします。

1. 要件のカバー度: 1.全く出来なかった 2.ほとんど出来なかった 3. 半分ほどは出来た 4.8割ほどは出来た 5. 全部出来た
2. 難易度: 1. とても難しかった 2. 難しかった 3. ちょうど良かった 4. 簡単だった 5. とても簡単だった
