# CLAUDE.md

このファイルは、本リポジトリで作業するAIアシスタント向けのガイドラインです。

## プロジェクト概要

**ichiyasaGitSample** は「いちばんやさしいGit&GitHubの教本」のサンプルプロジェクトです。
「Ichiyasa Git User Group」コミュニティのイベント告知用静的HTMLランディングページです。

- **種別**: 静的HTML5サイト（ビルドステップなし・パッケージマネージャーなし）
- **テンプレート**: HTML5 UP の "Read Only"（CCA 3.0ライセンス）
- **言語**: コンテンツは日本語、コードは英語

## ディレクトリ構成

```
ichiyasaGitSample/
├── .github/
│   └── workflows/
│       ├── claude.yml              # @claude メンション時に起動するClaudeアシスタント
│       └── claude-code-review.yml  # PRの自動コードレビュー（Claude）
├── assets/
│   ├── css/
│   │   ├── main.css               # メインスタイルシート（3094行・グリッド等含む）
│   │   └── font-awesome.min.css   # FontAwesome 4.6.3
│   ├── fonts/                     # FontAwesome Webフォント一式
│   └── js/
│       ├── jquery.min.js          # jQuery（minified、約94KB）
│       ├── jquery.scrolly.min.js  # スムーススクロールプラグイン
│       ├── jquery.scrollzer.min.js # ナビアクティブ状態プラグイン
│       ├── skel.min.js            # 旧レスポンシブフレームワーク（非推奨）
│       ├── util.js                # jQueryプラグイン定義（navList, panel, scrolly, scrollzer）
│       └── main.js                # アプリケーションエントリーポイント（112行）
├── images/
│   ├── avatar.png                 # コミュニティアバター（383x383、2.3KB）
│   └── banner.png                 # ヘッダーバナー（1911x439、793KB ※過大）
├── index.html                     # シングルページ本体（170行）
├── .gitignore                     # 最小限（.DS_Store のみ除外）
└── README.md                      # 改善メモ（日本語）
```

## 主要ファイル

### `index.html`
唯一のHTMLファイル。3つのセクションで構成されます。

- `#one` — コミュニティ説明（Ichiyasa Git User Group）
- `#two` — 次回イベント告知（第２回Git勉強会）
- `#three` — 過去のイベント（YouTubeの埋め込み動画）

スクリプトは `</body>` 直前に以下の順で読み込まれます。

1. `jquery.min.js`
2. `jquery.scrollzer.min.js`
3. `jquery.scrolly.min.js`
4. `skel.min.js`
5. `util.js`
6. `main.js`

### `assets/js/main.js`
skelフレームワークとjQueryでページを初期化します。

- `skel.breakpoints()` でレスポンシブブレークポイントを設定
- `$.scrolly()` でスムーススクロールを有効化
- `$.scrollzer()` でナビのアクティブ状態追跡を有効化
- モバイル向けタイトルバーとオフキャンバスナビゲーションのトグルを生成

### `assets/css/main.css`
3094行のスタイルシート。大半はテンプレートの未使用ボイラープレートです。
Google Fonts（Lato、Source Code Pro）はこのファイル内の `@import` で読み込まれます（レンダリングブロッキングに注意）。

## 開発ワークフロー

### ビルドステップなし
静的サイトのため、`index.html` をブラウザで直接開くか、静的ファイルサーバーで配信するだけです。

```bash
python3 -m http.server 8080
# または
npx serve .
```

### 変更手順
1. コンテンツを変更する場合 → `index.html` を編集
2. スタイルを変更する場合 → `assets/css/main.css` を編集
3. 動作を変更する場合 → `assets/js/main.js` を編集
4. ブラウザをリロードして確認

### ブランチ命名規則
- `master` — 安定したメインブランチ
- `claude/<説明>-<id>` — Claudeが作成するフィーチャーブランチ
- すべての変更はプルリクエスト経由でマージする

### Gitワークフロー
```bash
git checkout -b <ブランチ名>
# 変更を加える
git add <特定のファイル>
git commit -m "わかりやすいメッセージ"
git push -u origin <ブランチ名>
# プルリクエストを作成する
```

## CI/CD: GitHub Actions

### `claude.yml` — Claudeコードアシスタント
IssueやPRのコメントに `@claude` が含まれると起動します。Claudeは以下を実行できます。

- コードベースに関する質問への回答
- 変更の実装とコミット
- PRのCI結果の参照

必要なシークレット: `CLAUDE_CODE_OAUTH_TOKEN`

### `claude-code-review.yml` — 自動コードレビュー
PRイベント（opened, synchronize, ready_for_review, reopened）で自動起動します。
`code-review` プラグインを使ってClaudeが自動でレビューコメントを投稿します。

必要なシークレット: `CLAUDE_CODE_OAUTH_TOKEN`

## 既知の課題（READMEより）

以下は改善が望まれる点です。指示なしに勝手に修正しないでください。

**コンテンツ**
- スピーカー・セッション情報がプレースホルダーのまま（`未定`）
- イベント参加申し込みリンクがない（connpass / Doorkeeper等）
- イベントの年が記載されていない（「3月23日」のみ）
- フッターの著作権表記が `© Untitled` のまま
- ナビのラベル（「Japan Git User Group」）とセクション見出し（「Ichiyasa Git User Group」）が不一致

**不足ファイル**
- `images/speaker1.png` と `images/speaker2.png` がHTMLで参照されているが存在しない

**パフォーマンス**
- `banner.png` が793KB（WebP変換と圧縮が必要）
- Google Fontsを `@import` で読み込んでいる（レンダリングブロッキング）
- `font-awesome.min.css` を読み込んでいるがアイコンを使っていない
- jQueryを94KB読み込んでいるがスムーススクロールとナビのみに使用

**コード品質**
- `skel.min.js` はメンテされていない旧フレームワーク
- スクリプトタグに `defer` / `async` がない
- `main.css` に未使用のCSSが大量にある
- `.gitignore` が最小限すぎる

**SEO・アクセシビリティ**
- `<meta name="description">` がない
- すべての画像の `alt=""` が空
- OGPタグがない

## AIアシスタント向けの規約

- **コンテンツ変更**は `index.html` のみ編集する
- **スタイル変更**は `assets/css/main.css` のみ編集する（インラインスタイルは追加しない）
- **動作変更**は `assets/js/main.js` のみ編集する（HTMLに `<script>` ブロックを追加しない）
- **依存関係の追加**は事前に確認する（このプロジェクトは意図的に依存関係ゼロ）
- **新規ファイルの作成**は最小限にとどめ、既存ファイルの編集を優先する
- **git add** はファイル名を指定して実行し、`git add -A` や `git add .` は使わない
- **コミットメッセージ**は既存の履歴に合わせて日本語または英語で書く
- **すべての変更**はプルリクエスト経由でマージし、`master` に直接プッシュしない
- **スピーカー画像**（`speaker1.png`, `speaker2.png`）は存在しないため、該当箇所を変更する際は注意する
