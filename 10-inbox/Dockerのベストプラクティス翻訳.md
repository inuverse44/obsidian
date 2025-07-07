---
template: Inbox
title: タイトルを入れてください
date: 2025-07-07
source: https://www.docker.com/blog/intro-guide-to-dockerfile-best-practices/
tags:
  - Docker
status: pending
priority: 優先度
aliases:
---

# 要約
Dockerのベストプラクティスをまとめる
- キャッシュを利用する。変更頻度の低い順に並べる
- コピー先は具体的なパスを明示する。
- `apt-get update & install`は一緒に`RUN`
- 不要な依存関係を削除する
- パッケージマネジャのキャッシュを削除する
- 公式のイメージを使用する
- 具体的なタグを使用する
- 最小限のフレーバーを探す
- 一貫した環境でソースからビルドする
- 依存関係を別のステップで取得する
- マルチステージビルドを使用して、ビルド依存関係を削除する
# 詳細メモ
- 

# 🔗 関連
