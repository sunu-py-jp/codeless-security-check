# 監査の実行例

codeless-security-check による実際のセキュリティ監査結果です。

> **掲載プロジェクトについての重要な注記**
>
> ここに掲載しているプロジェクトはすべて、Claude Code および MCP エコシステムへの優れた貢献です。人気があり、活発にメンテナンスされ、開発者が実際に使用するツールの代表例であるからこそ選定しました。**いずれのプロジェクトにも悪意あるコードや意図的な脆弱性は含まれていません。**
>
> 「CAUTION」評価はプロジェクト自体の欠陥を示すものではありません。シェル実行、ファイルシステムアクセス、外部 API 呼び出しなど、ソフトウェアの動作を*利用者が*理解した上でインストールすべきであるという評価です。これらはツールの目的に本来備わる特性であり、欠点ではありません。シェル実行 MCP はシェルを実行するのが当然であり、Twitter クライアントは Twitter API を呼び出すのが当然です。本監査はこれらの振る舞いを可視化し、利用者が十分な情報に基づいて判断できるようにするものです。
>
> ここに掲載されているすべての作者・メンテナーの方々に深い敬意を表します。コミュニティのためにオープンソースツールを構築・維持することは、多大な労力と寛大さを要する営みです。その貢献に心から感謝いたします。

---

## anthropics/skills — frontend-design

**対象**: anthropics/skills / frontend-design (スキル)
**リスクレベル**: SAFE

### 作者の信頼性
- **公開者**: Anthropic
- **アカウント年齢**: 5年以上（2020年〜）
- **シグナル**: 72,243 stars, 7,394 forks, 27,351 followers
- **評価**: 信頼（既知の公開者）

### 概要

スクリプトなし、ネットワークアクセスなし、実行可能コードなしの純粋なマークダウンスキル。フロントエンド開発のためのデザインガイドラインと美的方向性のみを含む。コードレススキルパターンの模範的な実装。

### 検出結果

#### Critical（即座の脅威）
- なし

#### Warning（潜在的リスク）
- なし

#### Info（備考）
- `SKILL.md` にはデザインガイドラインと美的指示のみ — 実行コード、シェルコマンド、外部参照なし。
- スクリプト、リファレンス、静的フォント以外のアセットなし。
- 攻撃面ゼロ。

### 推奨
[x] 安全にインストール可能

---

## sunu-py-jp/X-Twitter-MCP

**対象**: sunu-py-jp/X-Twitter-MCP (MCP サーバー)
**リスクレベル**: CAUTION

### 作者の信頼性
- **公開者**: sunu-py-jp
- **アカウント年齢**: 1ヶ月未満（2026年2月〜）
- **シグナル**: 0 stars, 0 forks, 0 followers, MIT license
- **評価**: 低信頼（新規アカウント、コミュニティシグナルなし）

### 概要

Twitter/X API v2 へのフルアクセスを提供する TypeScript MCP サーバー。公式 `twitter-api-v2` ライブラリを使用したクリーンな実装で、Zod スキーマによる適切な入力バリデーションを備える。悪意あるパターンは検出されなかったが、強力な機能（ツイート投稿、DM 送信、フォロー管理）を持つため慎重な設定が必要。

### 検出結果

#### Critical
- なし

#### Warning
- `src/client.ts:9-13` — 環境変数から4つの API 認証情報を読み取り（`X_API_KEY`, `X_API_SECRET`, `X_ACCESS_TOKEN`, `X_ACCESS_TOKEN_SECRET`）。意図通り Twitter API に送信されるが、認証情報の権限範囲に注意が必要。
- `src/tools/dm.ts:7-28` — `send_dm` ツールは任意のユーザーに DM を送信可能。信頼されていないクライアントからアクセスされた場合に悪用の可能性がある強力な機能。
- `src/tools/tweets.ts:6-31` — `post_tweet` ツールは認証済みアカウントでツイートを投稿可能。この書き込み機能に注意が必要。
- 作者の信頼性が低い（コミュニティシグナルのない新規アカウント）。上記の検出結果との相互相関により全体的な注意レベルが上昇。

#### Info
- クライアント・サーバー・ツールが明確に分離された、構造の良いコードベース。
- `src/server.ts:36-51` — `X_ENABLED_GROUPS` と `X_DISABLED_TOOLS` 環境変数でツールアクセスを細かく制御可能。優れたセキュリティ設計。
- すべてのツール入力に Zod スキーマによる入力バリデーション。
- 依存関係は最小限で著名なもののみ（`@modelcontextprotocol/sdk`, `twitter-api-v2`, `zod`）。
- `eval`, `exec`, `child_process`、難読化コード、データ流出パターンは検出されず。

### 推奨
[x] 注意してインストール — 書き込みアクセスが明示的に必要でない限り、`X_ENABLED_GROUPS` で読み取り専用ツール（例: `tweets:get,timelines:get`）に制限すること。API トークンの権限を確認すること。

---

## tumf/mcp-shell-server

**対象**: tumf/mcp-shell-server (MCP サーバー)
**リスクレベル**: CAUTION

### 作者の信頼性
- **公開者**: tumf
- **アカウント年齢**: 15年以上（2009年〜）
- **シグナル**: 166 stars, 42 forks, 97 followers, MIT license
- **評価**: 信頼

### 概要

コマンドホワイトリスト方式のシェルコマンド実行 MCP サーバー。ホワイトリスト強制、シェル演算子ブロック、引数エスケープによる適切なコマンドバリデーションロジックを備える。セキュリティはユーザーによる `ALLOW_COMMANDS` 環境変数の設定に依存。

### 検出結果

#### Critical
- なし

#### Warning
- `src/mcp_shell_server/process_manager.py:160` — `asyncio.create_subprocess_shell()`（shell=True 相当）を使用。コマンドは事前にバリデーションされるが、シェル解釈によるバイパスの可能性あり。
- `src/mcp_shell_server/command_validator.py:24-27` — セキュリティ境界が `ALLOW_COMMANDS` 環境変数に依存。過度に広い設定（例: `bash`, `sh`, `python` を許可）はすべての保護を無効化する。

#### Info
- コマンドホワイトリストバリデーションとシェル演算子ブロック（`; && ||`）が適切に実装。
- パイプコマンドはバリデーション済み — パイプライン内の各コマンドが個別にホワイトリスト確認される。
- 引数エスケープに `shlex.quote()` を使用。
- データ流出、難読化コード、プロンプトインジェクションは検出されず。

### 推奨
[x] 注意してインストール — セキュリティは厳密な `ALLOW_COMMANDS` 設定に依存。インタプリタ（`bash`, `sh`, `python`）をホワイトリストに含めないこと。

---

## mark3labs/mcp-filesystem-server

**対象**: mark3labs/mcp-filesystem-server (MCP サーバー)
**リスクレベル**: SAFE

### 作者の信頼性
- **公開者**: mark3labs
- **アカウント年齢**: 9年以上（2016年〜）
- **シグナル**: 599 stars, 98 forks, 198 followers, MIT license
- **評価**: 信頼

### 概要

Go で実装された優れたファイルシステム MCP サーバー。シンボリックリンク解決を含むパスバリデーションにより、すべての操作が許可ディレクトリ内に適切にサンドボックス化。ネットワーク通信やデータ流出ベクターは検出されず。

### 検出結果

#### Critical
- なし

#### Warning
- なし

#### Info
- `filesystemserver/handler/helper.go:42-86` — `validatePath()` が絶対パスに変換、許可ディレクトリとの照合、シンボリックリンクの解決によりトラバーサル攻撃を防止。
- `filesystemserver/handler/helper.go:15-40` — `isPathInAllowedDirs()` が末尾セパレータチェックによりプレフィックス攻撃を防止（例: `/tmp/foo` は `/tmp/foobar` にマッチしない）。
- `filesystemserver/handler/delete_file.go:96` — `os.RemoveAll()` による再帰的削除が可能だが、明示的な `recursive=true` フラグが必要。
- ネットワーク呼び出し、外部データ送信、難読化コードなし。

### 推奨
[x] 安全にインストール可能

---

## daymade/claude-code-skills

**対象**: daymade/claude-code-skills (スキルコレクション)
**リスクレベル**: CAUTION

### 作者の信頼性
- **公開者**: daymade
- **アカウント年齢**: 12年以上（2013年〜）
- **シグナル**: 592 stars, 67 forks, 160 followers, MIT license
- **評価**: 信頼

### 概要

幅広いユースケースをカバーする30以上の Claude Code スキルの大規模コレクション。一部のスキルは外部 API にアクセスし、API 認証情報を環境変数から読み取るスクリプトを含む。悪意あるパターンは検出されず。コレクションの規模が大きいため、使用前に個別のスキルをレビューすることを推奨。

### 検出結果

#### Critical
- なし

#### Warning
- `twitter-reader/scripts/fetch_tweet.py:24,34-36` — 環境変数から `JINA_API_KEY` を読み取り、curl 経由で `r.jina.ai` に送信。正当な API 利用だが認証情報の外部送信を伴う。
- `cloudflare-troubleshooting/scripts/check_cloudflare_config.py:38-87` — `requests` ライブラリで外部エンドポイントに HTTP リクエスト。Cloudflare API 認証情報を読み取り。
- `cloudflare-troubleshooting/scripts/fix_ssl_mode.py:52-95` — API 経由で Cloudflare SSL 設定を変更。外部サービスへの書き込みアクセス。

#### Info
- 複数のスキルが `subprocess` を使用（markdown-tools, cli-demo-generator, youtube-downloader, skill-creator）— すべて正当な使用でシェルインジェクションのリスクなし。
- `macos-cleaner/scripts/safe_delete.py:99` — 機密ディレクトリ（.ssh, credentials）を削除から保護。
- 難読化コード、base64 ペイロード、プロンプトインジェクションパターンは検出されず。
- 大規模コレクション — インストール前に個別スキルのレビューを推奨。

### 推奨
[x] 注意してインストール — 使用前に個別スキルをレビューすること。外部 API アクセスを持つスキル（twitter-reader, cloudflare-troubleshooting）は環境変数による API 認証情報が必要。

---

## alirezarezvani/claude-skills

**対象**: alirezarezvani/claude-skills (スキルコレクション)
**リスクレベル**: CAUTION

### 作者の信頼性
- **公開者**: alirezarezvani
- **アカウント年齢**: 12年以上（2013年〜）
- **シグナル**: 1,889 stars, 228 forks, 213 followers, MIT license
- **評価**: 信頼

### 概要

エンジニアリング、ビジネス、マーケティング、コンプライアンスの各分野をカバーする最大の Claude Code スキルコレクション（50以上のスキル）。スクリプトは主にローカル分析とコード生成を実行。悪意あるパターンは検出されず。コレクションの規模により完全なレビューは非現実的 — 必要に応じて個別スキルをレビューすること。

### 検出結果

#### Critical
- なし

#### Warning
- `engineering-team/senior-backend/scripts/api_load_tester.py:26-28` — `urllib` でユーザー指定のエンドポイントに HTTP リクエストを送信。負荷テスト目的だが任意の URL に向けることが可能。
- コレクションには50以上のスキルにわたる100以上の Python スクリプトが含まれる — 完全な手動レビューは非現実的。コレクション全体ではなく個別スキルをインストールすること。

#### Info
- `engineering-team/senior-security/scripts/secret_scanner.py:384` — `__import__('datetime')` を使用。通常とは異なるパターンだが無害（datetime のみをインポート）。
- `engineering/tech-debt-tracker/assets/sample_codebase/src/payment_processor.py` — `requests.post()` 呼び出しを含むが、実行可能なスキルコードではなくサンプル/デモファイル。
- `engineering/migration-architect/scripts/rollback_generator.py` — curl コマンドを含むが、直接実行ではなく生成スクリプト用のテンプレート文字列。
- データ流出、難読化コード、プロンプトインジェクションパターンは検出されず。

### 推奨
[x] 注意してインストール — コレクション全体ではなく個別スキルをインストールすること。特に engineering-team/senior-backend のスクリプトは使用前にレビューすること。
