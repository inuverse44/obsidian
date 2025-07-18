---
template: permanent
title: GKE kubectlのForbiddenエラー対応手順
date: 2025-07-17
source: Geminiとの会話
tags:
  - GKE
  - kubectl
  - GCP
  - TroubleShooting
status: archived
priority: high
aliases:
  - GKE 権限エラー
---

# 概要
- GKEで`kubectl`コマンド（`kn`など）を実行した際、意図しない個人のGoogleアカウントが使用され、`namespaces is forbidden`という権限エラーが発生した。

# 本論
- **原因**: `gcloud`の認証情報と`kubectl`が使用する認証情報（`kubeconfig`ファイル）が一致していない。`gcloud auth login`や`gcloud config set account`を実行しただけでは、`kubectl`の設定は更新されない。

- **解決手順**:
    1.  **`gcloud`のアクティブアカウントを正しいものに設定する。**
        ```bash
        # 現在のアカウントを確認
        gcloud auth list

        # 正しい業務アカウントに切り替え
        gcloud config set account [正しいGCPアカウントのメールアドレス]
        ```

    2.  **`kubectl`の認証情報を更新する。**
        `gcloud`で設定したアカウント情報を`kubectl`に反映させるため、`get-credentials`コマンドを実行する。これが最も重要なステップ。
        ```bash
        gcloud container clusters get-credentials [クラスタ名] --zone [ゾーン名] --project [プロジェクトID]
        ```

    3.  **動作確認**
        再度`kubectl get namespaces`などを実行し、エラーが解消されたことを確認する。

# 🔗 関連
- [gcloud container clusters get-credentials](https://cloud.google.com/sdk/gcloud/reference/container/clusters/get-credentials)
- [GKE でのロールベースのアクセス制御の構成](https://cloud.google.com/kubernetes-engine/docs/how-to/role-based-access-control)