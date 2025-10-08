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
  - 03_title_logreg（Cabinのフラグ追加）
  - 

## 実験ログ（抜粋）
- 2025-10-04
  LR(C=1.0) + Title/Family
    → CV 0.8305 ± 0.0084, Holdout 0.8492(balanced は低下)

- 2025-10-05
  - S1：LR(C=1.0) + Title/FamilySize/IsAlone + AgeをTitle中央値で補完 + Pclass(カテゴリ)
    → CV 0.8294 ± 0.0056, Hold-out 0.8436
  - S2（＋CabinKnown）：Cabinの有無フラグ追加
    → CV 0.8361 ± 0.0111, Hold-out 0.8268 → 採用（CV↑・分散許容内）
  - S3a（TicketPrefix）：Ticket先頭文字の系列化（レア圧縮）
    → CV 0.8316 ± 0.0136, Hold-out 0.8268 → 不採用（CV↓・分散↑）
  - S3b（FareLog）：Fareをlog1pで圧縮
    → CV 0.8305 ± 0.0147, Hold-out 0.8212 → 不採用（CV↓・分散↑）

- 2025-10-06
  - S4 ＋ IsChild → CV 0.8350 ± 0.0099, Hold-out 0.8380 → 不採用（平均↓、分散わずかに改善）

- 2025-10-07
  - S1：FarePP_log1p
    →  CV 0.8260 ± 0.0062（↓）
  - S1'：+FarePP_log1p + Age×Pclass
    →  CV 0.8294 ± 0.0058 → 保留（Holdout 0.8547）
  - S2：ランダムフォレスト（浅め）
    → CV 0.8316 ± 0.0136, Hold-out 0.8268 → 保留（CV維持・分散↑）
  - S3：GBC（shallow, lr=0.05, n=500, d=2, leaf=8）
   → CV 0.8485 ± 0.0149, Hold-out 0.7989 → 保留（CV↑・分散↑）安定性は要確認
  - S4：GBC（subsample=0.8, lr=0.03, n=800, leaf=10）
   → CV 0.8425 ± 0.0194, Hold-out 0.8156 → 不採用（CV維持・分散↑）
  - S5：LR + Grouped Age + AgeMissing
   → CV 0.8328 ± 0.0108, Hold-out 0.8492 → 不採用（CV維持・分散維持）
  - S6：LR + Grouped Age + FarePP + weak crosses
   → CV 0.8328 ± 0.0089, Hold-out 0.8380 → 不採用（Cを振ってもCV維持）
  - S7: LR + AgeBand + CabinKnown/Deck
   → CV 0.8361 ± 0.016~0.018, Hold-out 0.8436 → 不採用（CV↑・分散↓）
  - S7': LR（Deck集約, AgeBand4, 交差減）
   → CV 0.8361 ± 0.0141, Hold-out 0.8380 → 安定版として採用（CV↑・分散↓）
  - S7'': LR（Deck削除, CabinKnownのみ維持, AgeBand4, 交差Sex×Pclassのみ）
   → CV 0.8316 ± 0.0148, Hold-out 0.8380 → 不採用（CV↓・分散↑）
  - S8: GBC（CVスイープのみ）
   → GBCのベスト：lr=0.05, n_estimators=250, max_depth=2, CV=0.8406±0.0174
  - S8': LR + GBC
   → CV 0.8350 ± 0.0170, Hold-out 0.8212 → 不採用（CV↓・分散↑）

- 2025-10-08
  - S1: LR + TicketGroupSize
    → CV 0.8283 ± 0.0094, Hold-out 0.8268 → 不採用（冗長？）
  - S2: LR + TicketShared（二値）
    → CV 0.8260 ± 0.0097, Hold-out 0.8260 → 不採用（冗長？）
  - S3: LR + TicketGroupSize（cap4→log1p）
    → CV 0.8223 ± 0.0119, Hold-out 0.8268 → 不採用（冗長？）
  - S4:  LR（Ticket系なし対照）
    → CV 0.8272 ± 0.0044, Hold-out 0.8268 → Ticket系不採用
    
現在ベスト：S7'
-Title正規化/FamilySize・IsAlone・FarePerPerson/AgeBand4/CabinKnown/Sex×Pclass
→ CV ≈ 0.8317（±0.0117）

メモ：

判断はCV優先、Hold-outの上下は分割ゆらぎとみなす

## ライセンス
MIT
