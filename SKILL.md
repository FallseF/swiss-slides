---
name: swiss-slides
description: >
  Swiss Modernスタイルのプレゼンテーションスライドを、reveal.js + 単一HTMLファイルで生成するスキル。
  ゼロ依存（CDN読み込みのみ）で、ブラウザで開くだけで動く。
  スライド構成案テキスト、Markdownファイル、または口頭指示からスライドを生成できる。
  Use when: ユーザーがスライド作成、プレゼン作成、講義資料作成を依頼したとき。
  /swiss-slides で呼び出す。引数にファイルパス、テキスト、またはテーマの指示を受け取る。
---

# swiss-slides: Swiss Modernスタイルのスライド生成

## 概要

reveal.js 5.1.0（CDN）を使った、単一の自己完結HTMLファイルとしてプレゼンテーションスライドを生成する。デザインはSwiss Modern（国際タイポグラフィ様式）を基調とし、AI臭さのない洗練されたスライドを出力する。

## ワークフロー

### ステップ1: 入力の判定

引数を受け取ったら、以下の順で判定する。

1. 引数がファイルパスとして存在するか確認する（Readツールで読み込みを試みる）
2. ファイルとして読み込めた場合、その内容をスライドの元ネタとして使う
3. ファイルでない場合は、引数のテキスト自体をスライドのテーマや指示として扱う

### ステップ2: スタイルプレビュー（Show, Don't Tell）

ユーザーにデザインの好みを言葉で聞くのではなく、視覚的に選ばせる。

1. 入力内容からタイトルスライド1枚分のミニHTMLを3パターン生成する（各パターンはカラーパレット違い）
   - パターンA: デフォルトのグリーン系（--accent: #2D6A4F）
   - パターンB: モノクローム系（--accent: #333333）
   - パターンC: ウォーム系（--accent: #8B4513）
2. 3ファイルをWriteツールで一時出力し、ユーザーに「ブラウザで開いて好みを選んでください」と伝える
3. ユーザーが選んだパレットで本番生成に進む

ユーザーが「プレビュー不要」「デフォルトで」と言った場合はスキップしてよい。

### ステップ3: ユーザーへの確認

AskUserQuestionツールで以下を確認する。一度に聞きすぎず、2〜3問に絞る。

聞くべきこと：
- 出力先のファイルパス（デフォルト: 作業ディレクトリ/slides/presentation.html）
- スライド枚数の目安（指定がなければ内容に応じて自動判断）
- PDF出力も必要か（デフォルト: 不要）

### ステップ4: スライド構成の設計

内容を分析し、スライド構成を決める。以下の原則に従う。

- 1スライド1メッセージ。詰め込まない
- タイトルスライド、ゴール、本編、演習/ワーク、まとめ、次回予告の流れを基本とする
- テキスト量が多い場合は積極的に分割する
- コードやプロンプトは専用のダークブロックスライドにする

コンテンツ密度の上限（厳守）：
- 箇条書き: 1スライドあたり最大5項目
- グリッドカード: 1スライドあたり最大6枚
- 本文テキスト: 1スライドあたり最大4段落（各段落2〜3行以内）
- タイトルスライド: 見出し1つ + サブタイトル1行のみ

### ステップ5: HTML生成

以下のデザインシステムに厳密に従ってHTMLファイルを生成する。Writeツールで出力する。

### ステップ6: PDF出力（任意）

ユーザーがPDF出力を希望した場合、DeckTapeで変換する。

```bash
npx decktape reveal --size 1280x720 "file://${PWD}/出力ファイル.html" 出力ファイル.pdf
```

Chromeのパスが必要な場合は `--chrome-path` オプションを付ける。macOSの場合：
```bash
--chrome-path "/Applications/Google Chrome.app/Contents/MacOS/Google Chrome"
```

## デザインシステム（厳守）

### 技術スタック

- reveal.js 5.1.0: CDNから読み込み（https://cdn.jsdelivr.net/npm/reveal.js@5.1.0/）
- フォント: Google Fonts から Noto Sans JP（300, 400, 700, 900）と Inter（300, 400, 700, 900）
- 単一HTMLファイル: CSS/JSはすべてインライン。外部ファイル依存なし

### カラーパレット（デフォルト）

```
--bg: #FAFAFA        （背景: ほぼ白）
--text: #1A1A1A      （本文: ほぼ黒）
--accent: #2D6A4F    （アクセント: 深い緑）
--secondary: #40916C （セカンダリ: 中間の緑）
--light-green: #D8F3DC（薄い緑: 背景用）
--dark: #1B4332      （ダーク: タイトルスライド背景用）
--danger: #D32F2F    （警告: NG表示用）
```

カラーパレットはCSS変数で管理する。ユーザーから別のパレットを指示された場合は変数を差し替えるだけで対応する。

### タイポグラフィ

- 日本語テキスト: Noto Sans JP
- 英語ラベル・数字: Inter
- スライドタイトル（h1）: font-weight 900, clamp(2em, 5vw, 3.2em), letter-spacing -0.02em
- セクションタイトル（h2）: font-weight 900, clamp(1.6em, 4vw, 2.4em)
- 小見出し（h3）: font-weight 700, clamp(1.2em, 3vw, 1.6em)
- 本文: font-weight 400, clamp(0.75em, 1.5vw, 0.95em), line-height 1.6
- ベースフォントサイズ: 28px

### レイアウトの原則

- テキストは左寄せが基本。中央寄せはタイトルスライドと休憩スライドのみ
- 非対称グリッドを多用する（grid-template-columns: 1.4fr 1fr）
- 余白を攻撃的に取る。詰め込まない
- グラデーション、ドロップシャドウ、角丸は使わない
- セクションのパディング: padding: 40px 60px
- カード間のギャップ: 48px
- 各スライドは必ずビューポート内に収める（はみ出し厳禁）

### スライドタイプ別のCSS

以下のCSSクラスを全HTMLファイルの<style>内に含める。

```css
:root {
  --bg: #FAFAFA;
  --text: #1A1A1A;
  --accent: #2D6A4F;
  --secondary: #40916C;
  --light-green: #D8F3DC;
  --dark: #1B4332;
  --danger: #D32F2F;
}

.reveal-viewport {
  background: var(--bg);
}

.reveal {
  font-family: 'Noto Sans JP', 'Inter', sans-serif;
  font-weight: 400;
  color: var(--text);
  font-size: 28px;
}

.reveal .slides {
  text-align: left;
}

.reveal .slides section {
  padding: 40px 60px;
  box-sizing: border-box;
  height: 100vh;
  height: 100dvh;
  overflow: hidden;
  display: flex !important;
  flex-direction: column;
  justify-content: center;
}

.reveal h1, .reveal h2, .reveal h3, .reveal h4 {
  font-family: 'Noto Sans JP', sans-serif;
  color: var(--text);
  text-transform: none;
  letter-spacing: -0.02em;
  line-height: 1.1;
  margin-bottom: 0.4em;
  text-align: left;
}

.reveal h1 { font-weight: 900; font-size: clamp(2em, 5vw, 3.2em); }
.reveal h2 { font-weight: 900; font-size: clamp(1.6em, 4vw, 2.4em); }
.reveal h3 { font-weight: 700; font-size: clamp(1.2em, 3vw, 1.6em); }

.reveal p {
  line-height: 1.6;
  margin-bottom: 0.6em;
  font-size: clamp(0.75em, 1.5vw, 0.95em);
}

/* アクセント */
.accent { color: var(--accent); }
.accent-bg {
  background: var(--accent);
  color: #fff;
  padding: 2px 10px;
  display: inline-block;
}

/* 背景バリエーション */
.light-bg {
  background: var(--light-green);
  padding: 24px 32px;
  margin: 16px 0;
}

.dark-bg {
  background: var(--dark);
  color: #fff;
  padding: 24px 32px;
  margin: 16px 0;
}

/* タグ・ラベル */
.tag {
  display: inline-block;
  font-family: 'Inter', sans-serif;
  font-weight: 700;
  font-size: 0.55em;
  letter-spacing: 0.08em;
  text-transform: uppercase;
  color: var(--secondary);
  margin-bottom: 8px;
}

/* サブタイトル */
.subtitle {
  font-weight: 300;
  font-size: 1.2em;
  color: var(--secondary);
  margin-top: 0.3em;
}

/* グリッドレイアウト */
.two-col {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 48px;
  align-items: start;
  margin-top: 24px;
}

.two-col-asymmetric {
  display: grid;
  grid-template-columns: 1.4fr 1fr;
  gap: 48px;
  align-items: start;
  margin-top: 24px;
}

.three-col {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr;
  gap: 32px;
  align-items: start;
  margin-top: 24px;
}

.four-col {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr 1fr;
  gap: 24px;
  align-items: start;
  margin-top: 24px;
}

/* OK/NGカラム */
.ok-col {
  border-left: 4px solid var(--accent);
  padding-left: 24px;
}
.ok-col h3 { color: var(--accent); }

.ng-col {
  border-left: 4px solid var(--danger);
  padding-left: 24px;
}
.ng-col h3 { color: var(--danger); }

/* プロンプト・コードブロック */
.prompt-block {
  background: var(--text);
  color: #E0E0E0;
  font-family: 'Inter', 'Noto Sans JP', monospace;
  font-size: 0.72em;
  line-height: 1.7;
  padding: 28px 32px;
  margin: 16px 0;
  overflow-x: auto;
  white-space: pre-wrap;
  word-break: break-all;
}
.prompt-block .comment { color: var(--secondary); }

/* カード */
.card {
  background: #fff;
  border: 1px solid #E0E0E0;
  padding: 24px;
}

.card-accent {
  background: #fff;
  border-left: 4px solid var(--accent);
  padding: 24px;
}

/* 大きな数字（ステップ番号など） */
.big-number {
  font-family: 'Inter', sans-serif;
  font-weight: 900;
  font-size: 4em;
  color: var(--light-green);
  line-height: 1;
}

/* ダークスライド（タイトル、区切り等） */
.slide-dark {
  background: var(--dark) !important;
  color: #fff !important;
}
.slide-dark h1, .slide-dark h2, .slide-dark h3 { color: #fff; }
.slide-dark .tag { color: var(--light-green); }
.slide-dark .subtitle { color: var(--light-green); }

/* ライトグリーンスライド（休憩、ワーク等） */
.slide-light {
  background: var(--light-green) !important;
}

/* チェックリスト */
.checklist {
  list-style: none;
  padding: 0;
}
.checklist li {
  padding: 12px 0;
  border-bottom: 1px solid #E0E0E0;
  font-size: 1.1em;
}
.checklist li::before {
  content: "□ ";
  color: var(--accent);
  font-weight: 700;
}

/* アクセシビリティ: モーション低減対応 */
@media (prefers-reduced-motion: reduce) {
  .reveal .slides section {
    transition: none !important;
  }
  .reveal .slide-background {
    transition: none !important;
  }
}

/* レスポンシブ: 高さが低い画面への対応 */
@media (max-height: 700px) {
  .reveal { font-size: 24px; }
  .reveal .slides section { padding: 28px 40px; }
}

@media (max-height: 600px) {
  .reveal { font-size: 20px; }
  .reveal .slides section { padding: 20px 32px; }
}

@media (max-height: 500px) {
  .reveal { font-size: 16px; }
  .reveal .slides section { padding: 16px 24px; }
}
```

### HTMLテンプレート構造

```html
<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>（スライドタイトル）</title>

<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;700;900&family=Noto+Sans+JP:wght@300;400;700;900&display=swap" rel="stylesheet">

<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/reveal.js@5.1.0/dist/reveal.css">
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/reveal.js@5.1.0/dist/theme/white.css">

<style>
  /* ここに上記CSSを全て入れる */
</style>
</head>
<body>
<div class="reveal">
<div class="slides">

  <!-- タイトルスライド例 -->
  <section class="slide-dark">
    <div class="tag">LECTURE 01</div>
    <h1>スライドタイトル</h1>
    <p class="subtitle">サブタイトル</p>
    <aside class="notes">スピーカーノート</aside>
  </section>

  <!-- 通常スライド例 -->
  <section>
    <h2>見出し</h2>
    <div class="two-col-asymmetric">
      <div>
        <p>メインコンテンツ</p>
      </div>
      <div class="light-bg">
        <p>補足情報</p>
      </div>
    </div>
    <aside class="notes">スピーカーノート</aside>
  </section>

  <!-- 休憩スライド例 -->
  <section class="slide-light" style="text-align: center;">
    <p class="tag">BREAK</p>
    <h1>休憩</h1>
    <p class="big-number">5<span style="font-size: 0.4em;">min</span></p>
    <aside class="notes">5分休憩</aside>
  </section>

</div>
</div>

<script src="https://cdn.jsdelivr.net/npm/reveal.js@5.1.0/dist/reveal.js"></script>
<script>
  Reveal.initialize({
    hash: true,
    transition: 'slide',
    width: 1280,
    height: 720,
    margin: 0,
    minScale: 0.2,
    maxScale: 2.0,
    center: false
  });
</script>
</body>
</html>
```

### スライドタイプのパターン集

以下のパターンを状況に応じて使い分ける。

タイトルスライド: slide-dark + tag + h1 + subtitle。プレゼン冒頭と各セクションの区切りに使う。

ゴールスライド: h2 + two-col で目標を2つ並べる。各目標はcard-accentで囲む。

コンテンツスライド（左重め）: h2 + two-col-asymmetric。左に本文、右にlight-bgの補足ボックス。

比較スライド: h2 + two-col。左にok-col、右にng-col。

ステップスライド: h2 + three-col。各ステップをcard内にbig-number + 説明で配置。

プロンプト表示: slide-dark + h2 + prompt-block。コード/プロンプトは必ずダーク背景のブロック内に表示。

演習説明: h2 + card-accent で題材・手順・配布物を整理。

チェックリスト: h2 + checklist。レビュー観点や確認項目に使う。

休憩: slide-light + 中央寄せ + big-number。装飾しない。

まとめ/クロージング: slide-dark + 要点リスト。最終スライドは簡潔に。

### スピーカーノート

全スライドに<aside class="notes">でスピーカーノートを付ける。ノートには以下を含める。

- そのスライドで伝えるべき要点
- 時間配分の目安（タイムスケジュールがある場合）
- 受講者への問いかけや補足説明

### 禁止事項

- グラデーション背景を使わない
- ドロップシャドウを使わない
- 角丸（border-radius）を使わない
- アイコンフォントやSVGアイコンを使わない
- アニメーションは最小限（reveal.jsのスライドトランジションのみ）
- 紫系、ピンク系の配色を使わない（AI臭くなる）
- 全スライド中央寄せにしない
- 小さいフォントサイズ（0.7em未満）を本文に使わない
- 1スライドに箇条書き6項目以上を詰め込まない
- 1スライドにグリッドカード7枚以上を配置しない
- スクロールが必要になる量のコンテンツを入れない

## 出力の制約

- HTMLファイル1つだけを出力する。説明文や解説は不要
- ファイルは必ずWriteツールで書き出す
- 出力後にブラウザで開けることを確認するメッセージを添える
