# Cloud Datastoreにデータを保存しよう

## 実際にデータを保存してみよう

### データベースを作成しよう

さて、クイズアプリがどんな感じのデータを持つかイメージが出来たら、実際にデータをFirebase上で入れていきましょう。
まずは、データベースを作成する必要があります。Firebaseのコンソール内の左側のメニューで`Database`という項目をクリックすると以下のような画面が開きます。(Realtime Databaseを選ばないように注意してください。)

![create-firebase-database-01](https://firebasestorage.googleapis.com/v0/b/codegrit-images.appspot.com/o/codegrit-react%2FLesson10%2Fcreate-firebase-database-01-2.png?alt=media&token=43683aee-65a9-43da-a9c5-8e4474621a7e)

次に`Create database`というボタンを作成すると、以下のようなモデルが立ち上がりますので、`Start in test mode`を選びデータベースを作成しましょう。

すると以下の画像のような画面が出てきます。

![create-firebase-database-02](https://firebasestorage.googleapis.com/v0/b/codegrit-images.appspot.com/o/codegrit-react%2FLesson10%2Fcreate-firebase-database-02.png?alt=media&token=837b76d0-292a-4bb3-a009-aeb021eeaeb6)

ここでは、一旦選択肢の下を選んで大丈夫です。警告が表示されますが今は無視して頂いて大丈夫です。レッスン11でこのファイルの設定方法について説明しています。

Enableをクリックするとデータベースが作成され、以下のような画面が表示されるはずです。

![create-firebase-database-03](https://firebasestorage.googleapis.com/v0/b/codegrit-images.appspot.com/o/codegrit-react%2FLesson10%2Fcreate-firebase-database-03-2.png?alt=media&token=5a0f007c-dd03-4e93-8d52-937527693dc2)

### Quizzesコレクションの作成

まずはQuizzesコレクションを作成してきましょう。`Add collection`という項目をクリックをして、最初にコレクションの名前を設定します。"quizzes"とquizの複数形を登録しましょう。(データベースでは一般的に複数形かつ小文字で名前をつけます。)

すると、最初のクイズドキュメントを登録することが出来る画面が出てきます。

![add-data-01](https://firebasestorage.googleapis.com/v0/b/codegrit-images.appspot.com/o/codegrit-react%2FLesson10%2Fadd-data-01.png?alt=media&token=9a070b3f-0ced-42fc-86da-3d576b1722bc)

この画面で、typeという項目と`string`という文字があるのが分かるでしょうか？ これはデータ型です。JavaScriptのデータが型を持つのと同じようにデータベースのデータも型を持っています。

### Cloud Firestoreのデータ型

Firestoreで利用できるデータ型の内今回利用するのは以下の2つのみです。

- string - 文字列
- number - 数字

その他、`サブコレクションとマップ`の項目で説明したmapや、配列を保存出来るarray、正誤値を入れるbooleanなどの型が用意されています。今回のレッスンでは、これらのデータ型には詳しくは説明しませんが、実際に自分でFirebaseを利用するときにドキュメントを見ることをおすすめします。

### Quizドキュメントのデータ

ではデータ型というものがあることを理解した上で、データを入れていきましょう。クイズのデータはJavaScriptのオブジェクトを利用して書くと以下のようになります。

```js
{
  title: 'あなたはどれくらい分かる？ 日本人テニス選手に関するクイズ',
  description: 'あなたはテニスが好きですか? 錦織選手や大坂なおみ選手、松岡修造選手など日本の有名な選手に関するクイズに答えましょう！'
}
```

クイズに入れるデータはなんでも構いませんので、自分でクイズを用意しても大丈夫です。CodeGrit側で準備したものを利用する場合は上記のデータを入力してください。`Document ID`の部分ですが、こちらは自動でIDがつけられますので何も入れなくて構いません。

データは作成出来たでしょうか？ 作成出来たら次はサブコレクションを作成していきます。

### QuizItemsサブコレクションの作成

![add-data-02](https://firebasestorage.googleapis.com/v0/b/codegrit-images.appspot.com/o/codegrit-react%2FLesson10%2Fadd-data-02-2.png?alt=media&token=738cc8c0-e340-4664-9ba3-978c9de1be12)

さて、次に作成するのはQuizItemsサブコレクションです。上記の画像は既にサブコレクションを作成済みの画面を表示しています。サブコレクションの名前を見ると、`quiz_items`という名前がついていることが分かります。データベースでは一般的に2ワード以上の名前を利用する場合は`アンダーバー(_)`を利用してワードをつなぎます。JavaScriptだと、大文字を利用してつなぐので少し違いますね。

データの作成は先程コレクションを利用したときと同じように出来ますので、まずは`quiz_items`というサブコレクションを作り、ドキュメントを登録していきましょう。

登録するデータは以下の4つです。(自分で作成したクイズを利用する場合は、自分のデータを入れてください。)

```js
[
  {
    order: 0,
    answer_slug: 'wim_8',
    title: '松岡修造選手の最高戦績は次のうちどれ？'
  },
  {
    order: 1,
    answer_slug: '201ph',
    title: '大阪なおみ選手のサービスの最速記録は？'
  },
  {
    order: 2,
    answer_slug: 'cilic',
    title: '錦織選手が惜しくも敗れた2014年のUSオープン決勝戦の相手は'
  },
  {
    order: 3,
    answer_slug: 'age46',
    title: 'シングルス世界最高4位を記録し、数々の最年長記録も持つクルム伊達公子選手の引退時の年齢は？'
  }
]
```

### QuizOptionsサブコレクションの作成

QuizOptionsサブコレクションも、同様に作成しましょう。サブコレクション名は`options`を利用します。

以下のデータを入れていきましょう。ちょっとデータを入れるのが大変になってきているかと思いますががんばりましょう。(quiz_item_0の0はorderを示しています。)

```js
quiz_item_0_options = [
  {
    order: 0,
    slug: 'wim_4',
    title: 'ウィンブルドンベスト4'
  },
  {
    order: 1,
    slug: 'wim_8',
    title: 'ウィンブルドンベスト8'
  },
  {
    order: 2,
    slug: 'us_8',
    title: '全米オープンベスト8'
  },
  {
    order: 3,
    slug: 'french_final',
    title: '全仏オープンファイナル'
  }
]

quiz_item_1_options = [
  {
    order: 0,
    slug: '165ph',
    title: '時速165キロ'
  },
  {
    order: 1,
    slug: '182ph',
    title: '時速182キロ'
  },
  {
    order: 2,
    slug: '193ph',
    title: '時速193キロ'
  },
  {
    order: 3,
    slug: '201ph',
    title: '時速201キロ'
  }
]

quiz_item_2_options = [
  {
    order: 0,
    slug: 'federer',
    title: 'ロジャー・フェデラー'
  },
  {
    order: 1,
    slug: 'cilic',
    title: 'マリン・チリッチ'
  },
  {
    order: 2,
    slug: 'delpotro',
    title: 'デル・ポトロ'
  },
  {
    order: 3,
    slug: 'raonic',
    title: 'ミロシュ・ラオニッチ'
  }
]

quiz_item_3_options = [
  {
    order: 0,
    slug: 'age42',
    title: '42歳'
  },
  {
    order: 1,
    slug: 'age44',
    title: '44歳'
  },
  {
    order: 2,
    slug: 'age46',
    title: '46歳'
  },
  {
    order: 3,
    slug: 'age48',
    title: '48歳'
  }
]
```

尚、今回はデータを一個一個入れていますが、一般的にはこうしたデータはプログラムを利用して入れています。ここまでで、大体どんな感じでデータを入れるのかがイメージ出てきたかと思います。