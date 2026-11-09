# 第3回 解答

## 演習

- 第1回（素のLLM）とエージェントの回答の違い：
- `--trace` で見えたツール呼び出し（ツール名と引数）：
- 1つの質問での LLM 呼び出し回数（`requests`）：
- 追加したツールと、docstring に書いた説明：

## 宿題

### 取り組んだレベル
基礎

### やったこと
- `app/agent.py` の `INSTRUCTIONS` から、「- 用語や講座に固有のことを答える前に、必ず search_glossary で用語集を確認する。」という強制指示行を削除した。
- 変更前と変更後で、同一のプロンプト（`"LoRA とQLoRAの違いは？"`）を `--trace` を付けて実行し、ツールの呼び出し挙動（tool_calls）や回答の差分を比較した。

### 結果（--trace の出力や比較）
- **変更前の trace**:
  - `[tool call] search_glossary({'query': 'LoRA'})`
  - `[tool call] search_glossary({'query': 'QLoRA'})`
  - `requests=2, tool_calls=2`
  - `tokens: input=1196, output=199`
- **変更後の trace**:
  - `[tool call] search_glossary({'query': 'LoRA'})`
  - `[tool call] search_glossary({'query': 'QLoRA'})`
  - `requests=2, tool_calls=2`
  - `tokens: input=1148, output=212`

### 気づき・疑問
- 「必ず search_glossary で用語集を確認する」という直接的な強制指示行を削除しても、モデル（gemini-3.5-flash-lite）は引き続き自発的に `search_glossary` ツールを2回呼び出した。
- これは、INSTRUCTIONS に残されていた「用語集に見つからないことは推測で断定しない」という指示や、ツール自体の説明文（docstring）・シグネチャを LLM が適切に理解し、用語の定義を正確に答えるためにはツール利用が不可欠であると自律的に判断したためと考えられる。
- トークン数を見ると、システムプロンプトの行を削った分、入力トークンが 1196 から 1148 へと微減していることが確認できた。