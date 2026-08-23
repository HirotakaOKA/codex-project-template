# Implementation Instructions

`docs/plans.md` に定義された文書整備マイルストーンを、順番に実施して検証する。

## Non-negotiable Constraint

- マイルストーン完了ごとの確認待ちで停止しない。
- 実装、テスト、文書更新を一体の作業として扱う。
- 成功していない作業を完了扱いにしない。
- ユーザーの既存変更を無断で破棄しない。

## Execution Rules

- `docs/plans.md` を文書整備作業のローカル計画として扱う。
- 一度に一つのマイルストーンへ集中する。
- 変更は小さく、レビュー可能な単位に分ける。
- 無関係な修正やリファクタリングを混在させない。
- 曖昧な点は、ユーザー指示、受入条件、テスト、既存実装の順で解決する。
- 合理的に判断できる場合は、判断を `docs/plans.md` に記録して継続する。

各マイルストーンで次を実行する。

1. Scope、対象外、Acceptance criteriaを確認する。
2. 関連コード、テスト、設定、依存関係を調査する。
3. 必要なテストを追加または更新する。
4. 最小の一貫した単位で実装する。
5. 指定された検証を実行する。
6. 失敗を修正して再検証する。
7. `docs/plans.md` と `docs/documentation.md` を更新する。
8. 次の未完了マイルストーンへ進む。

バグを発見した場合は、可能な限り次の順で対応する。

1. 再現条件を特定する。
2. 失敗するテストを追加する。
3. 原因を修正する。
4. テストと関連する回帰確認を実行する。
5. 後続作業へ影響する内容だけを `docs/plans.md` に記録する。

## Validation Requirements

マイルストーンに応じて、次を実行する。

- Build
- Lintまたは静的解析
- Typecheck
- Unit tests
- IntegrationまたはEnd-to-End tests
- Snapshotまたは決定性テスト
- Manual verification

検証失敗を残したまま次へ進まない。

次の方法で成功扱いにしてはならない。

- 失敗テストの削除や無効化
- アサーションの不当な弱体化
- エラーの握り潰し
- 内容を確認しないスナップショット更新
- 再実行で偶然成功した不安定テストの放置

## Documentation Requirements

`docs/documentation.md` には、現在実際に動作する内容だけを記載する。

実装に応じて次を更新する。

- プロジェクト概要と対応環境
- セットアップ、起動、ビルド、テスト方法
- 主要な利用手順
- リポジトリ構成
- データ形式または公開インターフェース
- 主要機能の仕様と制約
- トラブルシューティング

詳細な進捗、長いコマンド出力、一時的な試行錯誤は記載しない。

## Completion Criteria

次をすべて満たした場合のみ完了とする。

- `docs/plans.md` の全マイルストーンが完了している。
- すべてのAcceptance criteriaを満たしている。
- 必須のビルド、静的解析、型検査、テストが成功している。
- 必要な手動確認が完了している。
- 重大な未解決障害がない。
- `docs/documentation.md` が実装内容と一致している。
- 一時ファイル、デバッグコード、機密情報が残っていない。
- Git作業ツリーが意図した状態になっている。

## Local Plan Finalization

文書整備の最終段で、`.gitignore` に `docs/plans.md` だけを追加する。`docs/prompt.md`、`docs/implement.md`、`docs/documentation.md` は追跡対象のままとし、ignoreしない。

`docs/plans.md` が追跡済みの場合は、内容を失わないよう物理ファイルを残したまま次を実行する。

```sh
git rm --cached -- docs/plans.md
```

続けて `git check-ignore -v docs/plans.md` でignore規則を確認する。`docs/plans.md` はローカル作業計画であり、利用者向け仕様や恒久的な履歴の正本として扱わない。

完了時は、実装内容、検証結果、主要判断、既知の制約、主要変更ファイル、Git状態を報告する。

最初に `docs/plans.md` を読み、最初の未完了マイルストーンから開始する。
