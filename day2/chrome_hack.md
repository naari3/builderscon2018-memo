# chrome dev tools hack

どうやったら視聴カウンタを沢山回せるか？
3分待つなどはしない


## 手法

youtube iframe api

playlateを変える

どうやってサーバーと通信を取るか

どういったHTTPリクエストでやりとりがなされるか？どうやって視聴終了と認識されたか確認する

`domain:localhost` domainで絞る
`XHR` で絞る

countというリクエストがある

下にリクエストペイロード `id` がある
レスポンス `{ success: true }`
Replay XHRしてみる、全く同じリクエストを送ることができる
`{ success: false }` になった

もう一度リクエストを見てみる
レスポンス `{ success: true }`
Replay XHR
`{ success: false }` になった

initiatorでどこでリクエストが生成されるのか見れる バックトレース
updateViewCountというのがあった
youtube iframe apiでrecordviewが6割超えてたら1カウント

id変数があったのでこれを見つける

ブレイクポイント
XHRでブレイクポイント設定
URL contains `localhost`

send、XHR送信したところで止まった

ページの一番上にidが埋め込まれていた

毎回ランダムなidを指定してやれば新しい視聴だと認識させられるのではないか？と考える

リクエストをcurlとしてコピーしてみる

さっきと同じリクエストだが、新しいidを指定してみる
でもfalseとされた
idだけ新しくても駄目だと思った

startというリクエストがある、ここでもidを送っていた
breakpointでstart送った後、countに送る前にcurlでidを指定して送信したらうまく成功した

どちらもcurlで試す、成功する

動画を見たというように見せかけれた

pythonでぷろぐらむを書いた
- パース、 `var id=ここをmath`
そのidをペイロードにしてリクエスト

## どうやってこれを守ればよいか

- CAPTCHAを使う
  - 人間だということを証明させる

### RandomizedDOM

変数の名前を変える 正規表現で取れなくする

### validation

5分間のビデオを見るのに1秒では終了できないため、そういったvalidation

### shadow banning

不正なことを明示的にBANするのではなく、相手にBANしたということを知らせない

計画通り

## 結論

tools
- ツールテクニックを使ってコンソールでコードが実行できる、
  - js
  - フィルタ
  - リプレイ
  - curl
メンタリティ
- どういったメンタリティでやるか
  - 好奇心
  - BANされてもやる
  - やってみる、ググる、stackoverflowする
  - 考え方はどんなツールより重要

https://amynguyen.net
https://twitter.com/amyngyn

- Q. キーボードナビゲータを使ってる方にはちょっと厳しい気がする
- A. テストして機能を確認

- Q. いつもこういう事をやっているのか
- A. 倫理観を欠かさずやっていきたい
