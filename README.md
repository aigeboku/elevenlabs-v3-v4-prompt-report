# ElevenLabs v3/v4 プロンプト＆機能レポート（2026-09-29）

> 原文：[Googleドキュメント](https://docs.google.com/document/d/1zlMvRaGQhX32m5xSEeXXG1EHhxjl0D5ND94bmhN-ZP4/edit?tab=t.0)  
> 2026-09-29に取得したGoogleドキュメントの公開版（原文時点の情報）です。原文の内容を保ち、GitHubで読める形式に整えています。

**目次**

- [0. 冒頭の要約](#section-0)
- [1. v4 の特徴と変更点](#section-1)
- [2. 音声タグ一覧（v3／v4 共通。v4 で追加・強化されたものは注記）](#section-2)
- [3. プロンプトの書き方とコツ](#section-3)
- [4. v3 から v4 への移行の注意](#section-4)
- [5. ユースケース別テンプレ](#section-5)
- [6. 料金と API](#section-6)
- [7. 評判（公開から約1日時点）](#section-7)
- [8. 未確認のまま残った項目](#section-8)
- [9. 出所一覧（すべて 2026-09-29 に開いて確認）](#section-9)

作成日：2026-09-29（JST）／対象：Eleven v3・Eleven v4・Eleven v4 Turbo

表記ルール：数字・仕様には出所URLを付けています（URLは［S番号］で参照し、末尾の出所一覧に載せています）。確認できなかったものは「未確認」と書いています。テンプレ集では<strong>【出典あり】</strong>と<strong>【自作・未検証】</strong>を分けています。

<a id="section-0"></a>

## 0. 冒頭の要約

- <strong>Eleven v4（eleven\_v4）と Eleven v4 Turbo（eleven\_v4\_turbo）</strong>は、2026-09-28（米国時間。日本時間では9/28 23:00ごろ）に公開されました。ElevenCreative・ElevenAgents・API のすべてで使え、Free を含む全プランが対象です［S1］［S3］［S7］。

- <strong>v3 の音声タグ（\[whispers\] \[laughs\] \[sighs\] など）は v4 でも同じ書き方で使えます</strong>。公式ヘルプに「The same tags work with both models」と書かれています［S5］。公式ガイドは「v4 は v3 のほぼ上位互換で、切り替えを強く推奨」としたうえで、v4 向けのプロンプト技法は v3 にも当てはまると明記しています［S4］。

- <strong>v4 で新たに効くようになった・強化された指示</strong>は次のとおりです。

    - 自然文による演技指示（例：\[said angrily in French accent\] \[Low, steady voice, restrained urgency\]）［S1］［S4］

    - タグを重ねたときに、並べた順序どおりに従う［S7］

    - 効果音タグの追従性の向上［S7］

    - /ˌbaɪoʊˈkemɪstri/ のようにスラッシュで囲む IPA 発音指定［S4］

    - 生成し直しても話者（声）がぶれない［S7］

- <strong>使えない／変わった点</strong>

    - SSML（&lt;break&gt; を含む）は非対応。v3 でも非対応でした［S4］［S6］

    - 設定は Stability と Similarity の2つだけで、Style／Speed のスライダーはありません［S6］

    - v4 公開前に作った IVC／PVC は v4 用に再学習が必要です［S7］

    - 元の声と違う言語で話させると、元のアクセントは引き継がれずネイティブ発音になります（切り替え不可）［S6］

    - Voice Design で作った声は、旧モデルより演技力が落ちることがあります［S6］

- <strong>料金</strong>

    - API 定価は v4 が \$0.08/1K文字、Turbo が \$0.04/1K文字。10/12までそれぞれ \$0.022／\$0.011 に割引中です［S8］

    - Web でのクレジット消費は「他のTTSモデルと同じ」とされています［S7］

    - Creator 以上のプランは、2週間だけ、v4 の利用分が月間TTSクレジットの最大2倍まで残高から引かれません（Web・モバイルアプリのみ）［S9］

- <strong>日本語</strong>

    - TechCrunch によると、品質の伸びが最も大きかった言語は日本語・ブラジルポルトガル語・北京語・広東語です［S10］

    - ElevenLabs Japan の担当者は「三点リーダーなどの無音表現も含めて読み上げ精度が向上した」と投稿しています［S30］

    - 個人の検証では「Gemini 3.8 TTS とほぼ互角、価格は Gemini が大幅に安い」という評価があります［S31］

<a id="section-1"></a>

## 1. v4 の特徴と変更点

### 1-1. 基本情報

| 項目 | 内容 | 出所 |
| --- | --- | --- |
| モデルID | eleven\_v4（品質重視）／eleven\_v4\_turbo（低遅延） | S3 |
| 発表日 | 2026-09-28（ブログの公開日）。TechCrunch の記事時刻は 7:00 AM PDT（=9/28 23:00 JST） | S1, S10 |
| 公開状態 | 「available now」。アルファ／ベータの表記はありません。ただし「継続的に開発中で、今後挙動が変わる可能性がある」と明記されています | S1, S6 |
| 対応言語 | 90+（日本語を含む）。v3 は 70+、Multilingual v2 は 29、Flash v2.5 は 32 | S3 |
| 1回の文字数上限 | v4 は 10,000文字（約10分）、v3 は 5,000、Multilingual v2 は 10,000、Flash v2.5 は 40,000。v4 Turbo の上限は表に記載がなく<strong>未確認</strong> | S3, S7 |
| Text to Dialogue（複数話者API） | v4 と v3 で利用できます。1リクエストの inputs\[\].text の合計は2,000文字以下が推奨。話者数の上限はなし | S11 |
| 遅延 | Turbo は推論の遅延が中央値 約100ms（アプリやネットワークの遅延を除く）、話し始めるまでの時間は中央値 約150ms（WebSocket）。<strong>通常の v4 の遅延は未確認</strong>（Artificial Analysis の計測では生成速度が毎秒73.4文字） | S1, S3, S12 |
| 設定項目 | Stability と Similarity の2つ。Style／Speed はなし。SSML は非対応 | S6 |
| 出力形式 | MP3、WAV/PCM、μ-law（電話向け）など。44.1kHz の PCM を API で出すには Pro 以上が必要 | S7, S9 |
| 第三者の評価 | Artificial Analysis の Provider Voice アリーナで1位（Elo 1,319）。同じ声で比べる Controlled Voice では2位（1,157）。発音の正確さ（Pronunciation Robustness）は91.7% | S12 |

### 1-2. v3 から変わった点・新機能

- <strong>新しいアーキテクチャ</strong>：誰が話しているか、直前に何があったか、その行をどう聞かせるかを読み取って演じる設計です［S7］。

- <strong>タグの追従性</strong>：「v3 より確実にタグの並び順どおりに従う（効果音も含む）」とされています［S7］。自然文で「どう読むか」を書いても効きます［S1］［S13］。

- <strong>IPA 発音</strong>："/IPA/" のようにスラッシュで囲んで書きます。旧モデルのような XML の phoneme タグは不要です［S4］。

- <strong>長い作品の一貫性</strong>：context stitching（生成をまたいでも間や話し方をそろえる仕組み）により、長い作品でもペースや話し方が安定します。何度生成し直しても同じ話者の声のままです［S7］。ブログでは request stitching（生成をつなげて長尺にする機能）にも触れています［S1］。

- <strong>音声クローン</strong>：

    - PVC（Professional Voice Clone）に再対応しました。v3 では非対応でした［S7］

    - 「v4 の IVC（Instant Voice Clone）は Multilingual v2 の PVC を上回る」とされています［S7］

    - IVC の必要素材は、ブログでは「10秒」［S1］、ドキュメントの FAQ では「一般に1〜2分」［S6］と記述が食い違っています

- <strong>既存のボイス</strong>：ボイスライブラリの17,500以上の声はそのまま v4 で使えます。ただし v4 公開前に作った IVC／PVC は再学習が必要です（ボイスにカーソルを合わせて＋ボタンを押す）［S7］。

- <strong>アクセントの扱い</strong>：声の元の言語と同じ言語なら、アクセントはそのまま保たれます。違う言語で話させると、その言語のネイティブ発音になります。オン／オフの切り替えはできず、トグル化は研究段階で時期は未定とされています［S6］。

- <strong>素材の質が出やすい</strong>：元の声に忠実な分、学習素材の録音品質が結果に強く出ます。単一の話し方でそろえた、質のよい素材が推奨されています［S6］。

- <strong>Turbo</strong>：双方向ストリーミングに対応し、LLM が文章を生成している途中から音声を返せます。PVC は v4 と Turbo で同じように動くとされています［S7］。Turbo は Text to Dialogue WebSocket で使えます［S3］。

<a id="section-2"></a>

## 2. 音声タグ一覧（v3／v4 共通。v4 で追加・強化されたものは注記）

前提：タグは半角の角括弧 \[ \] で囲み、変化させたい位置に置きます。決められた選択肢ではなく自然文の指示なので、下の一覧は「例」です（網羅ではありません）［S11］［S4］。効くかどうかは声によって変わり、声の学習データにある話し方ほど効きやすくなります［S4］。

| 分類 | タグ例 | 出所 | メモ |
| --- | --- | --- | --- |
| 感情（公式ガイド） | \[sarcastic\] \[curious\] \[excited\] \[crying\] \[snorts\] \[mischievously\] | S4 | v4 のプロンプトガイドの「Voice-related」に載っているもの |
| 感情（Enhance 機能のプロンプト） | \[happy\] \[sad\] \[excited\] \[angry\] \[whisper\] \[annoyed\] \[appalled\] \[thoughtful\] \[surprised\] | S4 | UI の「Enhance」ボタン（LLM がタグを自動で付ける機能）が内部で使っているプロンプトの一覧 |
| 感情（公式ブログ） | \[sorrowful\] \[happily\] \[worried\] \[nervous\] \[frustrated\] \[tired\] \[calm\] \[awe\] \[booming\] \[big laugh\] | S14, S15, S16 |  |
| 接客・説明のトーン | \[professional\] \[sympathetic\] \[questioning\] \[reassuring\] | S4 | 公式のカスタマーサポート例 |
| トーン・スタイル | \[cheerfully\] \[flatly\] \[deadpan\] \[playfully\] \[dismissive\] \[cheeky\] \[cute\] \[resigned tone\] \[dramatic\] \[matter-of-fact\] \[whiny\] \[casual\] \[measured\] \[muttering\] | S4, S14, S16, S17, S6 |  |
| 声量・話し方 | \[whispers\]/\[whispering\] \[shouts\]/\[shouting\] \[softly\] \[quietly\] \[loudly\] \[under his breath\] | S4, S14, S15, S6 | 公式ガイドは「瞑想的な声は叫ぶべきではないし、ハイテンションな声のささやきは説得力に欠ける」としています［S4］。一方で v4 は、学習データにない \[whispering\] \[shouting\] にもある程度従います［S4］ |
| 間・テンポ | \[pause\] \[short pause\] \[long pause\] \[breathes\] \[continues after a beat\] \[rushed\] \[slows down\] \[slowly\] \[drawn out\] \[deliberate\] \[rapid-fire\] | S18, S14, S4, S7 | v4 の FAQ でも、間は \[pause\]／\[long pause\] で入れるよう案内されています［S7］ |
| ためらい・強調 | \[stammers\] \[hesitates\] \[repeats\] \[timidly\] \[emphasized\] \[stress on next word\] \[understated\] | S18, S16 |  |
| 人間的なリアクション | \[laughs\] \[laughs harder\] \[starts laughing\] \[wheezing\] \[chuckles\] \[giggles\] \[sighs\] \[exhales\] \[exhales sharply\] \[inhales deeply\] \[clears throat\] \[gasps\] \[gulps\] \[swallows\] \[coughing\] \[happy gasp\] \[frustrated sigh\] \[laughing hysterically\] \[with genuine belly laugh\] \[soft chuckle\] \[nervous laugh\] | S4, S14, S16, S6, S7 | いちばん無難で、どんな用途にも使いやすいタグ群 |
| アクセント・キャラクター | \[strong X accent\]（X に言語名などを入れる） \[French accent\] \[British accent\] \[Australian accent\] \[Southern US accent\] \[pirate voice\] \[evil scientist voice\] \[childlike tone\] \[robotic voice\] | S4, S14, S17 | 公式でも「experimental（実験的）」扱いで、声によって安定しないことがあります［S4］ |
| ジャンル・全体の方向づけ | \[fantasy narrator\] \[sci-fi AI voice\] \[classic film noir\] \[football\] \[wrestling match\] \[auctioneer\] | S17, S11 |  |
| 効果音・環境音 | \[gunshot\] \[applause\] \[clapping\] \[explosion\] \[leaves rustling\] \[gentle footsteps\] \[binary beeping\] | S4, S11 |  |
| 効果音（v4 の公式例） | \[door slams\] \[light rain\] \[phone buzzing\] \[Gong sounds\] \[crowd applause\] | S1, S7 | v4 で追従性が向上［S7］。声の指示が効果音として解釈されることもあるので、声の指示は具体的に書くよう推奨されています［S4］ |
| 歌・特殊 | \[sings\]/\[singing\] \[singing quickly\] \[woo\] \[fart\] | S4 | 実験的 |
| 会話の流れ（多話者） | \[interrupting\] \[overlapping\] \[jumping in\] \[starting to speak\] \[beginning to speak\] \[cautiously\] \[stifling laughter\] \[cracking up\] | S4, S14 | ダッシュ（—）と組み合わせて割り込みを表現します |
| <strong>v4 の自然文による演技指示（公式例）</strong> | \[said angrily in French accent\]／\[Low, steady voice, restrained urgency\]／\[Quietly, with controlled fear\]／\[Voice rising into firm resolve\]／\[Gentle laugh, then sincere\]／\[Quiet, reflective narration\]／\[Building tension, measured pace\]／\[Gentle pause, then quiet realization\]／\[whispered in a British accent\]／\[building\]／\[low, gravelly voice\]／\[lower, thoughtful\] | S1, S4, S6, S7 | v4 のガイドの例は、この書き方が中心です |
| 使わない方がよいもの | \[standing\] \[grinning\] \[pacing\] \[music\] のような、音にならない動作や状態 | S4 | Enhance 機能のプロンプトが名指しで禁止しています。なお Enhance は「声以外（音楽・効果音）のタグは付けない」設定ですが、TTS 本体は効果音タグに対応しています［S4］［S7］ |
| 日本語で書いたタグ | \[ささやき声で\] \[ため息\] \[笑いながら\] | S20 | v3 で「日本語のタグでも動いた」という個人の実測があります。<strong>v4 での日本語タグの効果は未確認</strong>（v4 の日本語検証の投稿は英語タグを使っていました［S32］） |

### 2-1. 多話者の書き方

- <strong>Web（ElevenCreative）</strong>：Text to Speech で複数のスピーカーを追加すると Dialogue mode になり、行ごとに話者を選べます［S21］。利用者からは「台本を書いて行ごとに話す人を選ぶだけで、会話のかたまりごとに1回で生成できる」という報告があります［S33］。公式の v4 デモは「話者名 - 声の説明」＋各行のタグという形式です（例：John - host, theatrical ／ Sia - conversational, British）［S7］。

- <strong>プロンプトガイドの表記</strong>：Speaker 1: \[excitedly\] ... ／ Speaker 2: \[curiously\] ... と書き、話者ごとに別の声を割り当てます［S4］。

- <strong>API（Text to Dialogue）</strong>：inputs=\[{"text":"\[cheerfully\] Hello, how are you?","voice\_id":"..."},{"text":"\[stuttering\] I'm... I'm doing well, thank you.","voice\_id":"..."}\] のように、発話ごとに text と voice\_id を渡します［S22］。

<a id="section-3"></a>

## 3. プロンプトの書き方とコツ

### 3-1. 公式が挙げているテクニック

- <strong>句読点</strong>：省略記号（...）は間と重みを加え、大文字は強調になり、通常の句読点は自然なリズムを作ります（例："It was a VERY long day \[sigh\] … nobody listens anymore."）［S4］。

- <strong>改行</strong>：長めの間になります。\[pause\] や省略記号でも間を作れます［S14］。

- <strong>割り込み・言いよどみ</strong>：ダッシュで割り込み（"\[cautiously\] Hello, is this seat-" → "\[jumping in\] Free?"）、省略記号で言いかけを表現します［S11］。

- <strong>声選び</strong>：声の性格に合うタグを使います。瞑想系の声に叫ばせない、ハイテンションな声にささやかせない、が目安です。真面目な声には \[giggles\] や \[mischievously\] が効きにくいとされています。v4 は学習データにない話し方もある程度こなしますが、最適とは限りません［S4］。

- <strong>明確に書く</strong>：声の質を具体的に書きます（例：\[low, gravelly voice\]）。効果音と誤解されないようにするためです［S4］。

- <strong>タグの組み合わせ</strong>：複数重ねて使えます（例：\[whispering\]\[pause\] Did you hear that? \[rushed\] Hide! Now!［S18］、\[tired\] It's been a long day… \[upset\] How many more days can I take?［S14］）［S4］。

- <strong>タグの置き場所</strong>：変えたい部分の直前か直後に置きます（例：\[annoyed\] This is hard. ／ This is hard. \[sighs\]、\[appalled\] Are you serious? \[sighs\] I can't believe you did that!）［S4］。

- <strong>文の構造</strong>：v4 では文章の組み立てが出力に強く影響します。自然な話し言葉、正しい句読点、はっきりした感情の文脈で書くのがよいとされています［S4］。

- <strong>タグが読み上げられてしまうとき</strong>：声と求める演技が合っていないのが主な原因とされています［S14］。

- <strong>地の文で感情を伝える</strong>："You're leaving?" she asked, her voice trembling with sadness. のような書き方でも効きますが、地の文そのものも読み上げられるので、後で編集で削る必要があります［S4］。

- <strong>ばらつき対策</strong>：出力は毎回変わります。seed を指定すると再現性が上がります（完全ではありません）［S11］［S23］。ダッシュボードでは同じ内容なら2回まで無料で生成し直せます［S11］。

- <strong>長文の分割</strong>：API の previous\_text／next\_text／previous\_request\_ids（最大3件）で、分割した生成どうしの流れをつなげられます［S23］。

- <strong>読み方の正規化</strong>：電話番号・通貨・日付・URL・略語は誤読しやすいので、LLM で読み方に展開してから渡します（公式に展開用プロンプトの例あり）［S4］。API には apply\_text\_normalization（auto／on／off）もあります［S23］。

### 3-2. Stability の使い分け

<table>
<tr>
<th><p>モデル</p></th>
<th><p>内容</p></th>
<th><p>出所</p></th>
</tr>
<tr>
<td><p>v3</p></td>
<td><p>スライダーは Creative／Natural／Robust の3段階です。</p><ul><li>Creative：感情豊かだが、指示にない演技が出ることがある</li><li>Natural：元の声に最も近く、バランス型</li><li>Robust：安定する代わりにタグが効きにくく、v2 に近い挙動</li></ul><p>表現を出したいなら Natural／Creative、という使い分けです。API では Stability が 0.0／0.5／1.0 の3値しか受け付けず、0.65 のような中間値や speed／style／similarity_boost を送ると 422 エラーになったという実測報告があります。</p><p><strong>注意</strong>：この3段階の説明は、現在の公式ドキュメント本文では確認できませんでした（v4 のガイドに統合された際に消えたとみられます）。二次情報をもとにした内容です。</p></td>
<td><p>S19, S20</p></td>
</tr>
<tr>
<td><p>v4</p></td>
<td><p>Stability と Similarity の2つ。</p><ul><li>Stability：低いほど表現が多彩になり、高いほど一定に保たれる</li><li>Similarity：高いほど元の声に忠実になるが、自然さが落ちることがある</li></ul><p>API の stability は小数で、既定値は 0.5 です。<strong>v4 で連続値（任意の小数）を受け付けるかは未確認</strong>です。</p></td>
<td><p>S6, S23</p></td>
</tr>
</table>

### 3-3. 利用者のコツ（公式以外）

- タグは次のタグが来るまで効き続けます（例：\[whisper\] の後はずっとささやき）。感情は段階を踏んで変えると自然です（笑い→考え込む→悲しい）。強調したい語は「OH MY GOD」のように大文字にします［S19］（Moe Lueker のブログと YouTube 解説［S24］）。

- 話者ラベルにキャラクターの説明を添えると演じ分けやすい、としています［S19］。<br>※同じ記事の「v3 は 200〜10,000文字」という記述は、公式の v3 上限 5,000文字［S3］と食い違っています（古い情報の可能性があります）。

- <strong>日本語で量産するときの注意</strong>（YouTube 4チャンネル・196ファイルでの実測報告）［S20］

    - language\_code: ja を指定しないと、中国語のような読みが混ざる

    - 1行50〜100文字に分けて生成し、誤読した行だけ作り直す

    - アルファベットの略語（HTTP、GPU など）はほぼ誤読するので、先回りして置換辞書に登録する

    - 角括弧ではなく丸括弧で書くとタグが読み上げられる

    - タグは山場の1〜2割の行に絞る

    - Voice Design（テキストから声を作る機能）では、プロンプトの先頭に「東京アクセント」を入れる（たなか氏の解説動画からの引用とのこと。動画そのものは未確認）

- v4 での日本語の検証例：「市場」「最中」「20日」「人気のない」などの読み間違えやすい語20問と、感情タグ10個を入れた留守電の台本で「ほぼ完璧」という報告があります（台本はテンプレ章の【出典あり】に掲載）［S32］。

- ElevenLabs Japan の担当者によると、v4 は「特に日本語などアジア言語で、三点リーダーなどの無音表現も含めて」読み上げ精度が向上し、タグの複合表現・効果音の挿入・歌唱にも対応しています［S30］。

- 無料プランは商用利用できません。商用ライセンスは Starter（\$6）以上に付きます［S9］［S20］。

<a id="section-4"></a>

## 4. v3 から v4 への移行の注意

<table>
<tr>
<th><p>項目</p></th>
<th><p>v3</p></th>
<th><p>v4</p></th>
<th><p>出所</p></th>
</tr>
<tr>
<td><p>音声タグ</p></td>
<td><p>対応</p></td>
<td><p>同じタグがそのまま使えます。追従性が向上し、重ねたタグを順序どおりに実行します</p></td>
<td><p>S5, S7</p></td>
</tr>
<tr>
<td><p>自然文での演技指示</p></td>
<td><p>一部可能（[pirate voice] など）</p></td>
<td><p>公式の例の中心になっています（[Quietly, with controlled fear] など）</p></td>
<td><p>S17, S4</p></td>
</tr>
<tr>
<td><p>SSML の break</p></td>
<td><p>非対応</p></td>
<td><p>非対応。[pause]／[long pause]、省略記号、改行で代わりに間を作ります</p></td>
<td><p>S4, S7</p></td>
</tr>
<tr>
<td><p>発音指定</p></td>
<td><p>発音辞書（alias など）</p></td>
<td><p>スラッシュで囲む IPA 表記に対応。発音辞書も引き続き使えます。<strong>XML の phoneme タグが v4 で動くかは未確認</strong></p></td>
<td><p>S4, S7</p></td>
</tr>
<tr>
<td><p>設定</p></td>
<td><p>Stability の3段階（二次情報）</p></td>
<td><p>Stability と Similarity。Style／Speed はなし</p></td>
<td><p>S19, S6</p></td>
</tr>
<tr>
<td><p>文字数上限</p></td>
<td><p>5,000</p></td>
<td><p>10,000</p></td>
<td><p>S3</p></td>
</tr>
<tr>
<td><p>PVC</p></td>
<td><p>非対応</p></td>
<td><p>対応（再学習が必要）</p></td>
<td><p>S7</p></td>
</tr>
<tr>
<td><p>既存のクローン</p></td>
<td><p>—</p></td>
<td><p>v4 用に再学習が必要。v3 のときと声の印象が変わることがあります（元の声により忠実になるため）</p></td>
<td><p>S7, S6</p></td>
</tr>
<tr>
<td><p>別言語での話し方</p></td>
<td><p>元の声のアクセントが残りやすい</p></td>
<td><p>ネイティブ発音になる（切り替え不可）</p></td>
<td><p>S6</p></td>
</tr>
<tr>
<td><p>Voice Design の声</p></td>
<td><p>—</p></td>
<td><p>旧モデルより演技力が落ちることがあります</p></td>
<td><p>S6</p></td>
</tr>
<tr>
<td><p>効かなくなったもの</p></td>
<td colspan="2"><p>公式に明記されているのは SSML と Style／Speed スライダーがないことだけです。<strong>それ以外に v4 で効かなくなったタグは未確認</strong></p></td>
<td><p>S6</p></td>
</tr>
</table>

<strong>移行の手順（公式の推奨にもとづく）</strong>

- 同じ台本を v3 と v4 で生成して聴き比べる。公式も、自分の声と内容で試すよう勧めています［S4］

- 自分のクローンを v4 用に再学習する［S7］

- Stability／Similarity を調整する

- 声の指示が効果音として解釈される箇所は、タグを具体的な書き方に直す［S4］

- 固有名詞は IPA か発音辞書で固定する

<a id="section-5"></a>

## 5. ユースケース別テンプレ

【出典あり】は原文のまま引用したもの、【自作・未検証】は本レポートで作成し、生成確認をしていないものです。自作のタグの効き具合は、声や設定によって変わります。

### 5-1. 出典ありの例

<strong>【出典あり】クイズ番組風の多話者（公式 v4 ページ［S7］）</strong>

```text
John - host, theatrical
[warm] Welcome back to the final round. [long pause] Our returning champion needs one more answer. [building] For ten thousand pounds: who wrote the Moonlight Sonata? [Gong sounds] Five seconds.
Sia - conversational, British
[whispered in a British accent] Beethoven. It's Beethoven, isn't it? [uncertain] I'm fairly sure it's Beethoven. [nervous laugh] Final answer.
John - host, theatrical
[delighted] Beethoven is correct! [crowd applause] [amused] I thought we'd lost you there for a moment. [warm] Ten thousand pounds!
```

<strong>【出典あり】ボイスアクティング（公式ガイド v4［S4］）</strong>

```text
[Low, steady voice, restrained urgency] Keep the lantern covered. If they see the light, they will know we crossed the river.
[Brief pause]
[Quietly, with controlled fear] I heard them at the bridge. Not soldiers. Something else.
[Voice rising into firm resolve] Then we do not stop. We reach the tower before sunrise, or we do not reach it at all.
```

<strong>【出典あり】長いナレーション（公式ガイド v4［S4］より抜粋）</strong>

```text
[Quiet, reflective narration] She had promised herself she would not return. Yet there she was, standing before the gate with mud on her boots and the key cold against her palm.
[Building tension, measured pace] The lock turned easily. Too easily.
[Softly, with wonder] On the table in the entryway sat a single lantern, already lit.
[Gentle pause, then quiet realization] I knew you would come.
```

<strong>【出典あり】カスタマーサポート（公式ガイド［S4］）</strong>

```text
[professional] "Thank you for calling Tech Solutions. My name is Sarah, how can I help you today?"
[sympathetic] "Oh no, I'm really sorry to hear you're having trouble with your new device. That sounds frustrating."
[reassuring] "Alright, based on what you're describing, it sounds like a software glitch."
```

<strong>【出典あり】日本語・v4 での検証台本（X @ZetoGroovin［S32］より抜粋）</strong>

```text
[cheerfully] もしもし、お姉ちゃん？ 私。……って、留守電か。
[excited] 今日は3月1日、お姉ちゃんの誕生日だよね！ おめでとう！
[sighs] ……本当はさ、20日に会う約束だったでしょ。私、10:30にはもう着いてたんだよ。
[hesitant] 人気のない改札で、2時間半……ずっと待ってた。
[crying][whispers] 辛かったなぁ……。
[angry][shouting] っていうか！ LINEくらい返してよ！
[door slams] [sighs][calm] ……ごめん。大人気なかった。
[sings] ハッピーバースデー、お姉ちゃん〜♪
[softly] じゃあ、4日の午後7時にまたかけるね。……おやすみ。
```

<strong>【出典あり】Text to Dialogue の API（公式クイックスタート［S22］）</strong>

```python
audio = elevenlabs.text_to_dialogue.convert(
    inputs=[
        {"text": "[cheerfully] Hello, how are you?", "voice_id": "9BWtsMINqrJLrRacOk9x"},
        {"text": "[stuttering] I'm... I'm doing well, thank you.", "voice_id": "IKne3meq5aSn9XLyUdCD"},
    ]
)
```

### 5-2. 自作テンプレ（日本語／英語）

<strong>【自作・未検証】A. YouTube ナレーション（AI ニュース解説）</strong>

推奨：eleven\_v4、1回の生成は数百〜千文字程度に分ける、language\_code: ja、固有名詞は発音辞書に登録。

```text
[明るく、テンポよく] こんにちは！ 今日のAIニュースです。
[少し間をおいて、興味深そうに] ElevenLabsが、新しい音声モデル「Eleven v4」を公開しました。
[落ち着いた解説口調] 大きな変化は三つ。……一つ目は、感情表現。二つ目は、声のクローン精度。そして三つ目が、日本語の自然さです。
[pause]
[少し声を落として] ただし、料金には注意が必要です。
[明るく締める] 詳しくは概要欄にまとめています。それでは、また次の動画で！
```

```text
[upbeat, energetic host] Hey everyone, welcome back to the channel!
[curious] ElevenLabs just dropped a brand-new voice model called Eleven v4.
[clear, explanatory tone] Three things changed... emotion, cloning accuracy, and multilingual quality.
[pause]
[lower, serious] But before you switch, there's one thing you NEED to know about pricing.
[warm, wrapping up] Links are in the description. See you in the next one!
```

<strong>【自作・未検証】B. 二人対談（Dialogue mode／Text to Dialogue）</strong>

話者ごとに別の声を割り当てます。API の場合は1リクエストの合計を2,000文字以下に抑えます［S11］。

```text
ホスト（明るい解説役）: [excited] ねえ、Eleven v4もう試した？
ゲスト（落ち着いた専門家）: [calm] 昨日さっそく触りました。一番驚いたのは……[pause] 日本語の間の取り方ですね。
ホスト: [curious] 間？ 具体的には？
ゲスト: [thoughtful] 三点リーダーを入れると、ちゃんと「ため」が入るんです。
ホスト: [jumping in] —それ、ナレーションだとめちゃくちゃ大事なやつ！
ゲスト: [laughs] そうなんですよ。
```

```text
Speaker 1: [excited] So have you tried Eleven v4 yet?
Speaker 2: [calm, thoughtful] I spent the whole evening with it. Honestly, the pacing surprised me most...
Speaker 1: [curious] The pacing? How so?
Speaker 2: [measured] It actually pauses where a human would—
Speaker 1: [jumping in] —which is huge for narration!
Speaker 2: [laughs] Exactly.
```

<strong>【自作・未検証】C. 広告（15〜30秒）</strong>

```text
[ささやくように、期待を込めて] 朝、まだ誰も起きていない時間。
[light rain]
[温かく、ゆっくり] 一杯のコーヒーが、今日を変える。
[明るく、はっきり] 新発売「モーニングブレンド」。全国のコンビニで、今日から。
```

```text
[whispers, anticipation] Early morning. The world is still asleep.
[light rain]
[warm, slow] One cup can change your whole day.
[bright, confident announcer] Introducing Morning Blend. Available today, nationwide.
```

<strong>【自作・未検証】D. 朗読・オーディオブック</strong>

公式の長文ナレーション例［S4］の形式にならっています。v4 は1回10,000文字まで［S3］。

```text
[静かに、語りかけるように] その夜、村には雪が降っていた。
[少し間をおいて] 少女は、古い手紙を握りしめたまま、戸口に立っていた。
[緊張を高めながら、ゆっくり] 扉の向こうから……足音が、近づいてくる。
[息をのむ] 
[ささやき声で] 「……おかえり」
```

```text
[Quiet, intimate narration] That night, snow was falling over the village.
[Brief pause] The girl stood at the door, an old letter clenched in her hand.
[Building tension, slow] From beyond the door... footsteps. Coming closer.
[gasps]
[whispering] "...Welcome home."
```

<strong>【自作・未検証】E. 多言語版（同じ声で英語・スペイン語版を作る）</strong>

v4 は、声の言語と違う言語で話させるとネイティブ発音になります［S6］。字幕の尺に合わせるため、各言語の台本は短めに訳すのがおすすめです（自作の運用案）。language\_code で言語を指定します［S23］。

```text
[JA] [落ち着いた解説口調] 今日は、AI音声の最新モデルを紹介します。
[EN] [calm, explanatory tone] Today, we're looking at the latest AI voice model.
[ES] [tono calmado y explicativo] Hoy vamos a ver el modelo de voz con IA más reciente.
```

既存の動画を丸ごと吹き替えるなら、Dubbing v2（API \$2.20/分、92言語）も選択肢です［S8］。

<a id="section-6"></a>

## 6. 料金と API

| 項目 | 内容 | 出所 |
| --- | --- | --- |
| API 料金（1K文字あたり） | v4：\$0.08（10/12まで \$0.022）／v4 Turbo：\$0.04（同 \$0.011）／v3：\$0.08／v3 Conversational：\$0.04／Multilingual v2：\$0.08／Flash・Turbo：\$0.04。割引終了の時間帯（タイムゾーン）は<strong>未確認</strong> | S8 |
| Web のクレジット | v4 は「他の TTS モデルと同じクレジット価格」。v3 は Web で1文字1クレジット | S7, S25 |
| キャンペーン | Creator 以上のプランでは、2週間、v4 の利用分が月間TTSクレジットの最大2倍まで残高から引かれません（Web・モバイルアプリのみ） | S9 |
| プラン | Free \$0（1万クレジット、非商用）／Starter \$6（3万、商用ライセンス、IVC）／Creator \$22（初月\$11、12.1万、PVC）／Pro \$99（60万、API で 44.1kHz PCM）／Scale \$299（180万）／Business \$990（600万） | S9 |
| 主なエンドポイント | Create speech／Stream speech（model\_id: eleven\_v4）、Create dialogue／Stream dialogue、Text to Dialogue WebSocket（Turbo で使用）。REST と TypeScript／Python の SDK があります | S5, S3, S7 |
| 主な API パラメータ | language\_code、seed（0〜4294967295。同じ値でも結果の一致は保証されない）、previous\_text／next\_text、previous\_request\_ids／next\_request\_ids（各最大3件）、apply\_text\_normalization（auto／on／off）、voice\_settings.stability（既定 0.5） | S23 |
| 同時実行数 | プランごとの共通の上限があります。Text to Dialogue WebSocket は別枠のセッション数で、Free 14／Starter 21／Creator 35／Pro 70／Scale 105／Business 105 です。<strong>v4 専用の上限は未確認</strong> | S3 |
| 外部の提供先 | fal でも提供されています | S26 |

<a id="section-7"></a>

## 7. 評判（公開から約1日時点）

- <strong>良い点</strong>

    - 日本語に強く、ピッチアクセントの誤りが少ない［S27］

    - 自分の声のクローンの精度も、日本語の自然さも「完璧」という声［S28］

    - 同じ行を十数回生成し直しても同じ話者の声のまま［S29］

    - Artificial Analysis で1位［S12］

- <strong>不満・懸念</strong>

    - 価格：Artificial Analysis によると、Gemini 3.8 Flash TTS は約\$16.49/1M文字、v4 の定価は\$80/1M文字［S12］。日本語での比較では「品質はほぼ互角で、メインは Gemini かな」という声［S31］

    - Turbo は音質がやや落ちる。長いナレーションなら通常の v4 がおすすめ、という投稿［S34］

    - 既存のクローンは再学習が必要で、声の印象が変わる［S6］

    - 10秒でクローンできることへの悪用の懸念。公式は、クローンには本人の同意確認が必須だとしています［S7］

- <strong>評判を見るときの注意</strong>：初日の反応が中心で、ElevenLabs の社員による投稿も混ざっています。公開後の Reddit の反応は、アクセスがブロックされて取得できず<strong>未確認</strong>です。

<a id="section-8"></a>

## 8. 未確認のまま残った項目

- 通常の v4 の遅延（公式の数値なし）、v4 Turbo の1回の文字数上限、v4 専用の同時実行数

- v4 の API で Stability に連続値を指定できるか。XML の phoneme タグが v4 で動くか

- v4 で日本語のタグ（\[ささやき声で\] など）がどの程度効くか

- v3 の Stability 3段階（Creative／Natural／Robust）が、現在の公式ドキュメントに載っているか（二次情報でしか確認できず）

- IVC の必要素材の長さ（ブログは10秒、FAQ は1〜2分で食い違い）

- 割引終了日（10/12）の時間帯

- 公式による日本語の定量評価

- 公開後の Reddit の評判

- v4 に特化した YouTube 解説（見つからず）。たなか氏の動画は二次引用のみで、動画自体は未確認

- v4 で「効かなくなった」タグ（SSML と Style／Speed 以外）

<a id="section-9"></a>

## 9. 出所一覧（すべて 2026-09-29 に開いて確認）

- S1 公式ブログ「Introducing Eleven v4」 [https://elevenlabs.io/blog/eleven-v4](https://elevenlabs.io/blog/eleven-v4)

- S2 TestingCatalog [https://www.testingcatalog.com/elevenlabs-launches-eleven-v4-and-v4-turbo-voice-models/](https://www.testingcatalog.com/elevenlabs-launches-eleven-v4-and-v4-turbo-voice-models/)

- S3 公式ドキュメント「Models」 [https://elevenlabs.io/docs/models](https://elevenlabs.io/docs/models)

- S4 公式「Best practices / Prompting Eleven v4」 [https://elevenlabs.io/docs/overview/capabilities/text-to-speech/best-practices](https://elevenlabs.io/docs/overview/capabilities/text-to-speech/best-practices)

- S5 公式ヘルプ「How do audio tags work with Eleven v3 and v4?」 [https://elevenlabs.io/docs/help-center/product/core-capabilities/text-to-speech/how-do-audio-tags-work-with-eleven-v3-and-v4](https://elevenlabs.io/docs/help-center/product/core-capabilities/text-to-speech/how-do-audio-tags-work-with-eleven-v3-and-v4)

- S6 公式ドキュメント「Eleven v4」 [https://elevenlabs.io/docs/overview/capabilities/text-to-speech/eleven-v4](https://elevenlabs.io/docs/overview/capabilities/text-to-speech/eleven-v4)

- S7 公式 v4 ページ（FAQ含む） [https://elevenlabs.io/v4](https://elevenlabs.io/v4)

- S8 公式 API 料金 [https://elevenlabs.io/pricing/api](https://elevenlabs.io/pricing/api)

- S9 公式料金 [https://elevenlabs.io/pricing](https://elevenlabs.io/pricing)

- S10 TechCrunch [https://techcrunch.com/2026/09/28/elevenlabs-new-v4-speech-model-supports-more-expression-control-and-90-languages/](https://techcrunch.com/2026/09/28/elevenlabs-new-v4-speech-model-supports-more-expression-control-and-90-languages/)

- S11 公式「Text to Dialogue」 [https://elevenlabs.io/docs/overview/capabilities/text-to-dialogue](https://elevenlabs.io/docs/overview/capabilities/text-to-dialogue)

- S12 Artificial Analysis（X） [https://x.com/ArtificialAnlys/status/2104578736687653293](https://x.com/ArtificialAnlys/status/2104578736687653293)

- S13 ElevenLabs 公式X（インラインタグ） [https://x.com/ElevenLabs/status/2104572133636731068](https://x.com/ElevenLabs/status/2104572133636731068)

- S14 公式ブログ「What are Audio Tags? The complete guide」 [https://elevenlabs.io/blog/v3-audiotags](https://elevenlabs.io/blog/v3-audiotags)

- S15 公式ブログ「Situational awareness」 [https://elevenlabs.io/blog/eleven-v3-situational-awareness](https://elevenlabs.io/blog/eleven-v3-situational-awareness)

- S16 公式ブログ「Expressing emotional context」 [https://elevenlabs.io/blog/eleven-v3-audio-tags-expressing-emotional-context-in-speech](https://elevenlabs.io/blog/eleven-v3-audio-tags-expressing-emotional-context-in-speech)

- S17 公式ブログ「Character direction」 [https://elevenlabs.io/blog/eleven-v3-character-direction](https://elevenlabs.io/blog/eleven-v3-character-direction)

- S18 公式ブログ「Precision delivery control」 [https://elevenlabs.io/blog/eleven-v3-audio-tags-precision-delivery-control-for-ai-speech](https://elevenlabs.io/blog/eleven-v3-audio-tags-precision-delivery-control-for-ai-speech)

- S19 Moe Lueker「ElevenLabs V3 Tutorial」 [https://moelueker.com/blog/elevenlabs-v3-tutorial-best-settings-audio-tags-free-gpt-tool](https://moelueker.com/blog/elevenlabs-v3-tutorial-best-settings-audio-tags-free-gpt-tool)

- S20 pm-linkle「ElevenLabs日本語の実力｜196本で検証」 [https://pm-linkle.com/?p=1832](https://pm-linkle.com/?p=1832)

- S21 公式ヘルプ「What is Dialogue mode?」 [https://elevenlabs.io/docs/help-center/product/core-capabilities/text-to-speech/what-is-dialogue-mode](https://elevenlabs.io/docs/help-center/product/core-capabilities/text-to-speech/what-is-dialogue-mode)

- S22 公式「Text to Dialogue quickstart」 [https://elevenlabs.io/docs/eleven-api/guides/cookbooks/text-to-dialogue](https://elevenlabs.io/docs/eleven-api/guides/cookbooks/text-to-dialogue)

- S23 公式 API リファレンス「Create speech」 [https://elevenlabs.io/docs/api-reference/text-to-speech/convert](https://elevenlabs.io/docs/api-reference/text-to-speech/convert)

- S24 Moe Lueker の YouTube 解説 [https://youtu.be/Z2B\_pgJ9hbA](https://youtu.be/Z2B_pgJ9hbA)

- S25 公式ヘルプ「What is Eleven v3?」「How much does it cost...v3?」 [https://elevenlabs.io/docs/help-center/product/core-capabilities/text-to-speech/what-is-eleven-v3-alpha](https://elevenlabs.io/docs/help-center/product/core-capabilities/text-to-speech/what-is-eleven-v3-alpha)

- S26 fal（X） [https://x.com/fal/status/2104630460542325071](https://x.com/fal/status/2104630460542325071)

- S27 X @Mitchyotaku [https://x.com/Mitchyotaku/status/2104648997625643363](https://x.com/Mitchyotaku/status/2104648997625643363)

- S28 X @agi\_aibusi [https://x.com/agi\_aibusi/status/2104711354510909766](https://x.com/agi_aibusi/status/2104711354510909766)

- S29 X @M\_amelian [https://x.com/M\_amelian/status/2104595697513517389](https://x.com/M_amelian/status/2104595697513517389)

- S30 X @tkhr410（ElevenLabs の日本GTM担当） [https://x.com/tkhr410/status/2104573406255755335](https://x.com/tkhr410/status/2104573406255755335)

- S31 X @masahirochaen [https://x.com/masahirochaen/status/2104608729027199003](https://x.com/masahirochaen/status/2104608729027199003)

- S32 X @ZetoGroovin [https://x.com/ZetoGroovin/status/2104594647033266493](https://x.com/ZetoGroovin/status/2104594647033266493)

- S33 X @engineer\_num1 [https://x.com/engineer\_num1/status/2091723718846464472](https://x.com/engineer_num1/status/2091723718846464472)

- S34 X @iver56 [https://x.com/iver56/status/2104581657403748533](https://x.com/iver56/status/2104581657403748533)

注：X の投稿は X API で存在を確認しました（Web からの直接取得は 403 で弾かれます）。そのほかの URL は HTTP 200 で開けることを確認しています。
