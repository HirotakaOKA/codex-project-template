# Git作業管理ルール

## 1. 目的

- Git履歴を、変更理由・検証結果・レビュー経緯を追跡できる状態に保つ。
- 人間とAIエージェントが、同じリポジトリで安全に並行作業できるようにする。
- 速さよりも、変更範囲の明確さ、再現性、レビュー可能性を優先する。

## 2. 基本原則

- 1タスク、1ブランチを基本とする。PRはremoteとレビュー経路がある場合に使用する成果物であり、ローカル統合ではcommit SHAによるhandoffを使用する。
- 無関係な変更を同じブランチやPull Requestへ混在させない。
- 作業開始前に、目的、対象範囲、完了条件、実行する検証を確認する。
- 既存の設計、命名、テスト、Git運用を調査してから変更する。
- 要求されていない整理、改名、依存関係更新、全体リファクタリングを行わない。
- AIの生成結果は提案として扱い、人間が差分と検証結果を確認する。
- Worktreeは作業場所、Branchは変更履歴と統合単位、Codex Sessionは作業者、Integration Targetは人間またはCoordinatorが宣言する統合先である。Worktreeやフォルダを統合しない。

## 3. タスク宣言と作業開始前確認

並列化するTaskは、作業開始前に計画書または同等のTask記録へ次を明記する。

```text
Task / Role (Worker, Reviewer, Integrator) / Owner or Session
Source Branch / Worktree / Base Branch / Base SHA / Integration Target
Dependencies / In scope / Out of scope / Shared resources
Commit allowed / Merge allowed / Push allowed / Cleanup allowed
Validation / Completion condition
```

- Base Branchは作業開始時の基点、Integration Targetは成果を戻す予定のbranchであり、同じ意味として扱わない。GitやCodexに統合先を推測させない。
- `git rev-parse --show-toplevel`、`git branch --show-current`、`git status --porcelain`、必要に応じて `git worktree list` を確認して、作業場所、branch、汚れ、他worktreeを把握する。
- `git branch --show-current` が空のdetached HEADでは、Integratorまたは明示権限を持つCoordinatorが名前付き専用branchを確保するまで、編集、commit、handoffを行わない。孤立commitを成果物として扱わない。
- dirty worktreeは所有者と目的を確認するまで上書き、stash、reset、削除しない。
- remoteがある場合も、無条件のpullやrebaseを行わない。base branchとbase SHA、許可された同期方針で最新性を確認する。

## 4. 作業開始

- 作業前に `git status` を確認する。
- 未コミット変更がある場合、所有者と目的を確認し、勝手に破棄・上書きしない。
- デフォルトブランチから直接作業しない。
- 宣言したBase BranchとBase SHAから作業branchを作成する。
- ブランチ名は目的が分かる名前にする。
  - `feature/<概要>`
  - `fix/<概要>`
  - `refactor/<概要>`
  - `docs/<概要>`
  - `test/<概要>`
  - `chore/<概要>`
- IssueやタスクIDがある場合は、ブランチ名またはPRへ記載する。

## 5. 並行作業

- 複数タスクを同時に進める場合は、タスクごとにブランチを分ける。
- 並列Workerは、専用の名前付きbranchと専用worktreeを使用する。1 worktree = 1 branch = 1責務は目標状態であり、Codexのdetached sessionをそのまま成果物にしない。
- 同じブランチを複数の作業者やエージェントから同時編集しない。
- 共有の計画書・共通文書は所有者を1人にする。他Workerはhandoffに追記候補、commit SHA、検証結果を返し、同じファイルを同時更新しない。
- 他者の未コミット変更、ブランチ、worktreeを削除しない。
- 並行タスク間の依存関係、統合順、共通ファイルはTask記録へ明記する。DB、実データ、固定port、共通cache、依存定義、migration、entry pointなどの共有資源は先行直列化または所有者1人とする。
- 同一local repositoryを共有するworktree間のhandoffにpush/pullやファイルコピーを使用しない。commit済みbranchをmergeまたはcherry-pickする。remoteとのpublish/syncにはpush/pullが必要になり得る。

## 6. Worker、Reviewer、Integrator

### Worker

- 宣言された自worktree・source branch・対象範囲だけを変更し、検証、`git diff`、`git diff --staged`、status確認を行う。
- commitはTaskで許可された場合だけ行う。許可時のhandoffは、source branch、commit SHA、`Integration Target...source` の差分、実施・未実施の検証、残存リスク、clean statusを含める。
- merge、push、rebase、reset、amend、他worktree操作、cleanup、worktree間のファイルコピーを行わない。
- commitが未許可なら、未commit差分は統合可能な成果物ではない。差分と検証結果をownerへ報告し、ownerまたはIntegratorがcommitする。

### Reviewer

- sourceのhandoff SHA、commit履歴、`Integration Target...source` の差分、Task範囲、検証、共有資源への影響を確認する。
- 修正は原則source branchへ返す。ReviewerはIntegration Targetを直接変更しない。
- PRがないローカル運用でもレビューを省略しない。

### Integrator

- Integration Targetをcheckoutした統合用worktreeからだけmergeする。Integration Targetごとにactive Integratorを1人にし、sourceを直列に扱う。
- 各merge直前にtarget branch、target HEAD、clean status、source handoff SHA、`target...source` の差分と履歴、Task範囲、検証結果を再確認する。
- merge後はtarget上で必要な統合検証と`git status`を完了し、失敗時は次のsourceを扱わない。
- 子Taskは親feature branchへ先に統合する。調査branchは採用commit、資料、非採用理由をhandoffし、Integratorがmerge、cherry-pick、保留、破棄を決定する。

## 7. 変更方針

- 最小の変更で目的を達成する。
- 既存コードの意図を維持し、変更理由を説明できる状態にする。
- 生成物、秘密情報、個人設定、一時ファイルをコミットしない。
- `.gitignore` を確認し、不足があれば理由を明示して更新する。
- 外部依存関係の追加・更新は、必要性、代替案、影響範囲を確認する。
- 公開API、設定形式、DBスキーマを変更する場合は互換性を確認する。
- 仕様変更を伴う場合は、コードだけでなく関連文書とテストも更新する。

## 8. コミット

- コミットは、独立して理解・検証・取り消しできる単位にする。
- 1コミットへ複数の無関係な目的を含めない。
- 作業途中の退避コミットと、レビュー対象の完成コミットを区別する。
- コミット前に `git diff` と `git diff --staged` を確認する。
- 意図しないファイル、デバッグコード、機密情報がないことを確認する。
- コミットメッセージは「何をしたか」より「何を、なぜ変えたか」が分かる内容にする。
- プロジェクトで規約がある場合は、その形式を優先する。
- 規約がない場合は、次の形式を推奨する。
  - `<type>(<scope>): <summary>`
  - 例: `fix(auth): reject expired refresh tokens`
- 主なtypeは `feat`、`fix`、`refactor`、`test`、`docs`、`chore` とする。
- 既に共有されたコミットのamendや履歴改変は、明示的な許可なしに行わない。

## 9. 禁止操作

- デフォルトブランチへ直接pushしない。
- 明示的な許可なく `git push --force` を使用しない。
- 明示的な許可なく `git reset --hard`、`git clean -fd` を使用しない。
- 他者が作成したコミットを勝手にrebase、squash、amendしない。
- テスト失敗を隠すために、テスト削除、skip追加、検証条件の緩和を行わない。
- CIや保護ルールを回避してマージしない。
- 認証情報、秘密鍵、トークン、接続文字列をコミットしない。

## 10. 検証

- 変更箇所に最も近いテストから実行する。
- その後、影響範囲に応じてビルド、lint、型検査、関連テストを実行する。
- バグ修正では、可能な限り不具合を再現する回帰テストを追加する。
- テストを実行できない場合は、理由と未検証範囲を記録する。
- テスト失敗が変更前から存在する場合は、既存問題である証拠を残す。
- 検証コマンドと結果をPRまたはlocal handoffへ記載する。
- 「成功したはず」ではなく、実行した事実と結果を報告する。

## 11. Pull Request

- PRはremoteとレビュー経路が利用可能な場合に使用する。remote未構成のlocal運用では、同じ情報をcommit SHAを含むhandoffへ記録する。
- PRは小さく保ち、単一の目的に集中させる。
- 未完成でも早期レビューが必要な場合はDraft PRを使用する。
- PR本文には次を記載する。
  - 背景と解決する問題
  - 変更内容
  - 変更しなかった範囲
  - 検証方法と結果
  - 影響、リスク、互換性
  - 関連Issue、仕様書、先行・後続PR
  - レビューで重点確認してほしい箇所
- UI変更では、必要に応じて画像や操作手順を添付する。
- 大きなPRになった場合は、機能、リファクタリング、テストなどに分割する。
- AIがPRを生成した場合も、送信前に人間が内容を確認する。

## 12. レビュー対応

- レビューでは正しさ、セキュリティ、性能、可読性、保守性、テストを確認する。
- AIレビューは補助であり、必須テスト、ブランチ保護、人間の承認を代替しない。
- 指摘への修正は、原則として同じブランチへ追加コミットする。
- 指摘を採用しない場合は、理由と根拠を返信する。
- すべての会話と必須チェックを解決してからマージする。
- 自分の変更を自分だけで承認して完結させない。

## 13. Conflict・Rebase・Merge

- コンフリクト解消前に、双方の変更意図を確認する。
- 機械的に片方を採用せず、解消後に関連テストを再実行する。
- mergeはIntegration TargetをcheckoutしたIntegratorが行う。標準方式はmergeとし、squashやfast-forwardはリポジトリまたはPRの明示方針がある場合だけ使用する。
- rebaseは共有されていないlocal branchで、明示方針がある場合だけ使用する。共有branchのrebaseとforce pushは関係者の合意を得る。
- 共有ブランチの履歴を書き換える場合は、関係者の合意を得る。
- worktreeは作業場所であり、mergeする対象はbranchである。
- cleanupはWorkerが行わない。Integratorまたは人間は、source commitがtargetから到達可能、統合検証成功、source worktreeがclean、session終了済みを確認後、`git worktree remove`、非強制の`git branch -d`の順で実施する。未統合branch、調査branch、所有者不明worktreeを自動削除しない。

## 14. 完了条件

- 要求された変更だけが含まれている。
- 差分を確認し、意図しない変更がない。
- 必要なビルド、テスト、lint、型検査が完了している。
- 関連文書とテストが更新されている。
- PRまたはlocal handoffに変更内容、検証結果、残課題、リスク、source commit SHAが記載されている。
- 利用可能な必須レビューとCIを通過している。local統合ではIntegratorの統合レビューと検証を完了している。
- 未解決事項がある場合は、完了扱いにせず明示する。

## 15. AIエージェントの報告

- 作業終了時に、変更ファイル、主な差分、検証結果を報告する。
- 実行していないテストや確認できなかった事項を明示する。
- 推測と確認済みの事実を区別する。
- コミット、push、PR作成、履歴改変は、権限と依頼範囲を確認して実行する。
- 判断に迷う破壊的操作は停止し、安全な代替案を提示する。
