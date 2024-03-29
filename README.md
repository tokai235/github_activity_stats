# GitHub Activity Stats
GitHub の統計情報を取得する Google Apps Script 群
(GitLab も対応しました)

## 取得できる情報
- マージされた Pull Request
  - 直近 100 件を取得

## 処理の流れ
1. GitHub API or Gitlab API で指定したリポジトリの merged pull request を取得
2. 取得した情報をスプレッドシートに書き込み
3. (スプレッドシート側でデータをグラフ化)
4. 指定したグラフを画像にして slack に post

## 前提とするスプレッドシートの形式について
- `pullsRawData`という名前のシートがあること
  - ここに merged pull request の情報が書き込まれる
- 1行目はヘッダー想定で、書き込みは2行目から
- ヘッダーは以下を想定
- reviewers はカンマ区切りのテキスト
  - 作者はスプレッドシート側で split して利用している

| \ | A | B | C | D | E | F |
| -- | -- | -- | -- | -- | -- | -- |
| 1 | mergedAt | repoName | id | title | reviewers | author |

- 2つのグラフがある
  1. 週ごとの Pull Request 合計数
  2. 週ごとの Reviewer, Author の合計数

## スクリプトプロパティに必要な秘匿情報
- **githubIdTokenBase64**
  - GitHub の `user:pass` を base64 にエンコードしたもの
- **slackToken**
  - slack に post するために slack bot を使用する前提だが、その bot の `bot tokens`
  - see: https://api.slack.com/authentication/token-types#bot
- **slackChannelId**
  - post 先の slack channel id
- **gitlabPAT**
  - Gitlab の Project Access Token
    - 自分は 1 Project しか必要なかったので Project Access Token にした
    - 複数 Project から取得したければ Group を作ってそこから取得できそう
  - Role: `Reporter`, Scopes: `read_api` で作成
