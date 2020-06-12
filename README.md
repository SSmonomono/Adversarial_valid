# Adversarial_valid

Adversarial Validatiinの実装
データセットはKaggleのsantanderのデータセット

実装段階（テスト段階）

1. 前処理
2. test-train-split
3. 分布可視化 -（実行段化）
4. 諸々の計算
5. 因果論勉強中
6. 数値実証段階に移行

- 一応参考は*Adversarial Validation Approach Concept Drift Problem in Automated Machine Learing Systems*
- **ある程度の特徴量選別を行えるようにするのが目標**

## 論文内容の噛み砕き
### 概要
機械学習の運用にあたり、学習の精度は一定期間は保たれる。しかし時系列によって行動や統計的な数値にずれが生じる。
このようなズレはData driftと呼ばれる。

この論文ではData driftについてAdversarial Validationを用いて分布の差を検知している。

### Data driftが起こるとどうなる？？
以下の図を見ると、trainとvalidationの分布は似ているが、実際に予測したいtestの分布とは異なっている。
これでは、学習機の性能は発揮できない。

<div align="center">
<img src="https://github.com/TakumaTakami/Adversarial_valid/blob/images/img2.png" width="780px" class="imgs">
</div>

このような学習データとテストデータの違いを解決するために、この論文ではAdversarial Validationを用いた３つの方法が紹介されている。
1. Automated Feature Selection
2. Validation Data Selection
3. Inverse Propensity Weighting

### Adversarial Validationとは？
kaggleなどでも用いられる特徴量選択の手法としてよく知られています。
kaggleなどではLocalCVとLBのスコアが一致しないなどの問題が生じる場合がある。
以下の記事が参考になります。
https://upura.hatenablog.com/entry/2019/10/27/211137

以下の図のように学習データとテストデータの違いがある場合、精度に影響が出るのでAdversarial Validationをする。
まずは学習データとテストデータに0と1のラベルをつけてデータをシャッフルする。
次に学習データとテストデータと分類する学習器を用意する。
学習がうまく行く場合は学習データとテストデータの分布が乖離している可能性がある。
この時Feature importanceを計算し、学習データとテストデータを分類するための寄与度が高い特徴量を算出することができる。
これらの特徴量を省くことでAdversarial Validationの性能を下げ、データの乖離を防ぎます。

<div align="center">
<img src="https://github.com/TakumaTakami/Adversarial_valid/blob/images/img3.png" width="500px" class="imgs">
</div>

