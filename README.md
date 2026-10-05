# Qwen3.5-9B Japanese Word Association Norms (日本語自由連想基準表)

大規模言語モデル **Qwen3.5-9B** を被験者として構築した、日本語の自由連想基準表（Word Association Norms）データセットです。  
心理学・認知科学における言語連想研究（深尾・大河内 1993 など）の刺激語に基づき、各刺激語に対して 300 回の独立サンプリングを行って得られた連想反応と出現頻度・連想強度を収録しています。

---

## 収録データ

| ファイル | 説明 | 刺激語数 | ペア数 | ユニーク語彙数 |
| :--- | :--- | :---: | :---: | :---: |
| [`data/cleaned_association_norms_qwen3.5_9b.csv`](data/cleaned_association_norms_qwen3.5_9b.csv) | **推奨版**: NFKC正規化、Markdown記法、拒否文、プロンプト残骸等を除去したクリーンデータ | 138 | 2,015 | 1,563 |
| [`data/llm_association_norms_qwen3.5_9b.csv`](data/llm_association_norms_qwen3.5_9b.csv) | **生データ**: モデルから生成された全連想ペアの生集計 | 139 | 2,068 | 1,617 |

---

## カラム仕様

CSV ファイルは UTF-8 エンコーディング（カンマ区切り）です。

| カラム名 | 型 | 説明 | 例 |
| :--- | :--- | :--- | :--- |
| `刺激語` | string | モデルに提示された刺激単語 | `頭`, `緑`, `幸せ` |
| `反応語` | string | モデルが連想・出力した単語 | `思考`, `自然`, `笑顔` |
| `合計頻度` | int | 300 試行中で該当反応語が出現した回数 | `255`, `190` |
| `連想強度` | float | 連想出現確率（`合計頻度` / `サンプル数N`） | `0.85`, `0.6333` |
| `サンプル数N` | int | 各刺激語あたりの試行回数 | `300` |
| `モデル名` | string | 生成に使用したモデル識別名 | `Qwen3.5-9B` |

---

## 生成方法・実験設定

- **対象モデル**: Qwen3.5-9B
- **試行回数**: 各刺激語につき 300 回（独立サンプリング）
- **プロンプト**:
  ```text
  あなたは言語連想実験の被験者です。
  以下の単語を聞いて、心に最初に思い浮かんだ単語を1つだけ答えてください。
  余計な説明や前置きは一切書かず、単語のみを出力してください。

  単語: {刺激語}
  答え:
  ```

---

## 利用方法（Python 例）

### 1. pandas で読み込み

```python
import pandas as pd

# クリーニング済みデータの読み込み
df = pd.read_csv("data/cleaned_association_norms_qwen3.5_9b.csv")

# 特定の刺激語（例: 「幸せ」）の連想反応上位を取得
subset = df[df["刺激語"] == "幸せ"].sort_values("連想強度", ascending=False)
print(subset[["刺激語", "反応語", "合計頻度", "連想強度"]].head(10))
```

### 2. 各刺激語の Top-1（最強）連想ペアの抽出

```python
# 各刺激語で最も連想強度の高いペアを抽出
top1_df = df.sort_values("連想強度", ascending=False).groupby("刺激語").first().reset_index()
print(top1_df[["刺激語", "反応語", "連想強度"]].head())
```

---

## ライセンス

本データセットは [Creative Commons Attribution 4.0 International (CC BY 4.0)](https://creativecommons.org/licenses/by/4.0/) の下で公開されています。学術研究・商用利用を問わず自由にご利用いただけます。
