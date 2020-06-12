# Adversarial_valid

Adversarial Validatiinの実装
データセットはKaggleのsantanderのデータセット

実装段階（テスト段階）

1. 前処理
2. test-train-split
3. 分布可視化 -（実行段化）
4. 諸々の計算
5. 因果論勉強中（傾向スコアマッチング部分ができていない）
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

では、３つの手法の説明

### Automated Feature Selection
1つ目は自動的に特徴量を選定する方法です。
先程記載したのと同じようにAdversarial Validationの学習機を作成し学習データとテストデータと分類します。
（この学習機のことを以下からはAdversarial classifierと呼ぶ）

学習器の評価としてAUCが0.5に近くなるように特徴量の選定を行います。AUCが1.0〜0.8に近いような値が出ている場合はFeature importanceを計算し分類の精度に高く寄与している特徴量を省くと言うような工程を続け、AUCを0.5に近づけていく。

本論文での手順は以下の通りです。

1. Adversarial classifierで学習データとテストデータと分類
2. AUCを用いて学習機の評価（AUCに近づける0.5）を行い、Feature importanceの計算し重要度が高い特徴量の選定することやMDI>0.1の特徴を削除する
3. 学習機の精度が設定した閾値をAUCが下回らない場合は1.の工程に戻る
4. AUCが閾値を下回った場合、選定された特徴量を用いたデータセットの作成を行う

<div align="center">
<img src="https://github.com/TakumaTakami/Adversarial_valid/blob/images/img4.png" width="700px" class="imgs">
</div>

- メリット : 分布の偏りが防げるため正確な評価や一般化が見込める
- デメリット : 特徴量を選別するため、学習に用いるデータが減ってしまう

### Validation Data Selection

2つ目は学習データをtrainとvalに分割するときに、分布がval ≈ testになるようにする手法です。
傾向スコアマッチングなど用いてData Driftの検証を行う。（テスト群データ1つに対して最も傾向スコアが近いコントロール群のデータをマッチさせる手法）

1. Adversarial classifierで学習データとテストデータと分類
2. 傾向スコアがうまくマッチングできているかを確認するためstandardized mean difference（SMD）が0.1以下になるにデータの分割点（閾値）を決める
<img src="https://github.com/TakumaTakami/Adversarial_valid/blob/images/img5.png" width="600px" class="imgs">
3. 閾値より低いデータはvalデータに、閾値より高いものはtrainとしてのデータとする。









