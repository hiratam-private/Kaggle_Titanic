# kaggle-titanic-notes
目的: Titanicで前処理→LR→実験を積み上げ、再現できる形で記録する。

## 環境
- Python 3.10+
- pip install -r requirements.txt

## データ
Kaggleから各自取得。

## 実行
- notebooks/
  - 01_baseline_lr.ipynb（欠損補完 + One-Hot + LR）
  - 02_feature_title_family.ipynb（Title/FamilySize/IsAlone）

## 実験ログ（抜粋）
- 2025-10-04
  LR(C=1.0) + Title/Family
    → CV 0.8305 ± 0.0084, Holdout 0.8492(balanced は低下)

- 2025-10-05
  S1：LR(C=1.0) + Title/FamilySize/IsAlone + AgeをTitle中央値で補完 + Pclass(カテゴリ)
    → CV 0.8294 ± 0.0056, Hold-out 0.8436
- 2025-10-05
  S2（＋CabinKnown）：Cabinの有無フラグ追加
    → CV 0.8361 ± 0.0111, Hold-out 0.8268 → 採用（CV↑・分散許容内）
- 2025-10-05
  S3a（TicketPrefix）：Ticket先頭文字の系列化（レア圧縮）
    → CV 0.8316 ± 0.0136, Hold-out 0.8268 → 不採用（CV↓・分散↑）
- 2025-10-05
  S3b（FareLog）：Fareをlog1pで圧縮
    → CV 0.8305 ± 0.0147, Hold-out 0.8212 → 不採用（CV↓・分散↑）

- 2025-10-06
  S4 ＋ IsChild → CV 0.8350 ± 0.0099, Hold-out 0.8380 → 不採用（平均↓、分散わずかに改善）

現在ベスト：S2（Title／Age補完／FamilySize・IsAlone／CabinKnown／Pclass）→ CV ≈ 0.836（±0.011）

メモ：

判断はCV優先、Hold-outの上下は分割ゆらぎとみなす

## ライセンス
MIT
