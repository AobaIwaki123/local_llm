# セットアップ手順

## 前提条件

| 要件 | 確認コマンド |
|------|-------------|
| Ollama インストール済み | `ollama --version` |
| 32GB 以上の Unified Memory | `sysctl -n hw.memsize` |
| 24GB 以上の空きストレージ | `df -h ~` |
| 他の重いアプリを終了済み | — |

## 1. モデルをダウンロード

```bash
ollama pull qwen3.6:35b-a3b
```

- サイズ: 約 24GB
- 所要時間: 回線速度による（1Gbps で約 3 分、100Mbps で約 30 分）
- 保存先: `~/.ollama/models/`

進捗確認:

```bash
# 別ターミナルで
ollama list
```

## 2. 起動

```bash
ollama run qwen3.6:35b-a3b
```

起動後は対話プロンプトが表示される。

```
>>> ここに入力
```

## 3. 終了

```
>>> /bye
```

またはモデルをメモリから解放する場合:

```bash
ollama stop qwen3.6:35b-a3b
```

## 4. API として使う

Ollama は起動中に OpenAI 互換 API を `localhost:11434` で公開する。

```bash
# Ollama をサービスとして起動
ollama serve &

# チャット API を叩く
curl http://localhost:11434/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "qwen3.6:35b-a3b",
    "messages": [{"role": "user", "content": "Hello!"}]
  }'
```

## Tips

### メモリ節約

32GB ギリギリのため、他アプリを閉じてから起動する。

```bash
# 並列リクエスト数を制限（デフォルト: 4）
OLLAMA_NUM_PARALLEL=1 ollama serve
```

### コンテキスト長を伸ばす

デフォルトのコンテキスト長は 4K。長文処理時は明示的に指定する。

```bash
ollama run qwen3.6:35b-a3b --ctx-size 32768
```

### 実行中モデルの確認

```bash
ollama ps
```

## トラブルシューティング

| 症状 | 対処 |
|------|------|
| `out of memory` | 他アプリを閉じて再起動。`OLLAMA_NUM_PARALLEL=1` を設定 |
| 応答が遅い | `ollama ps` でモデルが GPU に載っているか確認 |
| モデルが見つからない | `ollama list` で名前を確認。`qwen3.6:35b-a3b` は完全一致が必要 |
| pull が途中で止まる | 同じコマンドを再実行すると再開できる |
| **M5 チップで 500 エラー（全モデル失敗）** | 下記「M5 チップでの既知バグと代替手段」を参照 |

---

## M5 チップでの既知バグと代替手段

> **調査日**: 2026-04-19 / 詳細: `../research/ollama_m5_metal_bug_20260419.md`

Ollama 0.19〜0.21.x + Apple M5 + macOS 26 (Tahoe) の組み合わせでは、Metal シェーダーのコンパイルに失敗し**全モデルが 500 エラーで動作不能**になる既知バグがある（[ollama/ollama#15448](https://github.com/ollama/ollama/issues/15448) 他多数）。

`OLLAMA_NUM_GPU=0` や CPU only モードでも回避できない。2026-04-19 時点で根本修正は未リリース。

### 代替手段 1: Ollama 0.18.3 にダウングレード

同ハードウェアでの動作が確認されている最終バージョン。

```bash
# Homebrew 経由
brew uninstall ollama
brew install ollama@0.18.3
```

Homebrew に古いバージョンがない場合は [GitHub Releases v0.18.3](https://github.com/ollama/ollama/releases/tag/v0.18.3) から `.dmg` を直接取得する。

### 代替手段 2: LM Studio（GUI、推奨）

LM Studio は独自の Metal 実装を使用しており、M5 + macOS 26 でも動作する。  
Qwen3 系モデルも GGUF 形式で読み込み可能で、OpenAI 互換のローカル API サーバーとしても使える。

1. [lmstudio.ai](https://lmstudio.ai) からインストール
2. 検索バーで `qwen` と入力し `Qwen3-30B-A3B` 等の GGUF モデルをダウンロード
3. **Local Server** タブで起動（デフォルト: `localhost:1234`）

```bash
# API 呼び出しは Ollama とほぼ同じ形式
curl http://localhost:1234/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "qwen3-30b-a3b",
    "messages": [{"role": "user", "content": "Hello!"}]
  }'
```

### 代替手段 3: llama-server（llama.cpp 直接）

llama.cpp の Homebrew ビルドは Ollama より新しい Metal シェーダーを含む場合があり、M5 で動作することがある。

```bash
brew install llama.cpp

# モデルは Hugging Face から GGUF を直接取得
# 例: https://huggingface.co/Qwen/Qwen3-30B-A3B-GGUF

llama-server \
  --model ~/path/to/qwen3-30b-a3b-q4_k_m.gguf \
  --port 8080 \
  --ctx-size 32768
```

API エンドポイントは `http://localhost:8080/v1/chat/completions`（OpenAI 互換）。
