# codeless-security-check

[English](../README.md) | 日本語 | [한국어](README_ko.md) | [中文](README_zh.md)

Claude Code 用のセキュリティ監査スキル。スキルや MCP サーバーをインストール前に脆弱性チェックします。

## チェック内容

- 悪意あるコード実行 (eval, exec, シェルインジェクション)
- データ漏洩 (認証情報・環境変数の外部送信)
- プロンプトインジェクション (隠し指示、Unicode トリック)
- 過剰な権限要求 (広範なファイルアクセス、危険なフラグ)
- 作者の信頼性検証 (アカウント年齢、リポジトリのシグナル)
- クロスコリレーション (複数の検出結果を組み合わせた脅威判定)

## 設計思想

**スクリプトなし。コードなし。マークダウンのみ。**

実行可能コードを一切含みません。構造化されたチェックリスト、脅威パターン、エスカレーションルールを Claude に渡し、Claude の推論能力で分析します。

なぜコードレスか？

- **攻撃面ゼロ** — スクリプトを持つセキュリティツールは、それ自体が攻撃対象になる矛盾を抱える。このスキルは侵害されない。
- **未知の脅威を検出** — 正規表現スキャナーは既知のパターンしか見つけられない。Claude は意図を推論し、パターンマッチャーでは検出できない脅威を捉える。
- **コンテキスト効率** — 合計 ~13KB。コンテキストウィンドウを圧迫しない。

## 構成

```
codeless-security-check/
  SKILL.md              # 6ステップのワークフロー
  references/
    skill-checklist.md   # .skill パッケージ用チェックリスト
    mcp-checklist.md     # MCP サーバー用チェックリスト
    threat-patterns.md   # コード例付きの既知の攻撃パターン
```

## インストール

```bash
npx skills add https://github.com/sunu-py-jp/codeless-security-check --skill codeless-security-check
```

または手動で:

```bash
cp -r codeless-security-check ~/.claude/skills/
```

## 使い方

Claude Code に聞くだけ:

- 「このスキルのセキュリティをチェックして: ~/.claude/skills/some-skill」
- 「この MCP サーバーは安全？ https://github.com/org/some-mcp」
- 「この .skill ファイルの脆弱性をレビューして」

## 実行例

実際の監査結果は [EXAMPLES_ja.md](EXAMPLES_ja.md) を参照:

| 対象 | 種類 | リスク |
|------|------|--------|
| [anthropics/skills — frontend-design](https://github.com/anthropics/skills) | スキル | SAFE |
| [sunu-py-jp/X-Twitter-MCP](https://github.com/sunu-py-jp/X-Twitter-MCP) | MCP サーバー | CAUTION |
| [tumf/mcp-shell-server](https://github.com/tumf/mcp-shell-server) | MCP サーバー | CAUTION |
| [mark3labs/mcp-filesystem-server](https://github.com/mark3labs/mcp-filesystem-server) | MCP サーバー | SAFE |
| [daymade/claude-code-skills](https://github.com/daymade/claude-code-skills) | スキルコレクション | CAUTION |
| [alirezarezvani/claude-skills](https://github.com/alirezarezvani/claude-skills) | スキルコレクション | CAUTION |

## ライセンス

[MIT](../LICENSE)
