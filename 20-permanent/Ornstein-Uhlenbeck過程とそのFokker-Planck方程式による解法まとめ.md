---
template: Inbox
title: Ornstein-Uhlenbeck過程とそのFokker-Planck方程式による解法まとめ
date: 2025-07-08
source: ソースを入れてください
tags:
  - Fokker-Planck
  - 確率微分方程式
status: archived
priority: 優先度
aliases:
  - 金融工学
  - ブラウン運動
---
# 要約
このノートは、Ornstein-Uhlenbeck過程を記述する確率微分方程式と、その確率密度関数が従うFokker-Planck方程式について解説しています。主な内容として、以下の点を含みます。
- **Ornstein-Uhlenbeck過程の定義**: 平均回帰モデルとして紹介。
- **Fokker-Planck方程式の導出**: 確率微分方程式から確率密度関数の時間発展を記述する方程式を導出。
- **厳密解の導出**:
    - **Ansatz法**: 解がガウス分布であると仮定し、平均と分散の時間発展を求める方法。
    - **フーリエ変換法**: より厳密に、方程式をフーリエ変換して解き、逆変換で解を得る方法。
- **定常解**: 時間が無限に経過した後の、安定した確率分布（ガウス分布）を導出。
- **解法の一般性**: フーリエ変換が有効なケースと、そうでない場合のアプローチについて言及。 
# 本論

## 1. Ornstein-Uhlenbeck過程の定義

Ornstein-Uhlenbeck（オルンシュタイン＝ウーレンベック）過程は、以下の確率微分方程式（ランジュバン方程式）で記述されます。

$$
dX_t = -\frac{1}{\tau} (X_t - \mu) dt + \sigma dW_t
$$

これは、「平均値 $\mu$ に戻ろうとする力（ドリフト項）」と「ランダムな揺らぎ（拡散項）」を受ける粒子の運動などをモデル化します。

-   $X_t$: 時刻 $t$ での確率変数（例：粒子の位置）
-   $\mu$: 平均復帰レベル
-   $\tau$: 緩和時間（時定数）
-   $\sigma$: 揺らぎの強さ
-   $dW_t$: ウィーナー過程

---

## 2. Fokker-Planck方程式の導出

確率変数 $X_t$ が従う確率密度関数 $p(x, t)$ の時間発展は、Fokker-Planck方程式で記述されます。ドリフト項を $A(x) = -\frac{1}{\tau}(x - \mu)$、拡散係数を $D = \frac{\sigma^2}{2}$ とすると、Fokker-Planck方程式は以下のように書けます。
$$
\frac{\partial p(x, t)}{\partial t} = -\frac{\partial}{\partial x} [A(x)p(x, t)] + \frac{\partial^2}{\partial x^2}[D p(x, t)]
$$
これを具体的に書き下すと、Ornstein-Uhlenbeck過程のFokker-Planck方程式が得られます。
$$
\frac{\partial p(x, t)}{\partial t} = \frac{1}{\tau}\frac{\partial}{\partial x} [(x - \mu) p(x, t)] + \frac{\sigma^2}{2} \frac{\partial^2 p(x, t)}{\partial x^2}
$$

---

## 3. 厳密解の導出

この方程式の厳密解を導出します。

**仮定と初期条件**
-   **初期条件**: 時刻 $t_0$ で粒子は位置 $x_0$ に存在する。    $$
    p(x, t_0) = \delta(x - x_0)
    $$-   **境界条件**: 無限遠では確率密度は0になる。
    $$
    \lim_{x \to \pm\infty} p(x, t) = 0
    $$
### 3.1. 解法1: ガウス分布を仮定する方法 (Ansatz法)

解がガウス分布 $p(x, t) = N(m(t), v(t))$ で与えられると仮定し、Fokker-Planck方程式に代入することで、平均 $m(t)$ と分散 $v(t)$ が満たすべき常微分方程式を導きます。

-   **平均 $m(t)$ の方程式**:    $$
    \frac{dm(t)}{dt} = -\frac{1}{\tau} (m(t) - \mu)
$$
 - **分散 $v(t)$ の方程式**:
    $$
    \frac{dv(t)}{dt} = -\frac{2}{\tau} v(t) + \sigma^2
    $$
初期条件 $m(t_0)=x_0$, $v(t_0)=0$ の下でこれらを解くと、

-   **平均**:
	$$
	    m(t) 
	    = \mu 
	    + (x_0 - \mu) e^{-\frac{t - t_0}{\tau}}
	$$
-   **分散**:
	$$
	    v(t) 
	    = \frac{\sigma^2 \tau}{2} \left( 1 - e^{-\frac{2(t - t_0)}{\tau}} \right)
    $$
これらをガウス分布の式に戻すことで、**時間依存解（遷移確率密度関数）** が得られます。
$$
p(x, t | x_0, t_0) = \frac{1}{\sqrt{2\pi v(t)}} \exp\left( - \frac{(x - m(t))^2}{2v(t)} \right)
$$
### 3.2. 解法2: フーリエ変換を用いる厳密な方法

より厳密に、解の形を仮定せずにフーリエ変換を用いて導出します。
1.  **フーリエ変換の定義と性質**
    $$
    \tilde{p}(k, t) = \mathcal{F}[p(x, t)] = \int_{-\infty}^{\infty} p(x, t) e^{-ikx} dx
    $$
    重要な性質として、実空間での $x$ の乗算は、フーリエ空間での微分に対応します。これはフーリエ変換の定義式を $k$ で微分することで導かれます。
    $$
    \mathcal{F}[x \cdot p(x, t)] = i \frac{\partial \tilde{p}(k, t)}{\partial k}
    $$

2.  **Fokker-Planck方程式の変換**
    この性質を使いFokker-Planck方程式をフーリエ変換すると、$\tilde{p}(k,t)$ に関する1階の偏微分方程式が得られます。
    $$
    \frac{\partial \tilde{p}}{\partial t} + \frac{k}{\tau} \frac{\partial \tilde{p}}{\partial k} = - \left(\frac{\sigma^2 k^2}{2} + \frac{i\mu k}{\tau}\right) \tilde{p}
    $$

3.  **変換された方程式の解**
    この方程式を解くと、$\tilde{p}(k,t)$ はガウス分布の特性関数（フーリエ変換された形）になることがわかります。
    $$
    \tilde{p}(k,t) = \exp\left[ i m(t) k - \frac{v(t)}{2} k^2 \right]
    $$
    ここで $m(t)$ と $v(t)$ は、Ansatz法で得られたものと全く同じです。

4.  **逆フーリエ変換**
    この特性関数を逆フーリエ変換することで、解 $p(x,t)$ がガウス分布であることが自然に導かれます。

---

## 4. 定常解

時間が十分に経過した $t \to \infty$ での定常解 $p_{st}(x)$ は、2つの方法で求められます。

1.  **時間依存解の極限**: $m(t \to \infty) = \mu$, $v(t \to \infty) = \frac{\sigma^2 \tau}{2}$ を解の式に代入する。
2.  **方程式から直接導出**: Fokker-Planck方程式で $\frac{\partial p}{\partial t} = 0$ とおき、得られる常微分方程式を解く。

どちらの方法でも、以下の定常解が得られます。

$$
p_{st}(x) = \frac{1}{\sqrt{\pi \sigma^2 \tau}} \exp\left( - \frac{(x - \mu)^2}{\sigma^2 \tau} \right)
$$

これは平均 $\mu$、分散 $\frac{\sigma^2\tau}{2}$ のガウス分布です。

---

## 5. 解法の一般性について

-   **フーリエ変換**は、Fokker-Planck方程式のドリフト係数と拡散係数が $x$ の**低次の多項式**である場合に特に有効な手法です。
-   係数が複雑な関数の場合は、フーリエ変換は有効ではなく、**固有関数展開**や**数値計算**といった他のアプローチが用いられます。

---

## 6. 参考文献

-   H. Risken, "The Fokker-Planck Equation: Methods of Solution and Applications"
-   C. W. Gardiner, "Handbook of Stochastic Methods for Physics, Chemistry and the Natural Sciences"
-   N. G. van Kampen, "Stochastic Processes in Physics and Chemistry"
# 🔗 関連
