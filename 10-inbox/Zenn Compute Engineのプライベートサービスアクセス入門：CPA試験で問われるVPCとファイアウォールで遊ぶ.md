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


```bash
export PROJECT_ID=inuverse-test-vpc
export REGION=asia-northeast1
export ZONE=asia-northeast1-a

gcloud config set project $PROJECT_ID
gcloud config set compute/region $REGION
gcloud config set compute/zone $ZONE
```