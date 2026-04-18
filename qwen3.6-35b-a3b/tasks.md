# 動作確認・評価タスク

モデルの性能を体感するための4段階タスク。上から順に実施する。

---

## Task 1: モデルをダウンロード

```bash
ollama pull qwen3.6:35b-a3b
```

完了確認:

```bash
ollama list
# NAME                    ID            SIZE    MODIFIED
# qwen3.6:35b-a3b         xxxxxxxxxxxx  24 GB   ...
```

---

## Task 2: 日本語チャットで基本動作確認

応答速度と日本語品質の基本確認。

```bash
ollama run qwen3.6:35b-a3b
```

**プロンプト例:**

```
量子コンピュータをわかりやすく説明して
```

**確認ポイント:**
- [ ] 日本語で正しく応答するか
- [ ] 応答速度は許容範囲か（目安: 10〜20 tokens/sec on Apple Silicon）
- [ ] 内容に誤りや幻覚がないか

---

## Task 3: コード生成テスト

SWE-Bench 73.4% の実力を体感する。中程度の複雑さのコーディングタスク。

```bash
ollama run qwen3.6:35b-a3b
```

**プロンプト:**

```
Markdownファイルをパースしてシンタックスハイライト付きのHTMLに変換する
CLIツールをPythonで書いて。要件は以下:
- 引数: 入力ファイルパス（必須）、出力ファイルパス（省略時は stdout）
- コードブロックにシンタックスハイライトを適用
- スタンドアロンなHTMLとして出力（CSS込み）
- 標準ライブラリ + markdown, pygments のみ使用
```

**確認ポイント:**
- [ ] 動作するコードが出力されるか
- [ ] 要件を漏れなく満たしているか
- [ ] コードの品質・可読性

実際に動かす:

```bash
pip install markdown pygments
python <出力されたコード>.py input.md -o output.html
```

---

## Task 4: Thinking モードで推論テスト

`/think` オプションで思考プロセスを可視化し、推論能力と Thinking Preservation を確認する。

```bash
ollama run qwen3.6:35b-a3b
```

**Step 1 — Thinking モードを有効化:**

```
/think
```

**Step 2 — 推論問題を投げる:**

```
n人が参加する総当たり戦の試合数を求める式を導出して。
その後、32人で総当たり戦をする場合の試合数を答えて。
```

**Step 3 — 同じセッションで追加質問（Thinking Preservation の確認）:**

```
同じ考え方で、64チームのダブルイリミネーション方式では
最低何試合・最大何試合になるか計算して
```

**確認ポイント:**
- [ ] `<think>...</think>` ブロックに思考過程が表示されるか
- [ ] 導出過程が論理的に正しいか
- [ ] 前の推論コンテキストを踏まえて追加質問に答えられるか（Thinking Preservation）

Thinking モードを切る:

```
/nothink
```

---

## 結果メモ欄

| タスク | 結果 | メモ |
|--------|------|------|
| Task 1: pull | - | |
| Task 2: 日本語 | - | |
| Task 3: コード生成 | - | |
| Task 4: 推論 | - | |
