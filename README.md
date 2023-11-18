## 企画

飲み会などの幹事を任された人のためのオールインワン日程調整ツール

◾️ペルソナ

- 20代の若手会社員
- 会社での飲み会の幹事を任されることになった
- 東京都在住
- 彼女なし
- 幹事経験少なめ
- 仕事のやる気はあるが、ぶっちゃけ幹事は面倒

◾️どのような問題を解決するか

以下のような「不」を解消するWebサービス

- 面倒なスケジュール調整を簡単に
- イベント場所を探す手間を省きたい
- イベントを盛り上げたい

## 要件定義

MVPを定義する

非会員

- イベント主催者は日程の候補を出し、参加者は参加できるかできないかを回答できる
- イベント参加者は候補日程の中から参加可能な日時を選択できる
- 日程の候補日時は追加、編集、削除が可能
- カレンダーによって分かりやすく表示する

会員

- 主催者は日程の候補を出し、参加者は参加できるかできないかを回答できる
- イベント参加者は候補日程の中から参加可能な日時を選択できる
- 日程の候補日時は追加、編集、削除が可能
- カレンダーによって分かりやすく表示する
- 可能であれば、「無難な」お店を紹介できる機能を追加したい
- 日程と場所選びにくじ機能をつける（決まった候補日または候補店の中からくじ引き）

## 技術選定

以下の軸で選定

- 経済的デメリットが少ない
- 普段から使っており、慣れている
- 今回は難しい技術に挑戦するよりは、成功確度が高い技術で

### 使用技術一覧
- データベース
    - MySQL
- プログラミング言語
    - PHP
    - JavaScript
- フレームワーク
    - バックエンド
        - Laravel
    - フロントエンド
        - Nuxt.js(CompositionAPI)
        - TailwindCSS
- ライブラリ
  - 認証
    - Laravel Sanctum
    - Laravel Fortify
- 開発環境
    - Docker
        - PHP8系
        - Apache
- 本番環境
    - Docker
        - 証明書はDocker Imageを使う（SteveLTN/https-portal）
    - AWS
        - シングルAZ構成
        - Webサーバー
            - EC2
        - DBサーバー
            - EC2
- ソースコード管理
    - Git
    - Github
- Git運用
    - Git Flowに従う
- 外部API
    - ホットペッパーグルメAPI

## 設計

### DB設計

[DB設計](db/kanji_db.drawio.png)

### API設計

イベント
- GET /api/events/{event_id}
    - 概要: 特定のイベントを取得する
    - パラメータ: event_id (イベントのID)
    - レスポンス: イベントの詳細情報

- POST /api/events
    - 概要: 新しいイベントを作成する
    - パラメータ: イベントの情報
    - レスポンス: 作成されたイベントの情報

- PUT /api/events/{event_id}
    - 概要: 特定のイベントを更新する
    - パラメータ: event_id (イベントのID), 更新するイベントの情報
    - レスポンス: 更新されたイベントの情報

- DELETE /api/events/{event_id}
    - 概要: 特定のイベントを削除する
    - パラメータ: event_id (イベントのID)
    - レスポンス: 削除されたイベントの情報

候補日
- GET /api/events/{event_id}/candidates
    - 概要: 特定のイベントの候補日を取得する
    - パラメータ: event_id (イベントのID)
    - レスポンス: 候補日のリスト

- GET /api/events/{event_id}/candidates/{candidate_id}
    - 概要: 特定の候補日を取得する
    - パラメータ: event_id (イベントのID), candidate_id (候補日のID)
    - レスポンス: 候補日の詳細情報

- POST /api/events/{event_id}/candidates
    - 概要: 新しい候補日を作成する
    - パラメータ: event_id (イベントのID), 候補日の情報
    - レスポンス: 作成された候補日の情報

- PUT /api/events/{event_id}/candidates/{candidate_id}
    - 概要: 特定の候補日を更新する
    - パラメータ: event_id (イベントのID), candidate_id (候補日のID), 更新する候補日の情報
    - レスポンス: 更新された候補日の情報

- DELETE /api/events/{event_id}/candidates/{candidate_id}
    - 概要: 特定の候補日を削除する
    - パラメータ: event_id (イベントのID), candidate_id (候補日のID)
    - レスポンス: 削除された候補日の情報


参加者
- POST /api/events/{event_id}/users
    - 概要: 特定のイベントに参加者を追加する
    - パラメータ: event_id (イベントのID), 参加者の情報
    - レスポンス: 追加された参加者の情報

- POST /api/events/{event_id}/users/{candidate_id}/attendance
    - 概要: 特定の候補日に対して参加者の出席可否を登録する
    - パラメータ: event_id (イベントのID), candidate_id (候補日のID), 出席可否の情報
    - レスポンス: 出席可否の登録結果

開催場所
- GET /api/events/{event_id}/location
    - 概要: 特定のイベントの開催場所を取得する
    - パラメータ: event_id (イベントのID)
    - レスポンス: 開催場所のリスト

- POST /api/events/{event_id}/location
    - 概要: 特定のイベントに新しい開催場所を登録する
    - パラメータ: event_id (イベントのID), 開催場所の情報
    - レスポンス: 登録された開催場所の情報

- PUT /api/events/{event_id}/locations/{location_id}
    - 概要: 特定のイベントの特定の開催場所を更新する
    - パラメータ: event_id (イベントのID), location_id (開催場所のID), 更新する開催場所の情報
    - レスポンス: 更新された開催場所の情報

- DELETE /api/events/{event_id}/locations/{location_id}
    - 概要: 特定のイベントの特定の開催場所を削除する
    - パラメータ: event_id (イベントのID), location_id (開催場所のID)
    - レスポンス: 削除された開催場所の情報

- POST /api/events/{event_id}/locations/{location_id}/suggestions
    - 概要: 特定のイベントの特定の開催場所に対して候補開催場所を提案する
    - パラメータ: event_id (イベントのID), location_id (開催場所のID)
    - レスポンス: 提案された候補開催場所のリスト

### 画面設計

[Figma](https://www.figma.com/file/yUlIklKrFfNhMBnAx8V1oB/イベント管理ツール?type=design&node-id=0%3A1&mode=design&t=pAPeBW0TGfuVO4gY-1)