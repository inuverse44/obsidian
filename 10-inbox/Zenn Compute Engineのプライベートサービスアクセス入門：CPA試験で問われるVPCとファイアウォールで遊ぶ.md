---
template: Inbox
title: Zenn Compute Engineのプライベートサービスアクセス入門：CPA試験で問われるVPCとファイアウォールで遊ぶ
date: 2025-07-10
source: ソースを入れてください
tags:
  - VPC
  - Google
status: pending
priority: 高
aliases:
---
# Compute Engineのプライベートサービスアクセス入門：CPA試験で問われるVPCとファイアウォールで遊ぶ

---

## はじめに  
本記事では、公共インターネット経由を遮断した高度セキュリティ環境下での Google Cloud VPC Private Services Access（以下、PSA）の概要と、Compute Engine インスタンスにおけるソフトウェア配布および Active Directory への接続制御を一連の事例として解説します。CPA認定試験でも頻出の「プライベートサービスアクセス設定」「ファイアウォール優先度設計」のポイントを押さえ、試験対策と実務的知見の両面から学びましょう。

---

## 1. VPC Private Services Access 概要  
- **定義**  
  VPC Private Services Access は、VPC ネットワークから Google 管理サービス（Cloud Storage、Cloud SQL、Artifact Registry など）へインターネットを介さずに内部 IP でアクセスできる機能です。  
- **主なメリット**  
  1. 外部 IP 不要でセキュアにサービス利用  
  2. ファイアウォールで細かなアクセス制御が可能  
  3. CPA試験では「インターネット経由しないソフトウェア配布」の設問背景に用意されやすい

---

## 2. ケースA：Cloud Storage 経由によるプライベートソフトウェア配布  
### 2.1 シナリオ  
公共インターネットへのアウトバウンドが禁止された環境下で、Compute Engine インスタンスに必要なインストーラやパッケージを配布する。  

### 2.2 手順概要  
1. **インストールファイルを GCS にアップロード**  
   ```bash
   gsutil cp local-package.tar.gz gs://my-private-bucket/
   ```  
2. **VPC ネットワークで PSA 用サブネットを設定**  
   ```bash
   gcloud compute networks vpc-access connectors create psa-connector \
     --network=my-vpc --region=asia-northeast1 --range=10.8.0.0/28
   ```  
3. **Compute Engine を PSA サブネット上に配置**  
   - 外部IPを未割り当て  
4. **ファイアウォールルールで GCS の IP 限定（任意）**  
   - 「バケットのホスト名」「プロデューサーサブネット」のみ許可  
5. **インスタンス内で gsutil を用いファイル取得**  
   ```bash
   gsutil cp gs://my-private-bucket/local-package.tar.gz .
   ```  

### 2.3 ポイント  
- 外部IPを持たないインスタンスでも PSA が有効ならば GCS にアクセス可能  
- ファイアウォールは PSA ネットワークの「Allow」設定のみで十分な場合が多い  

---

## 3. ケースB：Active Directory 接続限定の Egress ファイアウォール設計  
### 3.1 要件  
VPC 内の全 Compute Engine インスタンスは、TCP/389（LDAP）やTCP/636（LDAPS）で Active Directory サーバーにのみ接続可能とし、その他すべての外向きトラフィックは禁止。  

### 3.2 デフォルト暗黙ルールのおさらい  
- **暗黙のAllow Egress**  
  - 宛先：`0.0.0.0/0`、優先度：`65535`、動作：ALLOW  
- **暗黙のDeny Ingress**  
  - ソース：`0.0.0.0/0`、優先度：`65535`、動作：DENY  

### 3.3 カスタムルール設計  
1. **Active Directory への許可ルール**  
   ```bash
   gcloud compute firewall-rules create allow-ad-egress \
     --direction=EGRESS \
     --network=my-vpc \
     --action=ALLOW \
     --rules=tcp:389,tcp:636 \
     --destination-ranges=10.100.0.10/32 \
     --priority=100
   ```  
2. **その他すべてを拒否するルール**  
   ```bash
   gcloud compute firewall-rules create deny-all-egress \
     --direction=EGRESS \
     --network=my-vpc \
     --action=DENY \
     --rules=all \
     --destination-ranges=0.0.0.0/0 \
     --priority=1000
   ```  

#### 重要ポイント  
- **許可ルールの優先度 (100)** は、**拒否ルール (1000)** よりも高く（数値が小さく）設定  
- 「暗黙Allow(65535)」よりも「deny-all(1000)」が上位に来るため、すべての未定義Egressを確実にブロック  

---

## 4. CPA試験への適用：典型的設問パターンと解答戦略  
| 設問パターン                          | ポイント                                   |
|-------------------------------------|------------------------------------------|
| ソフトウェア配布がインターネットNG環境       | 「PSA＋GCS＋内部IPのみ」で解答               |
| AD接続のみ許可しその他遮断               | カスタムEGRESS ALLOW → カスタムEGRESS DENY の優先度設計 |

- **解答メモ**  
  1. 「Private Services Access の設定」  
  2. 「外部IPを割り当てないCompute Engine」  
  3. 「ファイアウォール：ALLOW(AD)、DENY(all)」  
  4. 「優先度（ALLOW < DENY < 暗黙）を明記」

---

## 5. まとめ  
本記事では、Compute Engine をプライベートサブネット上に配置し、PSA 経由でのソフトウェア配布と、AD接続限定のファイアウォール設計をケーススタディ形式で解説しました。CPA認定試験では“インターネット非依存＋細粒度アクセス制御”の組み合わせが頻出です。本稿の手順とポイントを押さえ、実務イメージとリンクさせながら解答できるよう準備しましょう。

---

## 参考文献  
- VPC Private Services Access の概要  
  https://cloud.google.com/vpc/docs/private-services-access  
- Cloud Storage と gsutil リファレンス  
  https://cloud.google.com/storage/docs/gsutil  
- VPC ファイアウォール ルール設計  
  https://cloud.google.com/vpc/docs/firewalls#priority  
```
