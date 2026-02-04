# ロボット超音波診断システム(RUDS)を用いた血管3Dモデル構築

## 概要 (Overview)

本リポジトリでは、ロボット超音波診断システム（RUDS: Robotic Ultrasound Diagnosis System）を用いて構築した超音波診断ファントム「ABDFAN」（京都科学製）の血管3Dモデルおよび構築手法について公開します。

### 背景と目的
自らの実験において超音波診断ファントムABDFANの3Dモデル（特に血管構造）が必要でしたが、公式から公開されているものがありませんでした。また、以下の理由により従来の医用画像モダリティでは正確な3Dモデルの構築が困難でした：

* **MRI撮影の制約:** ファントム内部に金属部品が含まれており、金属アーチファクトによりMRI撮影が困難。
* **CTコントラスト不足:** CTスキャンではファントム内の血管と周囲組織の輝度差が乏しく、正確なセグメンテーションが困難。

そのため、本研究では**超音波画像のみを用いた3Dモデル構築手法**を提案し、ロボット制御による高精度スキャンと画像処理技術を組み合わせることで、血管3Dモデルの構築に成功しました。本リポジトリでは、この手法および構築したデータセットを公開し、同様の課題を持つ研究者の参考に供します。

## システム構成 (System Configuration)

* **ロボット:** RUDS (Robotic Ultrasound Diagnosis System)
* **ファントム:** 超音波診断ファントム "ABDFAN" (株式会社京都科学製)
* **座標系:** ロボットベッド部を$z$軸（頭尾方向）に移動させながら、アキシャル面($xy$平面)の超音波画像を取得しています。

![System Overview](images/system_overview.jpg)
*Fig 1. RUDSのシステム構成および座標系定義*

## 構築手法 (Methodology)

### 1. データ取得 (Data Acquisition)
ロボット制御により、以下のプロトコルで高精度なスキャンを実施しました。

* **Tilting (プローブ傾斜):** 肋骨の音響陰影を避けるため、プローブを$15.0\,\mathrm{deg}$傾斜させて固定。
* **Scanning Path (走査経路):**
    * **Path 1:** $z$軸方向に **0.5mm 間隔** でスキャン。
    * **Offset:** 1列目の終了後、ロボット手先を$x$軸（横）方向に **45.0mm**、$z$軸方向に55.0mm移動。
    * **Path 2:** 2列目を同様に0.5mm間隔でスキャン。

![Scanning Path](images/scanning_path.jpg)
*Fig 2. スキャン経路およびオーバーラップの概要*

![Robot Imaging](images/robot_imaging.jpg)
*Fig 3. RUDSによる肝臓スキャンの様子 (a) スキャン開始位置 (b) Path 1 (c) Path 2 / Liver scan with RUDS (a) Scan starting point (b) Path 1 (c) Path 2*

### 2. 画像結合 (Image Stitching)
広範囲な肝臓断面を得るため、Path 1とPath 2の画像を結合しています。
ロボットのエンコーダ値による幾何学的な結合では、ファントム表面の凹凸によるプローブ圧着時の微細なズレが生じるため、**テンプレートマッチング**を用いて重複領域の最適化を行いました。

### 3. 3D再構築 (3D Reconstruction)
* **アノテーション:** 結合画像に対し、血管（動脈・静脈・門脈）および腫瘍を手動でラベリング（Labelmeを使用）。
* **点群生成:** 画像上のピクセル座標($x, y$)とロボットのエンコーダ値($z$)を統合し、3次元点群データを生成。
* **メッシュ処理:** 点群解析ソフト（MeshLab）を使用し、外れ値除去、ボクセルグリッドフィルタ、Screened Poisson Surface Reconstruction、およびスムージング処理を実施して最終的なメッシュモデルを構築しました。

以下は構築された3Dモデルの例です。

![3D Model Result](images/3d_model_result.jpg)
*Fig 4. 再構築された血管3Dモデル*

## データセットの仕様 (Dataset Specifications)

**注記:** データセット（生データ、結合画像、3Dモデル）は容量の都合上、Google Driveにて管理しています。利用をご希望の方は後述の方法でご連絡ください。

### 提供フォーマット
* **3Dモデル:** `.ply` (Polygon File Format)
* **画像データ:** `.png` (結合済み超音波画像)

### 固定パラメータ
本データセットは、以下の固定パラメータに基づいて構築されています。
* **ピクセルスペーシング:** **41 pixels / 10 mm** (固定)
* **スライス厚:** 0.5 mm

## データセットの入手方法 (Access)

本データセットは、研究・開発目的での利用に限り提供可能です。

**申請方法:**
以下のメールアドレスまで、「所属」「氏名」「利用目的」を明記の上、ご連絡ください。
* **Contact:** [ここにメールアドレスを記載してください]

内容を確認後、Google Driveの共有リンクをご案内いたします。

## 引用について (Citation)

本データセットまたは手法を研究・開発にご利用いただく際は、以下の文献の引用をお願いいたします。

```bibtex
@inproceedings{YourName202X,
  title={Construction of 3D Vascular Models using Robotic Ultrasound...},
  author={Your Name and Co-authors},
  booktitle={Proceedings of [Conference Name]},
  year={202X},
  pages={xx--xx}
}