# Dreamerエージェント: 予測誤差に基づく内発的報酬 (Prediction Error Intrinsic Reward)

## プロジェクト概要

このプロジェクトは、モデルベース強化学習アルゴリズムであるDreamer（ドリーマー）エージェントの実装です。特に、エージェントの探索戦略として**「予測誤差（Prediction Error）」に基づいた内発的報酬（Intrinsic Reward）**を活用するバージョンに焦点を当てています。

このバージョンは、LPM（Learning Progress Motivation）のよりシンプルな比較対象として機能することを目的としています。エージェントは、環境の次の状態をどれだけ正確に予測できるか（予測誤差の大きさ）に基づいて、自律的な好奇心による報酬を受け取り、世界を探索・学習します。

## 主要コンポーネント

*   `student_code.py`: Dreamerエージェントの核となるモデル群（RSSM, Encoder, Decoder, Actor, Criticなど）のアーキテクチャ定義を含みます。
*   `train_intrinsic.py`: エージェントのメインの学習ループ、環境とのインタラクション、内発的報酬の計算、モデルの更新ロジックなどを実装しています。
*   `Run_Intrinsic_PredictionError_Colab.ipynb`: Google Colaboratory環境で、本プロジェクトを簡単にセットアップし実行するためのJupyter Notebookファイルです。

## 特徴

*   **モデルベース強化学習 (Dreamerベース):** エージェントは内部に環境の世界モデルを構築し、そのモデル内で行動を想像・計画することで効率的な学習を実現します。
*   **予測誤差に基づく内発的報酬 (ICM着想):** エージェントの好奇心は、世界モデルの予測が外れた度合い（予測誤差）によって駆動され、未知の状況や予測が難しい状況を積極的に探索するよう促されます。
*   **Colab対応:** Google Colaboratoryで容易に環境構築と学習が開始できるように設計されたノートブックが付属します。
*   **モジュール化されたコード:** 学習ロジックとモデル定義が分離されており、見通しが良くなっています。

## セットアップと実行方法 (Google Colabでの利用)

`Run_Intrinsic_PredictionError_Colab.ipynb` ノートブックを使用することで、特別な設定なしにColab環境で本プロジェクトを実行できます。

1.  **ノートブックをColabで開く:**
    *   Google Driveにご自身の `Run_Intrinsic_PredictionError_Colab.ipynb` をアップロードし、Colabで開きます。

2.  **セルを上から順に実行:**
    *   ノートブックの各コードセルを上から順に実行してください。
    *   **「1. セットアップ」:** 必要なシステムライブラリをインストールします。
    *   **「2. Pythonライブラリのインストール」:** 必要なPythonライブラリをインストールします。
    *   **「3. 実行に必要なスクリプトファイルを作成」:** `student_code.py` と `train_intrinsic.py` がColab環境内に自動生成されます。
    *   **「4. Googleドライブのマウント」:** Google Driveをマウントし、プロジェクトの作業ディレクトリに移動します。学習成果はここに保存されます。
    *   **「5. WandB ログイン」:** 学習結果をオンラインで確認したい場合、WandBアカウントにログインします。
    *   **「6. 学習の実行」:** `train_intrinsic.py` スクリプトが実行され、Atari Breakout環境での学習が開始されます。

## スクリプトの実行例 (Colabノートブックの最終セル)

```python
import os
os.environ['MUJOCO_GL'] = 'egl'

# 学習を実行（Atari Breakout環境で50000ステップ、WandBロギング有効）
# --env-name: 使用する環境名（例: "ALE/Breakout-v5", "Pendulum-v1"など）
# --steps: 学習ステップ数
# --wandb: Weights & Biasesでのロギングを有効化
# --wandb-project: WandBのプロジェクト名
# --wandb-run-name: WandBの実行名
!python train_intrinsic.py --env-name "ALE/Breakout-v5" --steps 50000 --wandb --wandb-project 'Dreamer-Intrinsic-Reward' --wandb-run-name 'breakout-intrinsic-v1'
```

## 出力と結果の確認

*   **コンソール出力:**
    *   学習の進捗（エピソードごとの外部報酬平均など）が表示されます。
    *   `Training Steps: ... total_r=... wm_loss=...` のような形で、現在のステップ数、直近の報酬、世界モデルの損失が表示されます。
*   **`intrinsic_stats.csv`:**
    *   作業ディレクトリ（Google Drive上のプロジェクトフォルダ）に生成されるCSVファイルです。
    *   各ステップにおける `actual_error`（実際の予測誤差）と `intr_reward`（内発的報酬）が記録されます。
*   **Weights & Biases (WandB):**
    *   `--wandb` オプションを有効にして学習を実行すると、WandBのウェブサイト上でリアルタイムに詳細な学習ログ（損失、報酬の推移、内発的報酬のグラフなど）を確認できます。
*   **評価動画 (`eval_view/video/`):**
    *   作業ディレクトリ内の `eval_view/video/` フォルダに、評価時のエージェントの挙動を記録した動画（`eval_iter_*.mp4`）が保存されます。
    *   動画は、左側に**実際の環境画面**、右側に**エージェントの世界モデルによる再構成画像**を並べた比較形式になっています。これにより、エージェントが環境をどのように認識・理解しているかを視覚的に確認できます。

## 著作権とライセンス

本プロジェクトは、以下のライセンス条件および著作権情報に基づいています。

1.  **フォーク元プロジェクト:**
    *   本プロジェクトの主要なコードベースは、`Guch1120/world_model_group7` から派生したものです。
    *   **現状:** フォーク元のリポジトリには明示的なライセンスファイルがありません。これはデフォルトで「All Rights Reserved」を意味します。
    *   **推奨:** チーム内で、プロジェクト全体のライセンスについて議論し、MITライセンスなどの適切なオープンソースライセンスを追加することを強く推奨します。

2.  **`torch_truncnorm` コード (`student_code.py`内):**
    *   `student_code.py`内の `TruncatedStandardNormal` および `TruncatedNormal` クラスは、`https://github.com/toshas/torch_truncnorm` から流用されたものです。
    *   **ライセンス:** MITライセンス
    *   **義務:** 以下の著作権表示およびMITライセンスの全文を、プロジェクトの `LICENSE` ファイルに含める必要があります。また、`student_code.py` 内の該当コード付近にコメントで表示元とライセンスを明記することが推奨されます。
        ```
        Copyright (c) 2020 Anton Obukhov
        ```
    *   **対応:** 以下のコメントを `student_code.py` 内の該当コードブロックの上に追加してください。
        ```python
        # The following code is adopted from https://github.com/toshas/torch_truncnorm
        #
        # MIT License
        #
        # Copyright (c) 2020 Anton Obukhov
        #
        # Permission is hereby granted, free of charge, to any person obtaining a copy
        # of this software and associated documentation files (the "Software"), to deal
        # in the Software without restriction, including without limitation the rights
        # to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
        # copies of the Software, and to permit persons to whom the Software is
        # furnished to do so, subject to the following conditions:
        #
        # The above copyright notice and this permission notice shall be included in all
        # copies or substantial portions of the Software.
        #
        # THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
        # IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
        # FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
        # AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
        # LIABILITY, WHETHER IN AN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
        # OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
        # SOFTWARE.
        ```
        また、プロジェクトのルートディレクトリに `LICENSE` ファイルを作成し、上記のMITライセンス全文を記載してください。

3.  **あなたの貢献 (`train_intrinsic.py` および修正部分):**
    *   あなたがこのプロジェクトに追加した新たなコードや修正に対する著作権は、あなたに帰属します。
    *   プロジェクト全体でMITライセンスを採用した場合、そのライセンス条件に従ってご自身の著作権表示（例: `Copyright (c) 2026 あなたの名前`）を追加することが可能です。

## 今後の展望

この「予測誤差に基づく内発的報酬」バージョンは、より複雑なLPM（Learning Progress Motivation）ベースのエージェントと比較を行うための、シンプルで強力なベースラインとなります。このコードを起点に、様々な好奇心メカニズムや強化学習手法の探求を深めることができるでしょう。
