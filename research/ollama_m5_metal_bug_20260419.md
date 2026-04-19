# Ollama M5 チップ Metal シェーダーバグ 調査

**調査日**: 2026-04-19  
**最終更新**: 2026-04-19（動作確認追記）  
**情報取得**: Tavily MCP（Web検索）+ GitHub Issues + 実機検証

---

## 概要

Ollama 0.19〜0.21.x + Apple M5 + macOS 26 (Tahoe) の組み合わせで、全モデルの推論が `500 Internal Server Error` で失敗する既知バグ。

---

## 根本原因

**Metal Performance Primitives の型チェック不一致**

`ggml` の Metal バックエンドが `MPPTensorOpsMatMul2d`（行列積演算）を呼び出す際、M5 チップの GPU ファミリー (`MTLGPUFamilyApple10` / `MTLGPUFamilyMetal4`) では `bfloat` と `half` の型が厳格に一致していることを要求する。しかし Ollama/ggml 側のシェーダーコードが両者を混在させているため Metal コンパイラが `static_assert` で失敗する。

```
error: static_assert failed due to requirement '__tensor_ops_detail::__is_same_v<bfloat, half>'
"Input types must match cooperative tensor types"
  → /System/Library/Frameworks/MetalPerformancePrimitives.framework/Headers/__impl/MPPTensorOpsMatMul2dImpl.h:3266

ggml_metal_init: error: failed to initialize the Metal library
ggml_backend_metal_device_init: error: failed to allocate context
panic: unable to create llama context
llama runner terminated: exit status 2
```

macOS 26 (Tahoe) が M5 の新 GPU ファミリーに対しこの型チェックをより厳格に施行するようになったことで顕在化。**OS + 新チップ + 古いシェーダーコードの三重要因**。

---

## 影響範囲

| 項目 | 詳細 |
|------|------|
| 影響バージョン | Ollama 0.20.5 〜 0.21.x（2026-04-19 時点） |
| **動作確認済み（実機）** | **Ollama 0.20.1** で qwen3.6:35b-a3b の推論を確認（2026-04-19） |
| 動作確認済み（報告）| Ollama 0.18.3 も同ハードウェアで正常動作（GitHub Issues より） |
| バグ混入バージョン | 0.20.5 以降で顕在化（0.20.1 → 0.20.5 の間に導入） |
| チップ | Apple M5, M5 Pro, M5 Max (`MTLGPUFamilyApple10`) |
| OS | macOS 26 (Tahoe) 26.3.1 以降 |
| 失敗モデル | 全モデル（qwen2.5:0.5b のような極小モデルも含む） |

---

## 挙動の特徴（ハマりポイント）

- `OLLAMA_NUM_GPU=0`（CPU only モード）を指定しても**回避不可能** — Metal ライブラリの初期化がモデル実行前に必ず走るため
- `OLLAMA_LLM_LIBRARY=cpu` も同様に効果なし
- Metal コンパイラキャッシュ削除 (`~/Library/Caches/com.apple.metal`) も無効
- `~/.ollama` 完全削除・再インストールも無効
- RAMや他リソースの問題ではない（0.5B モデルも同様に失敗）

---

## 関連 GitHub Issues

| Issue | タイトル |
|-------|---------|
| [ollama/ollama#15448](https://github.com/ollama/ollama/issues/15448) | M5 + macOS 26.3.1: 500エラー、CPU modeも失敗 |
| [ollama/ollama#15470](https://github.com/ollama/ollama/issues/15470) | M5 で gemma:26b クラッシュ |
| [ollama/ollama#15487](https://github.com/ollama/ollama/issues/15487) | M5 Max (MTLGPUFamilyMetal4) Metalシェーダーコンパイル失敗 |
| [ollama/ollama#15496](https://github.com/ollama/ollama/issues/15496) | M5 MacBook Air で qwen2.5:0.5b すら失敗 |
| [ollama/ollama#15541](https://github.com/ollama/ollama/issues/15541) | bfloat/half ミスマッチの詳細分析 |
| [ollama/ollama#15594](https://github.com/ollama/ollama/issues/15594) | macOS Tahoe 26.3.1 での static_assert 失敗 |
| [ollama/ollama-python#643](https://github.com/ollama/ollama-python/issues/643) | Python SDK 経由でも同症状 |

---

## 修正状況（2026-04-19 時点）

**PR #15550**（2026-04-13 マージ）: Gemma4 モデル固有の未初期化変数によるコンパイルエラーを修正。ただし**部分的な fix** であり、M5 全体の `bfloat/half` 型不一致の根本問題は open のまま。

v0.21.0 は `v0.20.7...v0.21.0` のチェンジセットで主に Hermes Agent 等の新機能が中心。M5 Metal の包括的修正は含まれていない模様。

---

## ワークアラウンド

### 1. Ollama 0.20.1 にダウングレード（実機動作確認済み・推奨）

**2026-04-19 に Apple M5 + macOS 26 実機で動作確認済み。**  
`qwen3.6:35b-a3b` (23GB) の推論が正常に完了することを確認。

```bash
# Homebrew を使わず GitHub Releases から .dmg を直接取得
brew uninstall ollama  # Homebrew 版が入っている場合
curl -L https://github.com/ollama/ollama/releases/download/v0.20.1/Ollama-darwin.dmg \
  -o ~/Downloads/Ollama-0.20.1.dmg
open ~/Downloads/Ollama-0.20.1.dmg

# バージョン確認
ollama --version  # → ollama version is 0.20.1
```

> モデルデータ (`~/.ollama/models/`) はアンインストールしても削除されないため、再ダウンロード不要。

### 1b. Ollama 0.18.3 にダウングレード（GitHub Issues で報告あり）

0.18.3 での動作は他ユーザーの報告による。0.20.1 が利用可能なため通常は不要。

```bash
# GitHub Releases から直接取得
# https://github.com/ollama/ollama/releases/tag/v0.18.3
```

### 2. LM Studio を代替として使用

ggml の Metal バックエンドを直接使わない実装のため、M5 でも動作する。  
→ 詳細は `qwen3.6-35b-a3b/setup.md` の「代替ツール」セクション参照。

### 3. llama-server（llama.cpp 直接）を使用

llama.cpp の最新ビルドは M5 対応の Metal シェーダーを含む可能性がある。

```bash
brew install llama.cpp
llama-server --model ~/.ollama/models/blobs/<model-blob> --port 8080
```

### 4. MLX backend（Ollama 0.19+ のプレビュー機能）

Ollama 0.19 以降は MLX バックエンドが別途存在し、M5 の GPU Neural Accelerator を活用できる場合がある。ただし全モデルが対応しているわけではない。

---

## 参考リンク

- [Reddit: Metal shader crash on every model (500 error)](https://www.reddit.com/r/ollama/comments/1smexbv/ollama_broken_on_apple_m5_macos_26_metal_shader/)
- [Ollama Blog: MLX on Apple Silicon](https://ollama.com/blog/mlx)
