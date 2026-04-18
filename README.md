# local_llm

最新のLLMモデルを試して知見を蓄積するプロジェクト。M5 / 32GB Mac上で動作。

## Models

| モデル | 特徴 | ドキュメント |
|--------|------|-------------|
| [qwen3.6-35b-a3b](./qwen3.6-35b-a3b/) | 35B MoE / SWE-Bench 73.4% / Apache 2.0 | [setup](./qwen3.6-35b-a3b/setup.md) / [tasks](./qwen3.6-35b-a3b/tasks.md) |

## MCP Servers

Claude Codeから利用できるMCPサーバーを追加済み。fetch/ollamaは `~/.claude/settings.json`、tavilyは `~/.claude.json` で管理。

### fetch

- **パッケージ**: `@modelcontextprotocol/server-fetch`
- **用途**: HuggingFace・OpenRouter・Ollama Libraryなど任意のURLからモデル情報を取得
- **API Key**: 不要
- **実行**: `npx -y @modelcontextprotocol/server-fetch`（Claude Code起動時に自動実行）

### ollama

- **パッケージ**: `@rawveg/ollama-mcp`
- **用途**: ローカルOllamaの操作（モデル一覧・pull・実行など14種のツール）
- **API Key**: 不要（ローカルOllamaに接続）
- **実行**: `npx -y @rawveg/ollama-mcp`（Claude Code起動時に自動実行）
- **前提**: Ollamaが起動していること (`ollama serve`)

### tavily

- **用途**: 最新LLMモデルのリリース情報・ニュース検索（AI向け最適化済み）
- **トランスポート**: HTTP（リモートMCP）
- **API Key**: 必要（URLクエリパラメータに埋め込み）
- **設定ファイル**: `~/.claude.json`（グローバル設定）
- **無料枠**: 1,000クレジット/月（ベーシック検索1件=1クレジット）
- **API Key再発行**: https://app.tavily.com
- **設定コマンド**:
  ```bash
  claude mcp add --transport http tavily https://mcp.tavily.com/mcp/?tavilyApiKey=<your-api-key>
  ```

## Python 環境

[uv](https://github.com/astral-sh/uv) で Python 3.12 の仮想環境を管理。

```bash
# 仮想環境の有効化
source .venv/bin/activate

# パッケージ追加例
uv pip install <package>
```

- Python バージョン: 3.12
- 仮想環境パス: `.venv/`
