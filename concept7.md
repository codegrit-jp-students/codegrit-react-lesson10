# Cloud Datastoreの利用準備をしよう

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