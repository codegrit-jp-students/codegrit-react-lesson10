# レッスン10. Firebaseを利用したアプリケーション作成

## クイズアプリケーションを作成しよう

![myquiz-screenshot.png](https://firebasestorage.googleapis.com/v0/b/codegrit-images.appspot.com/o/codegrit-react%2FLesson10%2Fmyquiz-screenshot.png?alt=media&token=5eb52db8-2c27-4834-a262-2b06093054bb)

ここまでのレッスンで学んだ内容で、皆さんは既にフロントエンドならどんなアプリでも作れるようになりました。なぜかというと、全てのWebサイトは小さなコンポーネントの積み重ねで出来ているからです。小さなコンポーネントが上手く作れるようになれば、後はそれを積み重ねていくだけです。

このレッスンでは、これまで学んできた内容を活用してクイズアプリを作成します。またそれだけでなく、Firebaseを利用してデータの保存や読み込みを行う方法を同時に学びます。

このレッスンで作成するアプリのサンプルはこちらのページでご覧頂けます。

- [MyQuiz App](https://myquiz-app-cecc8.firebaseapp.com/)

作成するアプリは単純なものではありますが、ステップフォームやWicked(魔法使いのような意味)フォームと呼ばれる複数ページに渡ってデータを取得するフォームの理解をするのに最適です。(厳密にはフォームは利用していませんが、複数ページでのユーザーの入力値を保存していくという点では同じです。)

またサンプルコードは以下のリポジトリよりダウンロード頂けます。(このレッスンではFirebaseという外部ツールを使うためこれまでとは異なりそのままでは`yarn run start`としても上手く動きません。)

- [codegrit-react-lesson10-myquiz-app](https://github.com/codegrit-jp-students/codegrit-react-lesson10-myquiz-app)

## Firebaseとは? 

FirebaseはGoogleによって運営されているサービスです。Firebaseを利用することで、本来はサーバーサイドの実装が必要だったユーザー認証やデータのやり取りをサーバーサイドプログラミングを知らない人でも簡単に行うことが出来るようになりました。また、チャットアプリなどリアルタイムなデータのやり取りが必要なサイトでもよく利用されています。Firebaseは無料で利用開始することが出来、今回のアプリでは課金などありませんので安心してください。

## Firebaseにプロジェクトを作ろう

![Firebase-top](https://firebasestorage.googleapis.com/v0/b/codegrit-images.appspot.com/o/codegrit-react%2FLesson10%2Ffirebase-top.png?alt=media&token=59768327-cd3f-41f0-ae31-88ea7162da3b)

早速Firebaseで今回作成するクイズアプリのプロジェクトを作成していきましょう。まずはFirebaseのサイトにアクセスしてください。

[Firebase公式](https://firebase.google.com/)

Googleのアカウントを持っている方は、Googleアカウントでログイン出来ます。ない方はGoogleアカウントを作成してログインしましょう。

ログインすると、右上のメニューからコンソール(管理画面)に入ることが出来ます。すると以下のような画面が出てくるはずです。

![Firebase-dashboard](https://firebasestorage.googleapis.com/v0/b/codegrit-images.appspot.com/o/codegrit-react%2FLesson10%2Ffirebase-dashboard.png?alt=media&token=c5164760-0661-4d2c-8615-5056255064b6)

ダッシュボード上で「プロジェクトを追加」をクリックします。するとモダルウィンドウが立ち上がりますので、以下のように編集して作成してください。

- プロジェクト名 - 自由です。(例: My Quiz App)
- 地域 - 日本
- ロケーション - asia-northeast1

作成するとプロジェクトのダッシュボードへ移動します。

## データベースを利用する

さて、フロントエンド開発のコースではこれまでデータはJSONやオブジェクト形式で保存したものを読み取って使うということをしてきました。しかし、 データ量が大きい場合やデータの保存が必要なサービスの場合はクライアントサイド側だけではデータを上手く扱うことが出来ません。そこで必要なのがデータベースです。

FirebaseはCloud Firestoreと呼ばれるデータベースを提供しており、今回のアプリはこのデータベース上にクイズの問題や回答、クイズの回答結果などのデータを保存していきます。

注意点として、Firebaseではこれまでは`Realtime Database`と呼ばれるデータベースが主流で、まだこちらも提供しております。ただし`Cloud Firestore`がより新しくこれから主流となると考えられるため、このレッスンでは`Cloud Firestore`を利用します。

## データベースの種類

データベースには大きく分けて2つがあります。一つはリレーショナルデータベースでもう一つはNoSQLデータベースです。リレーショナルデータベースについてはサーバーサイド開発のプログラムで学ぶことになります。今回利用するCloud FirestoreはNoSQLデータベースの一つとなります。

## Cloud Firestoreの基礎

Cloud Firestoreには以下のような特徴があります。

- スケールしやすい
- 順番を持たない

SQLの場合は一般的にデータは1,2,3,4と順番に保存されていきます。しかし、NoSQLの場合はデータの順番というものがありません。そのためデータを順番に取ってきたい場合には例えば、データが作成された日時でデータ一覧を並び替える必要があります。このようにすることによって、Cloud Firestoreでは、データを複数サーバーに分けて保存することが簡単に出来、データ量が将来的に莫大になっても上手く扱えるようになっています。

SQLについても知っているとこの内容はより理解しやすいですが、現状としてはCloud Datastoreはデータ量に関わらず安定して使えると理解していれば大丈夫です。

## データ構造

Cloud Firestoreに保存されるデータの構造は非常にシンプルです。

### コレクションとドキュメント

- コレクション

Cloud Datastoreで重要な2つの概念がコレクションとドキュメントです。コレクションとは、簡単に言うと保存されるデータが一種類に限定されたパソコンのフォルダのようなものです。

例えば、レストランを紹介するサイトを作るとしましょう。この時には、例えばレストランのリストを扱うためのコレクションを`restaurants`という名前で作成することが出来ます。

- ドキュメント

一つのレストランは様々な情報を持つはずです。例えば、レストランの名前、住所、開店や閉店の時間、定休日、定員などです。こうしたデータをまとめて保存したものをドキュメントと呼びます。各ドキュメントは必ずID(ドキュメントID)を持ちます。IDは社員証や学生証、免許証などにもありますのでおなじみの概念かと思いますが、あるデータが識別できるようにするものであれ重複の無い数値や文字列を利用します。NoSQLでは文字列を使います。

### サブコレクションとマップ

ところで、レストラン紹介サイトを作るなら他にも入れたいデータがありますよね。例えば、メニュー一覧がそうです。しかしそれぞれのメニューも名前や値段など複数のデータを持っています。

こうしたケースで利用できるのが、サブコレクションやマップです。

- サブコレクション

サブコレクションはコレクションの持つドキュメントが持つコレクションのことです。後ほど解説しますが、メニュー一覧やレビュー一覧はマップよりもサブコレクションに保存すると便利です。

- マップ

時には名前や住所をカタカナ、ローマ字、漢字など複数のフォーマットで持ちたい場合があるでしょう。こうした時にドキュメント内のデータを次のようにネストして保存することが出来ます。このようにネストされたデータのことをマップと呼びます。

```
- name
  - kanji: 優勝軒
  - カタカナ: ユウショウケン
  - romaji: yuushoken
```

ネストは複数改装とることが出来るので、リストのように利用することも出来ます。

## 今回のアプリで利用しているデータ

さて、アプリのページは既にご覧になったでしょうか? まだ見ていない方は以下のURLよりまずはアプリを見てみましょう。

- [MyQuiz App](https://myquiz-app-cecc8.firebaseapp.com/)

ご覧頂いて、どんなデータが読み込まれているのか予測はつくでしょうか？ まず思い浮かぶのはQuizそのものだと思います。オブジェクト形式で書くとこのような形です。

```js
const quizData = {
  title: "クイズのタイトル" // 「あなたはどれくらい分かる？...」の部分
  descriptin: "クイズの説明" // 「あなたはテニスが好きですか?...」の部分
}
```

クイズには、何個かの設問がついています。この設問のことをこのアプリでは`QuizItem`と呼んでます。`Quiz`コレクションが複数の`QuizItem`サブコレクションを持つという構造になります。

一つ一つの設問を見ていくと、分かる通り、設問ごとに回答の選択肢が複数個(今回のアプリでは4個)あります。これらの選択肢のことを今回のアプリでは`QuizOption`と呼びましょう。

そうすると、先程`Quiz`が`QuizItem`をサブコレクションとして持っていたのと同様に、`QuizItem`は`QuizOption`をサブコレクションとして持つという風に関係性が見えて来ます。

ここまでのことを視覚化してみると以下の画像のようになります。

![myquizのデータモデル](https://firebasestorage.googleapis.com/v0/b/codegrit-images.appspot.com/o/codegrit-react%2FLesson10%2Fmyquiz-datamodel.png?alt=media&token=6c9f8b42-fbbc-4cfa-82c0-37b3f79d7a3f)

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

## クエリ

さて、こうした保存したデータはFirebase APIを利用して読み込むことが出来ます。この読み込む際にAPIに対して投げるリクエストのことを専門用語ではクエリと呼びます。

Cloud Datastoreは、以下のようにファンクションを組み合わせていくことでクエリを投げます。

```js
const db = firebase.firestore();

const quizzesSnapshot = db.collection("quizes").get(); 
```

上記のクエリで返ってくるのはPromiseオブジェクトです。例えば、指定したコレクションが存在しない場合はrejectされてerrorが返ってきます。取得に成功した場合は、`QuerySnapshot`というオブジェクトがresolveさえて返ってきます。

なぜ、直接リストでドキュメントを返してこないのでしょうか？ これはなぜかというと、Cloud Datastoreはデータの変更がリアルタイムに変更されることを想定しており、データが変更された際にコールバックファンクションを呼び出すような利用方法も想定しているからです。

`QuerySnapshot`オブジェクトは`forEach`というファンクションが定義されています。これを利用することで戻ってきたコレクションのドキュメントにアクセスすることが出来ます。

```js
quizzesSnapshot.forEach((doc) => {
  console.log(doc.id); // クイズドキュメントのIDの取得
  console.log(doc.data()); // クイズドキュメントのデータの取得
})
```

また、ドキュメント一覧は次のようにして取得することも出来ます。

```js
const quizzes = quizzesSnapshot.docs
```

今回、作成するアプリではどちらの方法も利用していきます。サブコレクションを取得するためのクエリの作成方法などはアプリを作りながら説明していきます。

## ドキュメントとクエリ

上記のサブコレクションとマップに関する説明の部分で、メニュー一覧やレビュー一覧はサブコレクションに入れるべきと説明しました。これはCloud Firestoreではドキュメントに対してクエリを利用して一部だけを取ってくるということが出来ないからです。

そのため、仮にマップを利用してレビューやメニュー一覧を保存した場合、どんな場合でも全てのレビューと全てのメニューがレストラン情報と一緒に読み込まれます。

このことが引き起こす問題はデータ量の問題です。例えばレストラン一覧のページがあった場合、レビューやメニューを表示するとしてもせいぜい1個あるいは数個のはずです。

それにも関わらず、マップを利用すると全てのレストランのレビューデータも取ってくることになります。レビューは時間が経つに連れて増えるので大変な量のデータになってしまうことが想像出来るかと思います。

大量のデータを読み込むのには時間がかかり、またそのデータを扱うためのメモリ消費量も大きくなるため、アプリ全体の動きが遅くなりユーザビリティが大きく落ちてしまいます。

このように、全てのデータを取得する必要がなく、またデータ量が大きくなりそうな場合はサブコレクションを利用するのが賢明です。

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

## firebaseDb.jsを作成する。

ここまでで、いよいよQuizを作成していく準備が出来ました。QuizコンポーネントではクイズのデータをCloud Datastoreから取得する必要があります。

Cloud Datastoreからデータを取得するには、Firebaseライブラリから自分のFirebaseのファイルにアクセスするための初期化を行う必要があります。

この初期化をCloud Firestoreにアクセスするコンポーネントごとに行うのは大変です。そのため、Cloud Datastoreへのクエリを含むファンクションを全てまとめたユーティリティを作っていきます。

ユーティリティとは、アプリのプログラムにおいて、共通点のある処理をまとめて再利用が出来るようにしたファイルのことを言います。

まずはこのユーティリティファイルに`firebaseDb.js`という名前を作り編集をしていきましょう。ファイルはsrc直下に`lib`というフォルダーを作りその中に入れます。

```js
// src/lib/firebaseDb.js

import firebase from 'firebase/app';
import 'firebase/firestore';
```

さて、先程書いたFirebaseの初期化ですが、初期化を行うには自分のFirebase上のプロジェクトに関するデータが必要です。

このデータを取得するには、Firebaseプロジェクト上でWebアプリ用のデータを作成する必要があるのでこれを進めていきましょう。

まずはFirebaseのコンソールにアクセスし、メニューから`Project settings`を選びます。

![project-settings-1](https://firebasestorage.googleapis.com/v0/b/codegrit-images.appspot.com/o/codegrit-react%2FLesson10%2Fproject-settings-1-2.png?alt=media&token=b0046910-5c0a-4539-b063-179017ee70a5)

するとプロジェクトの設定ページが表示されるので、下の方にスクロールしていき、`Your apps`の部分からWebアプリの登録を選択します。

![project-settings-2](https://firebasestorage.googleapis.com/v0/b/codegrit-images.appspot.com/o/codegrit-react%2FLesson10%2Fproject-settings-2-2.png?alt=media&token=c50a1f3f-d057-448e-9eff-f0f689bbc86e)

すると、次のような画面が出てくるので`My Quiz`のような名前をつけてアプリを登録しましょう。

![project-settings-3](https://firebasestorage.googleapis.com/v0/b/codegrit-images.appspot.com/o/codegrit-react%2FLesson10%2Fproject-settings-3.png?alt=media&token=f7f7fb7f-6989-40e4-92ae-70e4f08eb7d7)

すると、次の画面のようにFirebaseの設定用のコードが出てきます。そのコードから`firebaseConfig`というオブジェクトのデータを全てコピーしましょう。

![project-settings-4](https://firebasestorage.googleapis.com/v0/b/codegrit-images.appspot.com/o/codegrit-react%2FLesson10%2Fproject-settings-4.png?alt=media&token=ebbc74d5-a3a5-4512-8230-2dcd6e1e28d7)

このデータを、先程の`firebaseDb.js`にペーストし、このデータを利用してfirebaseの初期化を行います。

```js
// src/lib/firebaseDb.js

const firebaseConfig = {
  省略...
}

firebase.initializeApp(firebaseConfig); // firebaseの初期化
const db = firebase.firestore(); // Cloud Datastoreへのアクセスのための初期化
```

これで、Cloud Datastoreへアクセスする準備ができたので、まずはクイズ一覧を呼び出すファンクションを定義しましょう。

```js
省略...

export const getAllQuizzes = () => {
  return db.collection("quizes").get();
}
```

では、このファンクションを利用して、Quizコンポーネントを作成していきましょう。

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