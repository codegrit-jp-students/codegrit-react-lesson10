# クイズアプリの概要

## クイズアプリケーションを作成しよう

![myquiz-screenshot.png](https://firebasestorage.googleapis.com/v0/b/codegrit-images.appspot.com/o/codegrit-react%2FLesson10%2Fmyquiz-screenshot.png?alt=media&token=5eb52db8-2c27-4834-a262-2b06093054bb)

ここまでのレッスンで学んだ内容で、皆さんは既にフロントエンドならどんなアプリでも作れるようになりました。なぜかというと、全てのWebサイトは小さなコンポーネントの積み重ねで出来ているからです。小さなコンポーネントが上手く作れるようになれば、後はそれを積み重ねていくだけです。

このレッスンでは、これまで学んできた内容を活用してクイズアプリを作成します。またそれだけでなく、Firebaseを利用してデータの保存や読み込みを行う方法を同時に学びます。

このレッスンで作成するアプリのサンプルはこちらのページでご覧頂けます。

- [MyQuiz App](https://myquiz-app-cecc8.firebaseapp.com/)

作成するアプリは単純なものではありますが、ステップフォームやWicked(魔法使いのような意味)フォームと呼ばれる複数ページに渡ってデータを取得するフォームの理解をするのに最適です。(厳密にはフォームは利用していませんが、複数ページでのユーザーの入力値を保存していくという点では同じです。)

またサンプルコードは以下のリポジトリよりダウンロード頂けます。(このレッスンではFirebaseという外部ツールを使うためこれまでとは異なりそのままでは`yarn run start`としても上手く動きません。)

- [codegrit-react-lesson10-myquiz-app](https://github.com/codegrit-jp-students/codegrit-react-lesson10-myquiz-app)