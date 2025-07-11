---
template: ReadingInbox
title: 実践OpenTelemetry
author:
  - Daniel Gomez Branco
date: 2025-07-10
published: OREILLY
isbn: 978-4-8144-0103-1
source: https://www.oreilly.co.jp/books/9784814401031/
read_date: 2025-07-10
status: pending
tags:
  - 読書
  - 本
aliases:
  - 負荷検証
---

# 要約
- 

# 重要ポイント・キーアイデア
- 

# 引用メモ

p3
> 一般用語でいうオブザーバビリティとは、外部出力の観測から内部状態をどの程度推測できるかを測るシステムの品質です。これは1960年に、Rudolf E. Ka'lma'nが制御システムの理論の概念として初めて説明したもので、論文「On the general theory of control system」の一部として、...（略）

On the general theory of control systemはOpen Source
https://www.sciencedirect.com/science/article/pii/S1474667017700948

中身はかなり数学を使用している。定義は
> Let $X^\ast$ be the dual vector space of the state space X, i.e. the space of all linear function on $X$. An element $\mathbf{z}^\ast$ or $\mathbf{x}^\ast$ of $X^\ast$ is called a *costate.*
> 
> 	A costate $\mathbf{z}^\ast$ of a plant is said to be 'observable' if its exact value $[\mathbf{z}^\ast, \mathbf{x}]$ at any state $\mathbf{x}$ at time $0$ can be determined from measurements of the output signal $y_1(t) = [\mathbf{b}^\ast,\mathbf{\phi}(t; \mathbf{x}, 0)]$ over the finite integral $0 \geq t \geq t_2$. The time $t_2$ will depend in general on $\mathbf{z}^\ast$. If every costate is observable, we say that the plant is 'completely observable'.

ちょっと何を言っているか分からないが、わかる範囲で読み解くと、
$X^\ast$という


p90
> （中略）
> この構造化されたデータの単位をスパンと呼び、１つの分散トランザクションに属するすべての操作をリンクする論理的な概念をトレースと呼びます。

トレースが包括的な単位で、スパンは構造化されたデータ単位。関数レベル。

リグレッション（regression）：回帰、後戻り、後退
linear regression

p93
>OpenTelemtryインスタンスはGlobalOpenTelemetry,get()から取得できるものの、依存性注入 (Dependency Injection)などの方法を使って計装クラスにインスタンスを敵将することが推奨されます。


p95
>スパンを作成する際に考慮すべき重要な側面の１つは粒度です。スパンは、システムの健全性を評価する際に統計的に意味のある作業単位で表現すべきです。

統計的に意味のある単位とは、どのように識別できるのか？
なにかよいcriteriaはないか？
親スパンと子スパンの持続時間の比が1%未満ならあまり意味がない。スパン収集のオーバーヘッドを生じさせるため、パフォーマンスにも影響あるかも。
親スパンと子スパンの持続時間の関係はどうあるべき？10%？20%?
おそらく%で切り分けるのは、良い方法ではないく、もっとメタ的な要素から切り分けられるべきである。その原理は？数学的に、演繹的に、適切なスパンを切り分けるためには？




p103
>親スパンと子スパンの関係は因果関係と依存関係であるべきです。親スパンで記述した操作が子スパンで記述した操作を引き起こし、親スパンはその結果にある程度依存します。

つまりシーケンス図的には
```mermaid
sequenceDiagram 
Parent->>Child: invoke
Child->>Child: run task 
Child->>Parent: return
```
の関係は満たしていて欲しい。
# 感想・考察
- 

# アクション・次のステップ
- 例：関連ノートにリンクする  
- 例：〇〇について調べる

# 🔗 関連ノート
- [[別のノート名]]
