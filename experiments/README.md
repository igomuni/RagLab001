# 実験インデックス

## 概要

このディレクトリには、完全な設定と結果を含むすべてのRAG実験が格納されています。
各実験は独立しており、ベクトル、結果、ドキュメントがすべて含まれています。

**目的**: 桃太郎の日本昔話テキストに対するRAGベースの質問応答において、LLMモデルと埋め込みモデルを体系的に比較する。

## 実施中の実験

| ID | 名前 | LLM | 埋め込み | ステータス | 日付 | レポート |
|----|------|-----|---------|----------|------|---------|
| exp_001 | baseline_ollama_phi3_nomic-v1 | Ollama phi3 | nomic-v1 | ✅ 完了 | 2026-01-13 | [レポート](../docs/20260113_0930_MVP実験結果_OllamaとLMStudio比較.md) |
| exp_002 | baseline_lmstudio_phi35_nomic-v1 | LM Studio phi-3.5 | nomic-v1 | ✅ 完了 | 2026-01-12 | [レポート](../docs/20260113_0930_MVP実験結果_OllamaとLMStudio比較.md) |
| exp_003 | moe_ollama_phi3_nomic-v2-moe | Ollama phi3 | nomic-v2-moe | 🚧 計画中 | - | - |
| exp_004 | moe_lmstudio_phi35_nomic-v2-moe | LM Studio phi-3.5 | nomic-v2-moe | 🚧 計画中 | - | - |

## 実験ディレクトリ構造

各実験ディレクトリの内容:

```
exp_XXX_name/
├── config.json           # 完全な実験設定（Git管理対象）
├── README.md             # 実験メモと発見事項（Git管理対象）
├── notebook.ipynb        # 実験用ノートブック（Git管理対象）
├── vectors/
│   ├── index.faiss       # FAISSベクトルインデックス（Git除外）
│   └── metadata.json     # チャンクメタデータ（Git除外）
└── results/
    └── experiment_results_YYYYMMDD_HHMMSS.json  # 実験結果（Git除外）
```

**重要**: ノートブックは各実験ディレクトリ内に配置されているため、`PROJECT_ROOT`パスは`Path.cwd().parent.parent`を使用します。

## 比較

### 埋め込みモデル別

- **nomic-v1** (ベースライン): exp_001, exp_002
- **nomic-v2-moe** (MoEアーキテクチャ): exp_003, exp_004

### LLMプロバイダー別

- **Ollama (phi3)**: exp_001, exp_003
- **LM Studio (phi-3.5)**: exp_002, exp_004

### ペア比較

1つの変数のみを変更した対照実験:

| 比較 | 変更した変数 | 実験 | 目的 |
|------|------------|------|------|
| LLMモデル | phi3 vs phi-3.5 | exp_001 vs exp_002 | モデル世代の比較 |
| 埋め込みモデル | v1 vs v2-moe | exp_001 vs exp_003 | MoE埋め込みの影響をテスト（Ollama） |
| 埋め込みモデル | v1 vs v2-moe | exp_002 vs exp_004 | MoE埋め込みの影響をテスト（LM Studio） |
| LLMモデル | phi3 vs phi-3.5 | exp_003 vs exp_004 | MoE埋め込み使用時のモデル比較 |

## 実験結果サマリー

### 主要な発見事項（exp_001 vs exp_002）

1. **RAGの必要性**: 両モデルともRAG無しでは深刻なハルシネーションを示す
2. **モデルの違い**:
   - Phi-3 (exp_001): 簡潔（平均140文字）、高速、軽量
   - Phi-3.5 (exp_002): 詳細（平均200文字）、高精度、やや冗長
3. **埋め込みモデル**: 両方ともnomic-v1（768次元）で正常に動作

### パフォーマンス比較

| 指標 | exp_001 (Ollama/phi3) | exp_002 (LM Studio/phi-3.5) |
|------|----------------------|----------------------------|
| RAG平均文字数 | 140.4文字 | 200.4文字 |
| Non-RAG平均文字数 | 122.4文字 | 276.8文字 |
| セットアップ複雑度 | ✅ シンプル（1ツール） | ⚠️ 中程度（2ツール） |
| 応答品質 | ✅ 正確 | ✅ より詳細 |
| 推奨用途 | 開発、プロトタイピング | 本番環境、高精度要求 |

## 実験の実行方法

### 既存のノートブックを使用

ノートブックは新しい実験構造を使用するように設定されています:

```python
EXPERIMENT_ID = "exp_001_baseline_ollama_phi3_nomic-v1"
EXPERIMENT_PATH = PROJECT_ROOT / "experiments" / EXPERIMENT_ID
```

1. ノートブックを開く（実験のREADMEでパスを確認）
2. `EXPERIMENT_ID`が実行したい実験と一致していることを確認
3. すべてのセルを実行
4. 結果は実験の`results/`ディレクトリに保存されます

### 新しい実験の作成

新しい実験を追加する場合（例: exp_005）:

1. **実験IDを決定**: 次の連番（exp_005、exp_006など）
2. **ディレクトリ構造を作成**:
   ```bash
   mkdir -p experiments/exp_005_description/{vectors,results}
   ```
3. **ノートブックをコピーして修正**: 類似する既存実験をベースにする
4. **設定を更新**:
   - ノートブックで`EXPERIMENT_ID`を設定
   - LLM/埋め込みモデルのパラメータを修正
5. **config.jsonを作成**: すべてのパラメータを記録
6. **実験を実行**: ノートブックをエンドツーエンドで実行
7. **発見事項を文書化**: 実験のREADME.mdを作成
8. **インデックスを更新**: このファイルの「実施中の実験」テーブルに新しい行を追加

## 移行メモ

### 経緯

この実験中心の構造は、2026年1月に以前のプロバイダーベースの構成（`data/vectors/ollama/`、`results/comparisons/lmstudio/`）を置き換えるために導入されました。

**旧構造**（非推奨だが後方互換性のため保持）:
- `data/vectors/{provider}/`
- `results/comparisons/{provider}/`

**新構造**（推奨）:
- `experiments/exp_XXX_{description}/`

### なぜ変更したのか？

旧構造では以下の質問に答えられませんでした:
- 「このインデックスを作成した埋め込みモデルは？」
- 「使用されたチャンクサイズは？」
- 「異なる埋め込みで同じLLMを比較できるか？」

新構造では、すべてのパラメータをディレクトリ名とconfig.jsonにエンコードすることで、これらの質問に簡単に答えられます。

## 将来の実験（アイデア）

### 埋め込みモデルのバリエーション
- exp_005: 異なる埋め込み次元
- exp_006: 多言語埋め込み（英語・中国語テキスト）

### LLMモデルのバリエーション
- exp_007: より大きなモデル（qwen2.5:7b、qwen3:8b）
- exp_008: 異なる量子化レベル（Q4 vs Q5）

### チャンキング戦略のバリエーション
- exp_009: より小さいチャンク（300文字、25オーバーラップ）
- exp_010: より大きいチャンク（1000文字、100オーバーラップ）
- exp_011: 文ベースのチャンキング

### 言語のバリエーション
- exp_012: 英語の桃太郎テキスト
- exp_013: 中国語の桃太郎テキスト
- exp_014: 多言語（日本語+英語+中国語）

## ツールとユーティリティ

### 実験比較（将来実装予定）

予定しているユーティリティスクリプト: `scripts/compare_experiments.py`

```bash
python scripts/compare_experiments.py exp_001 exp_002
# 出力: メトリクスの差分を含む詳細な比較レポート
```

### 実験作成（将来実装予定）

予定しているユーティリティスクリプト: `scripts/create_experiment.py`

```bash
python scripts/create_experiment.py \
  --llm ollama:phi3 \
  --embedding ollama:nomic-embed-text-v2-moe \
  --description "Testing MoE embedding"
# 出力: テンプレート付きのexp_003ディレクトリが作成される
```

## 関連ドキュメント

- [メインプロジェクトREADME](../README.md) - プロジェクト全体のドキュメント
- [MVP実験レポート](../docs/20260113_0930_MVP実験結果_OllamaとLMStudio比較.md) - exp_001 vs exp_002の詳細分析
- [個別実験README](./exp_001_baseline_ollama_phi3_nomic-v1/README.md) - 実験ごとの詳細な発見事項
