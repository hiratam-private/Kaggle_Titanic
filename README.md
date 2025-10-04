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
- 2025-10-04: LR(C=1.0) + Title/Family → CV 0.8305 ± 0.0084, Holdout 0.8492
- メモ：balanced は低下。次は Age×Pclass・Cabin_notnull・FarePerPerson を1手ずつ検証。

## ライセンス
MIT
