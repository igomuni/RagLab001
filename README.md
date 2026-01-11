# Momotaro RAG Experimentation System

ローカルLLM（Phi3）とFAISSを使用した、桃太郎物語に関するRAG（Retrieval-Augmented Generation）実験システム。

## 概要

このプロジェクトは、RAGの有無による回答品質の違いを実験的に検証することを目的としています。

**主な特徴:**
- ローカル実行（Ollama + Phi3モデル）
- 軽量ベクトルDB（FAISS）
- 多言語対応予定（日本語、英語、中国語）
- MVPアプローチでの段階的実装

## 技術スタック

- **LLM**: LM Studio + Phi-3.5-mini-instruct (または Ollama + Phi3)
- **Vector Database**: FAISS
- **Embedding Model**: nomic-embed-text (via Ollama)
- **Language**: Python 3.11+
- **Notebook**: Jupyter

## セットアップ

### 1. 前提条件

**オプションA: LM Studio使用（推奨）**
- Python 3.11以上
- [LM Studio](https://lmstudio.ai/) がインストール済み
- Ollama がインストール済み（エンベディング用: [インストール方法](https://ollama.ai)）
- 外付けSSDに保存されたモデルを使用可能

**オプションB: Ollama使用**
- Python 3.11以上
- Ollama がインストール済み ([インストール方法](https://ollama.ai))
- Ollamaが起動していること

### 2. プロジェクトのセットアップ

```bash
# リポジトリのクローン（または作成済みの場合はスキップ）
cd RagLab001

# 仮想環境の作成
python3 -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate

# 依存関係のインストール
pip install -r requirements.txt

# エンベディングモデルのダウンロード（Ollama経由）
ollama pull nomic-embed-text

# オプションB（Ollama使用）の場合のみ：
# ollama pull phi3
```

### 3. データの準備

桃太郎のテキストを以下のディレクトリに配置:

```bash
data/raw/japanese/
  ├── aozora_jp_001.txt    # 青空文庫版
  ├── wikipedia_jp_001.txt # Wikipedia版
  └── ...
```

**推奨データソース:**
- [青空文庫](https://www.aozora.gr.jp/) - 日本の古典・近代文学
- [Wikipedia - 桃太郎](https://ja.wikipedia.org/wiki/%E6%A1%83%E5%A4%AA%E9%83%8E)
- 子供向け童話サイト

**ファイル命名規則:**
- `source_language_id.txt`
- 例: `aozora_jp_001.txt`, `wikipedia_en_001.txt`

## 使い方

### LM Studio のセットアップ（オプションA使用時）

1. LM Studioを起動
2. 外付けSSD上のモデルを読み込む:
   - パス: `/Volumes/WD_BLACK SN7100 2TB Media/LMStudio/models/bartowski/Phi-3.5-mini-instruct-GGUF`
3. 「Local Server」タブでサーバーを起動
   - デフォルトURL: `http://localhost:1234`
4. サーバーが起動していることを確認

### MVPバージョン（推奨開始点）

**オプションA: LM Studio版（推奨）**
```bash
# Jupyter Notebookを起動
jupyter notebook notebooks/01_mvp_rag_experiment_lmstudio.ipynb
```

**オプションB: Ollama版**
```bash
# Jupyter Notebookを起動
jupyter notebook notebooks/01_mvp_rag_experiment.ipynb
```

ノートブックで以下を実行:
1. データの読み込みと前処理
2. テキストのチャンク化
3. エンベディング生成
4. FAISSインデックス構築
5. 検索機能のテスト
6. RAG vs Non-RAG 比較実験
7. 結果の分析

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
│   │   ├── japanese/
│   │   ├── english/
│   │   └── chinese/
│   ├── processed/        # 処理済みデータ
│   └── vectors/          # FAISSインデックス
├── notebooks/
│   └── 01_mvp_rag_experiment.ipynb  # MVP実験ノートブック
├── results/
│   └── comparisons/      # 実験結果
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
LMSTUDIO_MODEL_NAME=Phi-3.5-mini-instruct
LMSTUDIO_MODEL_PATH=/Volumes/WD_BLACK SN7100 2TB Media/LMStudio/models/bartowski/Phi-3.5-mini-instruct-GGUF

# Ollama設定（エンベディング用）
EMBEDDING_MODEL_NAME=nomic-embed-text

# チャンク設定
CHUNK_SIZE=500                   # チャンクサイズ（文字数）
CHUNK_OVERLAP=50                 # オーバーラップ
TOP_K_RESULTS=3                  # 検索結果数
```

## 実験例

### テスト質問

1. **プロット**: 桃太郎はどこから生まれましたか？
2. **登場人物**: 桃太郎は誰と一緒に鬼ヶ島へ行きましたか？
3. **詳細**: 桃太郎は動物たちに何をあげましたか？
4. **アクション**: 桃太郎は鬼ヶ島で何をしましたか？
5. **テーマ**: 桃太郎の物語が教える教訓は何ですか？

### 評価基準

各回答を以下の観点で評価:
- **正確性** (1-5): 事実として正しいか
- **完全性** (1-5): 全ての側面をカバーしているか
- **一貫性** (1-5): 論理的に整合しているか
- **関連性** (1-5): 質問に答えているか

## 結果

実験結果は `results/comparisons/` に保存されます:

```json
{
  "question_id": "q1",
  "question": "桃太郎はどこから生まれましたか？",
  "rag": {
    "response": "...",
    "context_chunks": [...],
    "response_length": 87
  },
  "no_rag": {
    "response": "...",
    "response_length": 45
  }
}
```

## 次のステップ

MVPが成功したら、以下の拡張を検討:

- [ ] 多言語データの追加（英語、中国語）
- [ ] 評価メトリクスの自動化
- [ ] 異なるチャンク戦略の実験
- [ ] リランキング機能の追加
- [ ] コードのモジュール化
- [ ] 実験スクリプトの自動化

## トラブルシューティング

### LM Studioに接続できない

1. LM Studioが起動しているか確認
2. 「Local Server」タブでサーバーが起動しているか確認
3. ブラウザで `http://localhost:1234/v1/models` にアクセスして、APIが応答するか確認
4. `.env`ファイルの `LMSTUDIO_BASE_URL` が正しいか確認

### 外付けSSD上のモデルが見つからない

1. 外付けSSDがマウントされているか確認
2. LM Studioの設定で、モデルパスが正しく設定されているか確認
3. LM Studioでモデルを手動で読み込む

### Ollamaに接続できない（エンベディング用）

```bash
# Ollamaが起動しているか確認
ollama list

# Ollamaを起動
ollama serve
```

### エンベディングエラー

```bash
# nomic-embed-textモデルをダウンロード
ollama pull nomic-embed-text
```

### メモリ不足エラー

- LM Studioで量子化されたモデル（Q4, Q5など）を使用
- チャンクサイズを小さくする（例: 300文字）
- バッチサイズを減らす

### 日本語フォントが表示されない（可視化）

```python
# matplotlibの設定を確認
import matplotlib
print(matplotlib.matplotlib_fname())
```

## ライセンス

MIT License

## 貢献

イシューやプルリクエストを歓迎します！

## 参考資料

- [Ollama Documentation](https://github.com/ollama/ollama)
- [FAISS Documentation](https://github.com/facebookresearch/faiss)
- [RAG Overview](https://www.anthropic.com/research/retrieval-augmented-generation)

---

**実験を楽しんでください！** 🍑
