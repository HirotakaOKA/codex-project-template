# [PROJECT_NAME] Documentation

この文書は、現在実装されているシステムの利用方法と技術概要を説明する。

## What [PROJECT_NAME] Is

`[PROJECT_NAME]` は、[対象ユーザー] が [解決する問題] を行うための [システム種別] である。

主な機能:

- [機能1]
- [機能2]
- [機能3]

## Status

- Current phase: `[PHASE]`
- Supported environment: `[ENVIRONMENT]`
- Last verified: `[YYYY-MM-DD]`
- Known limitations: [NONEまたは制約]

ローカルの文書整備作業計画は `docs/plans.md` に保持する。これは利用者向け仕様や恒久的な履歴の正本ではない。

## Local Setup

Requirements:

- `[OS]`
- `[RUNTIME_AND_VERSION]`
- `[SDK_OR_TOOL]`

Install:

```sh
[INSTALL_COMMAND]
```

Start:

```sh
[START_COMMAND]
```

起動後:

- Application: `[URL_OR_EXECUTABLE]`
- API or health check: `[URL_OR_COMMAND]`

Configuration:

| Setting | Required | Default | Description |
|---|---:|---|---|
| `[SETTING]` | Yes | - | [説明] |
| `[SETTING]` | No | `[VALUE]` | [説明] |

## Verification Commands

| Purpose | Command |
|---|---|
| Development | `[DEV_COMMAND]` |
| Build | `[BUILD_COMMAND]` |
| Lint | `[LINT_COMMAND]` |
| Typecheck | `[TYPECHECK_COMMAND]` |
| Tests | `[TEST_COMMAND]` |
| Integration tests | `[INTEGRATION_COMMAND]` |
| Package or export | `[EXPORT_COMMAND]` |

## Demo Recipes

### Basic Workflow

1. `[START_COMMAND]` を実行する。
2. [初期状態を確認する]
3. [主要機能を操作する]
4. [期待結果を確認する]

### [FEATURE_NAME]

1. [操作]
2. [操作]
3. [期待結果]

## Repository Structure Overview

```text
/
├─ src/          # アプリケーションコード
├─ tests/        # 自動テスト
├─ docs/         # 設計および利用文書
├─ scripts/      # 開発・ビルド用スクリプト
├─ examples/     # サンプルデータ
└─ [OTHER]/      # [説明]
```

主要な責務:

- `src/[MODULE]`: [責務]
- `src/[MODULE]`: [責務]
- `tests/[MODULE]`: [テスト対象]
- `scripts/[MODULE]`: [用途]

## Data or File Format Overview

`[FORMAT_NAME]` は、[用途] のために使用する。

主要構造:

- `[FIELD]`: [説明]
- `[FIELD]`: [説明]
- `[FIELD]`: [説明]

Example:

```text
[FORMAT_EXAMPLE]
```

- Current version: `[VERSION]`
- Compatibility policy: [方針]

## Feature Reference

### [FEATURE_NAME]

Implementation:

- Entry point: `[PATH]`
- Core logic: `[PATH]`
- Tests: `[PATH]`

Behavior:

- [主要な動作]
- [境界条件]
- [既知の制約]

機能ごとに同じ形式で追記する。

## Troubleshooting

### Application Does Not Start

確認事項:

1. ランタイムとSDKのバージョン
2. 依存関係のインストール状態
3. ポートまたはプロセスの競合
4. 設定ファイルと環境変数
5. ファイルまたはディレクトリ権限

### Tests Fail

確認事項:

1. 最初に失敗したテストを確認する。
2. 実行環境のバージョンを確認する。
3. テストデータや一時状態を初期化する。
4. 外部サービスや依存プロセスを確認する。
5. 再実行だけで不安定なテストを成功扱いにしない。

### [PROJECT_SPECIFIC_ISSUE]

Symptoms:

- [症状]

Resolution:

```sh
[COMMAND_OR_PROCEDURE]
```
