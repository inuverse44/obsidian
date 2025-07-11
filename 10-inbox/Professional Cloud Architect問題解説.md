---
template: Inbox
title: Professional Cloud Architect問題解説
date: 2025-07-07
source: ソースを入れてください
tags:
  - GCP
  - PCA
  - Udemy
status: pending
priority: 中
aliases:
---

# 要約
- Udemyの問題解説
- ケーススタディ問題でCloud Functionの呼び出しの認証に関する問題
- 

# 詳細メモ

## 第３回
### Q3

問題：
>【ケーススタディ問題】この質問については、TerramEarthのケーススタディを参照してください。
>
>あなたは、バックエンドにいくつかのCloud Functionを使用する新しいアプリケーションの構築を開始します。あるユースケースでは、Cloud Function func_displayが別のCloud Function func_queryを呼び出す必要があります。この時、func_displayからの呼び出しをfunc_queryだけが受け付けるようにする必要があります。また、Googleの推奨するベストプラクティスにも従いたいと考えています。
>
>要件を達成するためにするべきことは何ですか？

選択肢：
1. これらの2つのFunctionを同じプロジェクトとVPCに作成する。func_queryが内部トラフィックのみを受け入れるようにする。func_queryには、func_displayからのトラフィックのみを許可するingressファイアウォールを作成する。また、両方のFunctionが同じサービスアカウントを使用するようにする
2. トークンを作成し、環境変数としてfunc_displayに渡する。func_queryを起動するときに、リクエストにトークンを含める。同じトークンをfunc_queryに渡し、トークンが異なる場合は呼び出しを拒否する
3. func_queryを「認証を必須にする」ようにする。固有のサービスアカウントを作成し、それをfunc_displayに関連付ける。func_queryにサービスアカウントのinvokerロールを付与する。func_displayにidトークンを作成し、func_queryの起動時にそのトークンをリクエストに含める
4. func_queryを「認証を必須にする」ようにして、内部トラフィックのみを受け付けるようにする。この2つのFunctionを同じVPC内に作成する。func_queryにingressファイアウォールルールを作成し、func_displayからのトラフィックのみを許可する

私の回答：
2

実際の回答：
3

Udemy解説：
>複数のCloud Functionを接続するサービスを構築する場合、各Functionが他のFunctionの特定のサブセットに対してのみリクエストを送信できるようにすることがベストプラクティスです。
>
>たとえば、ログイン用のFunctionがある場合、そのFunctionはユーザプロファイル用のFunctionにはアクセスできるはずですが、 検索用のFunctionにはアクセスできないようにするべきです。
>
>特定の呼び出し側のFunctionからのリクエストを受け入れるように受信側Functionを設定するには、受信側Functionの呼び出し側FunctionのサービスアカウントにCloud Functions Invoker (roles/cloudfunctions.invoker) ロールを割り当てる必要があります。
>
>したがって正解は以下の通りです。
>
>「func_queryを「認証を必須にする」ように設定する。固有のサービスアカウントを作成し、それをfunc_displayに関連付ける。func_queryにサービスアカウントのinvokerロールを付与する。func_displayにidトークンを作成し、func_queryの起動時にそのトークンをリクエストに含める」
>
>参考：
>[https://cloud.google.com/functions/docs/securing/authenticating](https://cloud.google.com/functions/docs/securing/authenticating)

# 🔗 関連
