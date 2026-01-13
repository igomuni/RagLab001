# Momotaro RAG Experimentation System

ローカルLLMとFAISSを使用した、桃太郎物語に関するRAG（Retrieval-Augmented Generation）実験システム。

## 概要

このプロジェクトは、RAGの有無による回答品質の違いを実験的に検証することを目的としています。

**主な特徴:**
- 2つのLLMプロバイダーをサポート（Ollama、LM Studio）
- プロバイダー別のインデックス管理
- 軽量ベクトルDB（FAISS）
- 段落ベースの最適化されたチャンキング戦略
- 包括的な実験結果レポート

**実験結果:**
- ✅ **RAGの有効性を実証**: RAG使用時は正確な回答、非使用時は深刻なハルシネーション
- ✅ **プロバイダー比較完了**: Phi-3 vs Phi-3.5の性能差を定量化
- 📊 詳細は [実験結果レポート](docs/20260113_0930_MVP実験結果_OllamaとLMStudio比較.md) を参照

## 技術スタック

### LLMプロバイダー（選択可能）

| プロバイダー | モデル | 推奨用途 |
|------------|--------|---------|
| **LM Studio** | Phi-3.5-mini-instruct | 本番環境・高精度が必要 |
| **Ollama** | phi3:latest (2.2GB) | プロトタイピング・開発 |

### 共通コンポーネント

- **Vector Database**: FAISS (IndexFlatL2)
- **Embedding Model**: nomic-embed-text (768-dim, via Ollama)
- **Language**: Python 3.11+
- **Notebook**: Jupyter
- **Storage**: 外部SSD対応（両プロバイダー）

## セットアップ

### 1. 前提条件

**オプションA: LM Studio使用（高精度推奨）**
- Python 3.11以上
- [LM Studio](https://lmstudio.ai/) がインストール済み
- Ollama がインストール済み（エンベディング用: [インストール方法](https://ollama.ai)）
- 外付けSSDに保存されたモデルを使用可能

**オプションB: Ollama使用（手軽で推奨）**
- Python 3.11以上
- Ollama がインストール済み ([インストール方法](https://ollama.ai))

### 2. プロジェクトのセットアップ

```bash
# リポジトリのクローン（または作成済みの場合はスキップ）
cd RagLab001

# 仮想環境の作成
python3 -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate

# 依存関係のインストール
pip install -r requirements.txt
```

### 3. モデルのダウンロード

**オプションA: LM Studio使用の場合**
```bash
# エンベディングモデルのダウンロード（Ollama経由）
ollama pull nomic-embed-text

# LM Studioで以下を実行:
# 1. LM Studioを起動
# 2. Phi-3.5-mini-instructモデルをダウンロード（または外部SSDから読み込み）
# 3. 「Local Server」タブでサーバーを起動（デフォルト: http://localhost:1234）
```

**オプションB: Ollama使用の場合**
```bash
# LLMとエンベディングモデルのダウンロード
ollama pull phi3
ollama pull nomic-embed-text

# Ollamaが起動していることを確認
ollama list
```

### 4. データの準備

桃太郎のテキストファイルは既に含まれています:

```bash
data/raw/japanese/
  ├── aozora_jp_001.txt       # 青空文庫版 (2,001 chars, 5章)
  ├── traditional_jp_002.txt  # 伝統的詳細版 (3,197 chars, 6章)
  └── children_jp_003.txt     # 子供向け簡易版 (1,454 chars)
```

## 使い方

### クイックスタート

**オプションA: LM Studio版（高精度）**
```bash
# 1. LM Studioでサーバーを起動（http://localhost:1234）
# 2. Jupyter Notebookを起動
jupyter notebook notebooks/01_mvp_rag_experiment_lmstudio.ipynb
```

**オプションB: Ollama版（手軽）**
```bash
# Jupyter Notebookを起動
jupyter notebook notebooks/01_mvp_rag_experiment_ollama.ipynb
```

### ノートブックの内容

各ノートブックで以下を実行:

1. **セットアップとインポート** - 環境確認とライブラリ読み込み
2. **接続テスト** - LLMプロバイダーの接続確認
3. **データロード** - 3つの桃太郎テキストを読み込み
4. **チャンク化** - 段落ベース（500文字、50文字オーバーラップ）で15チャンク生成
5. **エンベディング生成** - 768次元ベクトルに変換
6. **FAISSインデックス構築** - ベクトル検索用インデックス作成
7. **検索テスト** - 類似チャンク検索の動作確認
8. **LLM統合** - RAGプロンプト構築とテキスト生成
9. **比較実験** - 5つの質問でRAG vs Non-RAGを比較
10. **結果保存** - JSONファイルとして保存
11. **統計分析** - 応答長、使用ソースの集計
12. **可視化** - グラフによる結果比較

### 実験の流れ

```
データ収集 → 前処理 → チャンク化 → エンベディング → インデックス作成
                                                          ↓
結果分析 ← 評価 ← 比較実験 ← LLM応答生成 ← 検索 ←--------┘
```

## プロジェクト構造

```
RagLab001/
├── data/
│   ├── raw/              # 生テキストデータ
│   │   └── japanese/     # 桃太郎テキスト（3ファイル）
│   └── vectors/          # FAISSインデックス（プロバイダー別）
│       ├── ollama/       # Ollama用インデックス
│       └── lmstudio/     # LM Studio用インデックス
├── notebooks/
│   ├── 01_mvp_rag_experiment.ipynb          # オリジナル版（汎用）
│   ├── 01_mvp_rag_experiment_ollama.ipynb   # Ollama専用版
│   └── 01_mvp_rag_experiment_lmstudio.ipynb # LM Studio専用版
├── results/
│   └── comparisons/      # 実験結果（プロバイダー別）
│       ├── ollama/       # Ollama実験結果JSON
│       └── lmstudio/     # LM Studio実験結果JSON
├── docs/                 # ドキュメント
│   └── 20260113_0930_MVP実験結果_OllamaとLMStudio比較.md
├── src/                  # ソースコード（将来の拡張用）
├── config/               # 設定ファイル
├── scripts/              # ユーティリティスクリプト
├── requirements.txt      # Python依存関係
├── .env                  # 環境変数
└── README.md
```

## 設定

`.env`ファイルで以下をカスタマイズ可能:

```bash
# LLMプロバイダーの選択
LLM_PROVIDER=lmstudio            # Options: lmstudio, ollama

# LM Studio設定
LMSTUDIO_BASE_URL=http://localhost:1234/v1
LMSTUDIO_MODEL_NAME=phi-3.5-mini-instruct
LMSTUDIO_MODEL_PATH=/Volumes/WD_BLACK SN7100 2TB Media/LMStudio/models/bartowski/Phi-3.5-mini-instruct-GGUF

# Ollama設定
OLLAMA_BASE_URL=http://localhost:11434
OLLAMA_MODEL_NAME=phi3
EMBEDDING_MODEL_NAME=nomic-embed-text

# チャンク設定
CHUNK_SIZE=500                   # チャンクサイズ（文字数）
CHUNK_OVERLAP=50                 # オーバーラップ
TOP_K_RESULTS=3                  # 検索結果数
VECTOR_DIMENSION=768             # エンベディング次元数
```

## 実験結果

### テスト質問（5問）

1. **プロット**: 桃太郎はどこから生まれましたか？
2. **登場人物**: 桃太郎は誰と一緒に鬼ヶ島へ行きましたか？
3. **詳細**: 桃太郎は動物たちに何をあげましたか？
4. **アクション**: 桃太郎は鬼ヶ島で何をしましたか？
5. **テーマ**: 桃太郎の物語が教える教訓は何ですか？

### 実験結果サマリー（2026年1月13日実施）

| 指標 | Ollama (phi3) | LM Studio (phi-3.5-mini) |
|------|---------------|------------------------|
| **RAG平均文字数** | 140.4 chars | 200.4 chars |
| **Non-RAG平均文字数** | 122.4 chars | 276.8 chars |
| **RAG応答精度** | ⚠️ やや不安定 | ✅ 高精度 |
| **RAG応答簡潔性** | ✅ 簡潔 | ⚠️ やや冗長 |
| **セットアップ** | ✅ 非常に簡単 | ⚠️ 2ツール必要 |

### 主な発見

1. **RAGの絶対的必要性**
   - RAG使用時: 両モデルとも正確な回答を生成
   - RAG非使用時: 深刻なハルシネーション（架空の人物・設定を創作）

2. **モデル世代差の影響**
   - **Phi-3.5** (LM Studio): コンテキストに忠実、事実抽出が正確
   - **Phi-3** (Ollama): コンテキストを過度に解釈、時々創作

3. **チャンキング戦略の妥当性**
   - 15チャンク生成、すべてTop-3検索で活用
   - 詳細版テキストが簡易版より優先される傾向

詳細な分析は [実験結果レポート](docs/20260113_0930_MVP実験結果_OllamaとLMStudio比較.md) を参照してください。

### 推奨用途

| シーン | 推奨プロバイダー |
|-------|----------------|
| **本番環境・高精度要求** | LM Studio (phi-3.5-mini) 🏆 |
| **プロトタイピング** | Ollama (phi3) |
| **開発・実験** | Ollama (phi3) |
| **速度・簡潔性重視** | Ollama (phi3) |
| **詳細な説明が必要** | LM Studio (phi-3.5-mini) |

## 評価基準

各回答を以下の観点で評価:
- **正確性** (1-5): 事実として正しいか
- **完全性** (1-5): 全ての側面をカバーしているか
- **一貫性** (1-5): 論理的に整合しているか
- **関連性** (1-5): 質問に答えているか

## 次のステップ

### 短期（1-2週間）
- [ ] 他のモデルでの実験（qwen2.5:7b、qwen3:8bなど）
- [ ] 自動評価メトリクスの実装（BLEU、ROUGE）
- [ ] チャンキング戦略の最適化実験

### 中期（1-2ヶ月）
- [ ] 多言語対応（英語、中国語版テキスト）
- [ ] コードのモジュール化（src/配下）
- [ ] CLI/APIインターフェースの実装

### 長期（3-6ヶ月）
- [ ] ハイブリッド検索（ベクトル + BM25）
- [ ] リランキング機能（Cross-Encoder）
- [ ] 自動評価パイプライン

## トラブルシューティング

### LM Studioに接続できない

1. LM Studioが起動しているか確認
2. 「Local Server」タブでサーバーが起動しているか確認
3. ブラウザで `http://localhost:1234/v1/models` にアクセスして、APIが応答するか確認
4. `.env`ファイルの `LMSTUDIO_BASE_URL` が正しいか確認
5. モデル名が正しいか確認（`phi-3.5-mini-instruct`は小文字）

### Ollamaに接続できない

```bash
# Ollamaが起動しているか確認
ollama list

# 必要なモデルがインストールされているか確認
ollama list | grep phi3
ollama list | grep nomic-embed-text

# モデルのダウンロード
ollama pull phi3
ollama pull nomic-embed-text
```

### エンベディングエラー

```bash
# nomic-embed-textモデルを再ダウンロード
ollama pull nomic-embed-text

# Ollamaが正しく動作しているか確認
ollama run nomic-embed-text "test"
```

### メモリ不足エラー

- LM Studioで量子化されたモデル（Q4, Q5など）を使用
- チャンクサイズを小さくする（例: 300文字）
- Top-K検索数を減らす（例: 2）

### 日本語フォントが表示されない（可視化）

```python
# matplotlibの設定を確認
import matplotlib
matplotlib.rcParams['font.sans-serif'] = ['Arial Unicode MS', 'Hiragino Sans', 'Yu Gothic', 'Meiryo']
```

### モデルの保存先を確認したい

```bash
# Ollamaのモデル保存先
echo $OLLAMA_MODELS
# デフォルト: ~/.ollama/models（環境変数で変更可能）

# LM Studioのモデル保存先
# LM Studio設定画面で確認
# このプロジェクトでは外部SSD: /Volumes/WD_BLACK SN7100 2TB Media/LMStudio/models
```

## ライセンス

MIT License

## 貢献

イシューやプルリクエストを歓迎します！

## 参考資料

### 公式ドキュメント
- [Ollama Documentation](https://github.com/ollama/ollama)
- [LM Studio](https://lmstudio.ai/)
- [FAISS Documentation](https://github.com/facebookresearch/faiss)

### RAG関連
- [RAG Overview - Anthropic](https://www.anthropic.com/research/retrieval-augmented-generation)
- [Microsoft Phi-3 Technical Report](https://arxiv.org/abs/2404.14219)

### プロジェクトドキュメント
- [MVP実験結果レポート](docs/20260113_0930_MVP実験結果_OllamaとLMStudio比較.md)

---

**実験を楽しんでください！** 🍑

**Recent Updates:**
- 2026-01-13: Ollama版実験完了、プロバイダー比較レポート追加
- 2026-01-12: LM Studio版MVP実験完了
- 2026-01-12: LM Studio外部SSD対応
- 2026-01-11: プロジェクト初期セットアップ
