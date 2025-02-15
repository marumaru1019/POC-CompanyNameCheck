---

# 会社名表記違いの検証結果

---

## やったこと

1. **スペースの有無**:
   - 例: 九州電子 株式会社 vs 九州電子株式会社
   - スペースの有無に関わらず、同一会社として認識できるかを検証しました。

2. **半角全角の違い**:
   - 例: 合同会社All Right vs 合同会社Ａｌｌ　Ｒｉｇｈｔ
   - 半角と全角文字の違いに関わらず、同一会社として認識できるかを検証しました。

3. **カナ/英字の違い**:
   - 例: 大和ハウス工業 株式会社 vs DAIWA HOUSE INDUSTRY CO., LTD.
   - カタカナと英字表記の違いに関わらず、同一会社として認識できるかを検証しました。

4. **株式会社の有無**:
   - 例: T&Dリスクソリューションズ株式会社 vs T&Dリスクソリューションズ
   - 「株式会社」の有無に関わらず、同一会社として認識できるかを検証しました。

5. **略称**:
   - 例: 株式会社福島中央ﾃﾚﾋﾞ vs fct.jp
   - 略称やドメイン名に関わらず、同一会社として認識できるかを検証しました。

6. **その他**:
   - 例: 株式会社M-TEC vs 株式会社MｰTEC本社･工場
   - 表記の細かな違いや追加情報が含まれている場合でも、同一会社として認識できるかを検証しました。

---

## アプローチ

### 考えられるアプローチ
1. **ルールベースのマッチング**: ★
   - 表記ゆれを正規表現で対応付ける。
   - 例えば、「株式会社」「CO., LTD.」「Corporation」「Ltd.」などのパターンを正規化して比較する。
   - メリット: 高速で、特定のパターンに強い。
   - デメリット: ルールのメンテナンスが必要で、新しいパターンへの対応が遅れる可能性がある。

2. **辞書ベースの正規化**:
   - 対応表を辞書として持ち、会社名を正規化する。
   - 例えば、「ソニー」と「Sony」などの対応を辞書に登録し、比較時に正規化して一致を確認する。
   - メリット: ルールベースより柔軟で、新しいパターンにも対応しやすい。
   - デメリット: 準備コストが高くなる可能性がある。また、管理が大変。

3. **自然言語処理 (NLP) モデルの利用**:
   - 事前学習されたNLPモデルを使用して、表記ゆれを解消する。
   - 例えば、BERTやword2vecなどのモデルを使用して、意味的な類似性に基づいて一致を判断する。
   - メリット: デメリット性と精度。
   - デメリット: 訓練データと計算リソースが必要。また、後述する生成AIベースのものに比べて精度が低くなりやすい。

4. **音声認識ベースのマッチング**:
   - 会社名の音声表現を基に一致を判断する。
   - 例えば、カナと英字の音声的な類似性を計算し、一致を確認する。
   - メリット: 特に日本語と英語の音声表現の違いを解消しやすい。
   - デメリット: 計算コストがかかるのと精度が安定しない可能性がある。

5. **画像認識ベースのマッチング**:
   - 会社名を画像としてとらえて表記ゆれを解消する。
   - 例えば、半角全角などは画像ベースでとらえることで差異を解消する。
   - メリット: 事前の準備なしに汎用的に使用できる。
   - デメリット: 計算コストがかかるのと精度が安定しない可能性がある。
  
6. **GPTベースのマッチング**: ★
   - 生成AIに会社名のペアを渡して、同じ会社かどうかチェックさせる。
   - 例えば、GPT-4o を用いて、会社名を比較させるようなプロンプトを作成し、一致度を0-5点で出す。
   - メリット: プロンプトの準備のみで事前に必要な処理やデータがない。
   - デメリット: 多少計算コストがかかるのと精度が安定しない可能性がある。

### ルールベース

- 事前に定義されたルールに基づき、スペースの削除、全角半角の統一、特定のキーワード（例：「株式会社」）の処理を行いました。
- 決まったパターンに対して高い精度で同一会社の判定が可能です。

### GPTベース

- OpenAIのGPTモデルを使用して、ルールベースで処理しきれない複雑な表記違いにも対応しました。
- より汎用的で柔軟なアプローチが可能です。

---

## 検証結果

#### ルールベースの検証結果

- 特定のパターンに対しては高い精度で同一会社を認識。
- 略称やカナ/英字の違いには対応が難しい。

#### GPTベースの検証結果

- 複雑な表記違いにも高い精度で対応可能。
- ただし、一部のケースで誤判定が発生することがある。
  - 同一語句が入っているけど異なる会社 (AWS Japan vs 株式会社AWS、株式会社Ｓｉｌｖｅｒ　Ｂｌａｃｋs vs 株式会社Silver Black、株式会社富士通 vs Fujitsu Japan Limited)
  - 過去と現在で会社名が異なるケース (有限会社イー・マーキュリー vs 株式会社ミクシィ)

### ルールベースとGPTベースの比較

- ルールベースは特定のパターンに対しては迅速かつ高精度。
- GPTベースは多様な表記違いに対応できるが、処理時間とコストがかかる。
- 両者を組み合わせることで、より高い精度と効率を実現可能。

---

## 注意点

### GPTベースのアプローチ

- **汎用性**: 多様な表記違いに対応可能。
- **コスト**: 処理時間とAPIコストがかかる。
- **制限**: レートリミットの管理が必要。

---

## 結論

- **ルールベースのアプローチ**: 確立されたパターンに対しては迅速かつ高精度で有効。
- **GPTベースのアプローチ**: より柔軟で多様な表記違いに対応可能。
- **ハイブリッドアプローチの推奨**: ルールベースとGPTベースの組み合わせにより、精度と効率を最大化。

---