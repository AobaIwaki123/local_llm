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
