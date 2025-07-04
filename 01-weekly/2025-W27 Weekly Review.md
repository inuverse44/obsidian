
# 今週
- Cloud Loggingによりstock-conversion-service, new-stock-serviceの処理時間をローカルで計測可能にした。
- 開発環境にデプロイし、動作確認中
- 当初　7/4に負荷検証の一連の流れ完了予定であったが、１日遅延。デプロイして動作チェック後、残課題を巻き取る。

◇詳細
営業在庫
- Cloud Logging API調査
- ログのメッセージ、トレースIDからタイムスタンプの取得。簡単な統計量の計算（平均ん、標準偏差、中央値、95%ile、99%ile）。
- 営業在庫スケジュール決めMTG
- Logging APIのリクエスト時にExponential backoffを実装
- 負荷検証実行フローのための、ロギング、モニタリング、Gatling（負荷ツール）の結合（遅延）
- stock-conversioon-serviceとnew-stock-serviceのPR & デプロイ（開発環境）
- stock-conversioon-serviceとnew-stock-service間のRabbit MQの処理時間がわかるように調整

◇その他
- TOP報告解説
- Kubernates hands on
# 来週

◇営業在庫
- 負荷検証実行フローのための、ロギング、モニタリング、Gatling（負荷ツール）の結合
- 負荷検証ができるのが、少なくとも7/14以降なので、動き方について相談する

◇その他
- Kubernates hands on

# その他

ここ2週間、Geminiで日報を食わせて週報を書かせていましたが、日報の構造化が必要です。YAMLのように書いた方がLLM君たちが読み取りやすいとは思いますが、日頃の使い勝手が悪くなります。また、さらにmarkdownとTORA三の相性が悪いこと、HTMLへ単純に変換しても、TORA三上で見栄えが悪いことがわかりました。

見栄えって、思ったよりも人間の行動を影響させる気がしています。論文


