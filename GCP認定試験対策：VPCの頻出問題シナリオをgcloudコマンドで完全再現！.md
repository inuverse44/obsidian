---
template: Inbox
title: タイトルを入れてください
date: 2025-07-12
source: ソースを入れてください
tags: 
status: pending
priority: 優先度
aliases:
---
## Zenn記事用の整理案

以下に、ご提供のコマンドと解説をZenn記事向けに構成案としてまとめました。

# GCP認定試験対策：VPCの頻出問題シナリオをgcloudコマンドで完全再現！

GCPの認定試験、特にVPCネットワークに関する問題は具体的な構成をイメージするのが難しいですよね。この記事では、頻出される2つのシナリオを実際に`gcloud`コマンドを使いながら手を動かして再現し、その仕組みを深く理解することを目指します。

- **シナリオ1**: 外部IPを持たないVMからCloud Storageにファイルをダウンロードする (Question 34)
    
- **シナリオ2**: VMからの送信トラフィックをActive Directory宛のみに制限する (Question 35)
    

## 0. 準備：プロジェクトとAPIの設定

まずは、作業の前提となる環境設定を行います。

```
# ご自身の環境に合わせて設定してください
export PROJECT_ID=inuverse-test-vpc
export REGION=asia-northeast1
export ZONE=asia-northeast1-a

gcloud config set project $PROJECT_ID
gcloud config set compute/region $REGION
gcloud config set compute/zone $ZONE
```

### 🔹 必要なAPIの有効化

VPC、Compute Engine、Cloud Storageを操作するために、それぞれのAPIを有効化します。

Bash

```
gcloud services enable \
  compute.googleapis.com \
  storage.googleapis.com
```

---

## シナリオ1: 外部IPなしVMからのセキュアなファイルアクセス (Q34)

このシナリオは、「**インターネットアクセスが許可されていないVMに、どうやってソフトウェアをインストールするか？**」というセキュリティ要件の高い環境を想定した問題です。

**🔑 解法のポイント**: **限定公開のGoogleアクセス (Private Google Access)**

### Step 1: VPCとサブネットの作成

まず、VMを配置するためのカスタムVPCとサブネットを作成します。

Bash

```
# カスタムモードでVPCを作成
gcloud compute networks create my-vpc \
  --subnet-mode=custom

# VPC内にサブネットを作成
gcloud compute networks subnets create my-subnet-psa \
  --network=my-vpc \
  --region=$REGION \
  --range=10.8.0.0/28
```

### Step 2: 外部IPアドレスを持たないVMを作成

問題の要件通り、`--no-address` オプションを指定して、内部IPアドレスのみを持つVMを作成します。

Bash

```
gcloud compute instances create gce-psa-test \
  --zone=$ZONE \
  --machine-type=e2-micro \
  --network=my-vpc \
  --subnet=my-subnet-psa \
  --no-address
```

作成後、`EXTERNAL_IP`が空欄であることを確認しましょう。

Bash

```
gcloud compute instances list
# NAME          ZONE                 MACHINE_TYPE  INTERNAL_IP  EXTERNAL_IP  STATUS
# gce-psa-test  asia-northeast1-a    e2-micro      10.8.0.2                  RUNNING
```

### Step 3: ファイルの置き場所としてCloud Storageバケットを作成

VMにインストールしたいソフトウェア（今回はダミーファイル）を配置するバケットを作成します。バケット名は世界で一意にする必要があります。

Bash

```
# バケット名はご自身のプロジェクトIDなどに変更してください
gsutil mb -l $REGION gs://inuverse-test-vpc/
```

### Step 4: 【最重要】限定公開のGoogleアクセスを有効化

この設定により、サブネット内のVMは外部IPがなくても、Google CloudのAPI（Cloud Storageなど）にGoogleの内部ネットワーク経由でアクセスできるようになります。

Bash

```
gcloud compute networks subnets update my-subnet-psa \
  --region=$REGION \
  --enable-private-ip-google-access
```

これで、Q34の正解構成が完成しました！VMにSSH接続して `gsutil ls gs://inuverse-test-vpc/` を実行するとバケットにアクセスでき、`ping 8.8.8.8` を実行すると失敗することから、インターネットには接続できないことが確認できます。

コード スニペット

```
flowchart LR
  subgraph SUBNET["サブネット (外部IPなし / PGA 有効)"]
    VM["Compute Engine<br>no external IP"]
  end

  subgraph GOOGLE["Google ネットワーク"]
    PGA_IP["199.36.153.4/30<br>(private.googleapis.com)"]
    GCS["storage.googleapis.com"]
    BUCKET["GCS バケット"]
  end

  VM -- "トラフィック" --> PGA_IP
  PGA_IP -- "Googleバックボーン経由" --> GCS
  GCS -- "API呼び出し" --> BUCKET
```

---

## シナリオ2: Egress（下り）ファイアウォールによる通信制御 (Q35)

このシナリオは、「**すべてのVMから特定サーバーの特定ポートへの通信だけを許可し、他はすべて遮断したい**」という、送信トラフィックの厳格な制御に関する問題です。

**🔑 解法のポイント**: **ファイアウォールルールの優先度 (Priority)** VPCファイアウォールでは、**優先度の数値が小さいルールから順に評価**されます。この性質を利用して、許可ルールを拒否ルールより高い優先度（小さい数値）に設定します。

### Step 1: Active Directoryへの通信を許可するルールを作成 (優先度 100)

まず、宛先（Active Directoryサーバー）とポート（LDAP/LDAPS）を限定した許可ルールを、高い優先度 `100` で作成します。

Bash

```
gcloud compute firewall-rules create allow-ad-egress \
  --network=my-vpc \
  --direction=EGRESS \
  --action=ALLOW \
  --rules=tcp:389,tcp:636 \
  --destination-ranges=10.100.0.10/32 \
  --priority=100
```

- `destination-ranges=10.100.0.10/32`: `/32` はIPアドレス1つだけを指名するCIDR表記です。
    
- `rules=tcp:389,tcp:636`: Active Directoryが使用するLDAP/LDAPSプロトコルのポートです。
    

### Step 2: その他すべての通信を拒否するルールを作成 (優先度 1000)

次に、すべての宛先 (`0.0.0.0/0`) へのすべてのプロトコル (`all`) の通信を拒否するルールを、低い優先度 `1000` で作成します。

Bash

```
gcloud compute firewall-rules create deny-all-egress \
  --network=my-vpc \
  --direction=EGRESS \
  --action=DENY \
  --rules=all \
  --destination-ranges=0.0.0.0/0 \
  --priority=1000
```

これで、Q35の正解構成が完成しました！VMからのEgress通信は、まず優先度100のルールで評価され、AD宛でなければ次に優先度1000のルールで評価され、結果として拒否されます。

---

## Appendix: 動作確認のためのSSH接続設定

今回のVMには外部IPがないため、直接SSH接続はできません。動作確認を行うには、**Identity-Aware Proxy (IAP)** を経由して接続するのが便利です。そのためのIngress許可ルールも追加しておきましょう。

Bash

```
gcloud compute firewall-rules create allow-ssh-iap \
  --network=my-vpc \
  --direction=INGRESS \
  --action=ALLOW \
  --rules=tcp:22 \
  --source-ranges=35.235.240.0/20 \
  --description="Allow SSH via IAP"
```

これで、`gcloud compute ssh` コマンドでVMに安全に接続できます。

Bash

```
gcloud compute ssh gce-psa-test --zone=$ZONE
```