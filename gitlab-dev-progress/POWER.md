---
name: "gitlab-dev-progress"
displayName: "GitLab Dev Progress Checker"
description: "チームのfeatureブランチ一覧をGitLabから取得し、各ブランチの開発フロー進捗を確認するPower。どのブランチがどの工程にいるかをまとめて把握できる。ブランチの進捗、開発フロー、MR状況、feature一覧、滞留ブランチの確認など、チームの開発状況を把握したい場合は必ずこのPowerを使うこと。"
keywords: ["gitlab", "branch", "progress", "feature", "開発フロー", "進捗", "merge-request"]
author: "team"
---

# GitLab Dev Progress Checker

## Overview

チームの全featureブランチを対象に、開発フローの各工程がどこまで完了しているかを一覧で確認するPowerです。

`develop` ブランチをベースとして、`feature/` から始まるブランチの進捗を追跡します。glabコマンド（GitLab CLI）とgitコマンドを組み合わせてリモートの状態を取得し、各ブランチが開発フローのどのステップにいるかをKiroが判定して報告します。

チームの開発状況を俯瞰したいとき、どのブランチが滞留しているか確認したいとき、MRの作成漏れを検出したいときに活用してください。

## Available Steering Files

- **checklist** - 各ステップの完了判定ロジックと進捗レポートの生成手順

## 開発フロー定義

このPowerが追跡する開発フローの工程は以下の通りです（仮定義）。チームの手順書が整備されたら更新してください。

| ステップ | 工程名 | 完了の判断基準 |
|---------|--------|--------------|
| Step 1 | ブランチ作成 | `feature/` ブランチが `develop` から分岐して存在する |
| Step 2 | 実装中 | ブランチにコミットが積まれている（developより先行） |
| Step 3 | リモートpush済み | ブランチがリモート（origin）に存在する |
| Step 4 | MR作成済み | GitLab上にMerge Requestが存在する（Open状態） |
| Step 5 | マージ済み | MRがマージされ、developに取り込まれた |

> ⚠️ この開発フロー定義は仮のものです。チームの手順書が整備されたら、このファイルのステップ定義を更新してください。

## 使い方

### チーム全体の進捗確認

```
チームのfeatureブランチの進捗を確認して
```

```
feature/ブランチ一覧と開発フローの進捗を教えて
```

```
どのブランチが滞留しているか確認して
```

### 特定ブランチの確認

```
feature/xxx ブランチの進捗を確認して
```

### 出力例

```
## featureブランチ 進捗サマリー（2026-05-17時点）

| ブランチ名 | 現在のステップ | 工程名 | 備考 |
|-----------|-------------|--------|------|
| feature/login | Step 4 | MR作成済み | MR #12 Open中 |
| feature/payment | Step 3 | リモートpush済み | MRなし ⚠️ |
| feature/dashboard | Step 5 | マージ済み | develop取込済み |
| feature/search | Step 2 | 実装中 | コミット3件 |

⚠️ 要確認: feature/payment（push済みだがMRなし、最終コミット: 5日前）
```

## 注意事項

- glabコマンドはGitLabへの認証が必要です。未認証の場合は `glab auth login` を実行してください。
- プライベートリポジトリの場合、適切なアクセストークンが設定されている必要があります。
- ブランチ数が多い場合、取得に時間がかかることがあります。

## 開発フロー定義の更新方法

チームの手順書が整備されたら、このPowerの「開発フロー定義」セクションのステップ表と `steering/checklist.md` の判定ロジックを更新してください。
