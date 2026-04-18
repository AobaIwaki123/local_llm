# LLMモデルプロバイダー まとめ

**調査日**: 2026-04-18  
**情報取得**: Tavily MCP（Web検索）による最新情報

---

## 米国系 主要プロバイダー

### OpenAI
- **最新モデル**: GPT-5 シリーズ（GPT-5, 5.1, 5.2, 5.4, 5 mini/nano/pro）、o3-mini（推論特化）
- **オープンウェイト**: GPT-OSS 120B / 20B（Apache 2.0系）
- **特徴**: 市場シェア最大（AI Labパワー指数 51.8%）。幅広いエコシステムとサードパーティ統合。
- **価格帯**: GPT-5.2 は $1.5/$14 per 1M tokens。GPT-OSS-120B は $0.12/1M と格安。

### Anthropic
- **最新モデル**: Claude Opus 4.6, Claude Sonnet 4.6, Claude Haiku 4.5
- **特徴**: コーディング・推論ベンチマークでトップクラス（SWE-bench Verified 80.8%）。200K コンテキスト。
- **価格**: Opus 4.6 は $5/$25、Sonnet 4.6 は $3/$15 per 1M tokens。

### Google
- **最新モデル**: Gemini 3.1 Pro（最新）、Gemini 2.5 Pro/Flash
- **特徴**: Gemini 3.1 Pro は GPQA Diamond 94.3%・AIME 100% と推論で現在最高クラス。10M トークンコンテキスト（Gemini 3 Pro）。Gemini のトラフィックシェアが急増（13.9% → 23.9%、2025年10月〜2026年2月）。
- **価格**: Gemini 2.5 Pro $1.25/$10、Gemini 3 Pro $2/$12 per 1M tokens。

### xAI（Elon Musk）
- **最新モデル**: Grok 4、Grok 4.20、Grok 4.20 Beta
- **特徴**: 2M トークンコンテキスト、フロンティアレベルの性能。ただしエコシステム・ドキュメントは他社より未成熟。
- **価格**: Grok 4 は $3/$15 per 1M tokens。

### Meta
- **最新モデル**: Llama 4 Scout / Maverick（2025年4月リリース）、Behemoth（準備中）
- **特徴**: 完全オープンウェイト。全モデルが MoE（Mixture of Experts）アーキテクチャ。10M トークンコンテキスト。API費用はホスティング先次第（AWS Bedrock、Azure、Groq 等）。
- **価格**: Llama 4 Scout $0.11/$0.34、Maverick $0.2/$0.6 per 1M tokens（サードパーティ経由）。

---

## 欧州系

### Mistral AI（フランス）
- **最新モデル**: Mistral Large 3（2512）、Mistral Small 4、Mistral Medium 3.1、Magistral（推論特化）
- **特徴**: EUサーバーホスティングで GDPR 対応が強み。OpenAI 互換 API。効率的な小型モデルが充実。
- **価格**: Mistral Small 3 $0.05/$0.08 と非常に安価。Large は $2/$6 per 1M tokens。

---

## 中国系 主要プロバイダー

### Alibaba / Qwen チーム
- **最新モデル**: Qwen3 シリーズ（0.6B〜235B MoE）、Qwen3.5、Qwen3.6-Plus（プロプライエタリ）、Qwen3.5-Omni（マルチモーダル）
- **特徴**: ハイブリッド思考モード（CoT のオン/オフをリクエスト単位で切替）。1M トークンコンテキスト。CJK（中日韓）テキストに強い。小型モデルまでオープンウェイトで公開。
- **価格**: Qwen3-235B $0.24/$0.48（API）。非常にコスト効率が高い。

### DeepSeek（杭州）
- **最新モデル**: DeepSeek R1（推論特化）、DeepSeek V3.2（685B MoE）
- **特徴**: 2025年初頭に世界を驚かせたコスパ最強モデル。MIT ライセンスでオープンウェイト。GPT-4o 比で入力 18倍・出力 36倍安い。
- **価格**: R1 $0.55/$2.19、V3.2 $0.14/$0.28 per 1M tokens。
- **備考**: 次世代モデル準備中（Huawei チップ使用の可能性あり）。

### Zhipu AI / Z.AI（GLM シリーズ）
- **最新モデル**: GLM-5、GLM-5.1（2026年3月リリース）
- **特徴**: Huawei チップで学習した完全国産モデル。SWE-bench 77.8% で Claude Opus 4.6（80.8%）の 94.6% に到達。オープンウェイト。2026年1月に香港 IPO（評価額 $31.3B）。

### Moonshot AI（Kimi）
- **最新モデル**: Kimi K2、Kimi K2.5、Kimi K2 Thinking
- **特徴**: 1T MoE アーキテクチャ。最大 100 エージェントの Agent Swarm 対応。HumanEval 99.0% とコーディング性能が高い。256K コンテキスト。

### MiniMax
- **最新モデル**: MiniMax M2.5、M2.7（2026年3月リリース）
- **特徴**: 1M トークンコンテキストが最大の特徴。自己進化エージェント機能。SWE-bench 78%。

### ByteDance（Doubao）
- **最新モデル**: Doubao 2.0（2026年2月リリース）、Seedance 2.0（音声動画生成）
- **特徴**: ByteDance の内製モデル。中国国内での普及が中心。

### StepFun
- **最新モデル**: Step-3.5-Flash（196B、11B active MoE）
- **特徴**: 超高速推論（100〜350 tok/s）。AIME 99.8%、SWE-bench 74.4%。256K コンテキスト。オープンウェイト。

---

## ベンチマーク比較（主要モデル）

| モデル | GPQA Diamond | AIME 2025 | SWE-bench Verified |
|---|---|---|---|
| Gemini 3.1 Pro | 94.3% | 100% | 80.6% |
| GPT-5.2 | 92.4% | 100% | 80.0% |
| Claude Opus 4.6 | 91.3% | 99.8% | 80.8% |
| Qwen 3.5 | 88.4% | — | 76.4% |
| GLM-5 | 86.0% | 92.7% | — |
| DeepSeek V3.2 | — | 89.3% | 73.1% |
| Gemini 2.5 Pro | 84.0% | 86.7% | 63.8% |
| Llama 4 Maverick | 84.0% | 83.0% | — |

---

## API 価格比較（input / output、per 1M tokens）

| プロバイダー | モデル | Input | Output |
|---|---|---|---|
| OpenAI | GPT-5.2 | $1.50 | $14.00 |
| Anthropic | Claude Opus 4.6 | $5.00 | $25.00 |
| Anthropic | Claude Sonnet 4.6 | $3.00 | $15.00 |
| Google | Gemini 2.5 Pro | $1.25 | $10.00 |
| xAI | Grok 4 | $3.00 | $15.00 |
| Meta | Llama 4 Maverick | $0.20 | $0.60 |
| Mistral | Mistral Large 3 | $0.50 | $1.50 |
| Alibaba | Qwen3-235B | $0.24 | $0.48 |
| DeepSeek | DeepSeek V3.2 | $0.14 | $0.28 |
| DeepSeek | DeepSeek R1 | $0.55 | $2.19 |

---

## 市場トレンドまとめ

| トレンド | 内容 |
|---|---|
| **MoE 主流化** | Llama 4・DeepSeek V3・Qwen3・Step 等、大型モデルはほぼ MoE が標準に |
| **推論モード（Thinking）** | CoT をオン/オフできるハイブリッド設計が増加（Qwen3、o3 等） |
| **長コンテキスト競争** | 1M〜10M トークンが当たり前に |
| **中国勢の台頭** | DeepSeek・Qwen・GLM がフロンティアモデルに肉薄 |
| **価格の急落** | DeepSeek が市場を揺さぶり、各社が追従 |
| **エージェント AI** | SWE-bench（自律コーディング）が主要ベンチマークに |
| **マーケットシェア変動** | ChatGPT が 72.5% → 60.5% に低下、Gemini が急伸 |

---

## 情報ソース

- Tavily MCP Web 検索（2026-04-18 実施）
- computeprices.com、margindash.com、iternal.ai LLM Leaderboard
- GitHub QwenLM/Qwen3.6、Alibaba Cloud Model Studio
- WaveSpeedAI、TrendForce、AIMultiple
