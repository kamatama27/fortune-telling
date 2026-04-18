# ゆめいろべあーず ハッピーしんだん

## プロジェクト概要
小学生・子供向けの無料MBTI性格診断サイト。12問の質問に答えると全16タイプからくまのキャラクターで結果が表示される。ターゲットは小学生の女の子。

## 正式URL
- **本番**: https://shindan.yonde-web.com/
- **ホスティング**: GitHub Pages（カスタムドメイン）
- **旧URL**: kamatama27.github.io/fortune-telling/（同一リポジトリ、CNAMEで移行済み）

## 技術スタック
- 静的HTML/CSS/JS（単一ファイル `index.html`）
- サーバーサイド処理なし
- Google Analytics: `G-VWMFJYCKS8`

## ファイル構成
```
index.html              - メインページ（診断UI・ロジック全て含む）
CNAME                   - カスタムドメイン設定
robots.txt              - クロール指示
sitemap.xml             - サイトマップ（17ページ）
yumeiro_logo.png        - サイトロゴ
yumeiro_bears.png       - サイドベアーズ画像
yumeiro_back.png        - 背景画像
tipe_chara/             - 16タイプのくまキャラ画像
  yumeirobear_enfj.png  - 各タイプの結果画像（16種）
  ...
result/                 - OGP用リダイレクトページ（16タイプ分）
  enfj/index.html       - 各タイプ専用OGPメタタグ + メインへリダイレクト
  ...
```

## index.html の構造

### CSS（`<style>`内）
- CSS変数でテーマカラー管理（`--pink`, `--purple`, `--blue`など）
- レスポンシブ対応（900px, 600pxブレークポイント）
- PC: サイドベアーズ表示 / モバイル: 上下ベアーズ表示

### HTML画面構成
1. **スタート画面** (`#start-screen`) - 説明 + 「じぶん/おともだち」2つのボタン
2. **おともだち名前入力画面** (`#friend-name-screen`) - 名前入力欄 + 「ちゃん」サフィックス
3. **質問画面** (`#quiz-screen`) - プログレスバー + 質問カード + 戻るボタン
4. **結果画面** (`#result-screen`) - くま画像 + タイプ名 + 特徴 + スコアバー + シェアボタン

### JavaScript
- **質問データ**: 12問（EI/SN/TF/JP各3問）、表示順はシャッフル
- **16タイプデータ**: `typeData` オブジェクト（name, emoji, traits, desc）
- **状態管理**: `currentQ`, `scores`, `answers`, `shuffledQuestions`, `quizMode`, `friendName`
- **診断モード**: 'self'（自分）/ 'friend'（おともだち）
- **友達モード**: 名前入力（空欄時デフォルト「ともくまちゃん」）、質問中にラベル表示、結果に名前反映
- **シェア機能**: LINE・X・Instagram（画像保存）
- **共有リンク対応**: `?type=enfj` パラメータで結果直接表示

## SNSシェア仕様

### LINE
- `https://line.me/R/share?text=...` で共有
- テキスト + URL

### X (Twitter)
- `twitter.com/intent/tweet` でポップアップウィンドウ（550x420）
- ハッシュタグ: `#性格診断 #16タイプ #ゆめいろべあーず`
- 友達モード時は「〇〇ちゃんは」を含むテキスト

### Instagram
- くま画像の直接ダウンロード（`<a download>`方式）
- ユーザーが手動でInstagramに投稿

### OGPプレビュー画像
- シェアURLは `/result/<type>/` 形式（例: `/result/enfj/`）
- 各 `result/<type>/index.html` に静的OGPタグ（`og:image`にくま画像）
- クローラーがOGP読み取り後、JSでメインページにリダイレクト
- リダイレクト先: `/?type=<type>` → メインページで結果表示
- GitHub Pagesは動的OGP不可のため、この静的ファイル方式で対応

## result/<type>/index.html の構成
各ファイルは同じ構造:
- タイプ別の `og:title`, `og:description`, `og:image`, `og:url`
- `twitter:card` = `summary_large_image`
- Google Analyticsタグ（`G-VWMFJYCKS8`）
- `<meta http-equiv="refresh">` + JS `window.location.replace` でメインページへリダイレクト

## SEO対応
- `<link rel="canonical">` 設定済み（各ページ）
- `robots.txt` でサイトマップURL指定
- `sitemap.xml` でトップ + 16結果ページを登録
- GSCにサイトマップ送信済み

## 注意事項
- `file://` プロトコルでは画像保存ボタン（`<a download>`）が動作しない（`https://` では正常動作）
- テキストは全てひらがな中心（ターゲットが小学生のため）
- OGP画像を変更する場合は `index.html` の `og:image` と `result/` 配下16ファイルの両方を更新する必要がある
- タイプ追加時は `typeData`、`tipe_chara/` 画像、`result/` ページ、`sitemap.xml` の4箇所を更新
