# 企画
## 企画概要

飲み会などの幹事を任された人のためのイベント管理ツール。
飲み会や忘年会などのイベントを想定。

## 目的

以下のような「不」を解消するWebサービス

- 面倒なスケジュール調整を簡単に
- イベント場所を探す手間を省きたい
- イベントを盛り上げたい、失敗したくない

## 背景

普段から飲み会などの幹事を行うことが多く、「調整さん」などの既存サービスを利用している。
たしかに、メンバーの日程を調整する際にはかなり便利だった。
しかし、飲み会に選ぶ場所選びをどうしようか毎回悩む。探すのが面倒で、ハズレのない無難なお店を選ぶのに神経を使う。
そこで、日程調整だけではなく、無難なお店選びの提案やイベントのメンバー管理、チャットなどの連絡事項も一括で行えるアプリケーションがあれば非常に使えるなと思い、作ろうとなった。
また、競合サービスでここまで行っているアプリは他にはなく、マーケティング目線である程度アクセスを集められるのではないかと思ったのも理由の一つ。
MVPでは最小構成でリリースしてしまうが、追々機能追加を行っていく予定。

## ペルソナ

- 20代の若手会社員
- 会社での飲み会の幹事を任されることになった
- 東京都在住
- 彼女なし
- 幹事経験少なめ
- 仕事のやる気はあるが、ぶっちゃけ幹事は面倒

## 予算

月額5,000円以内には収めたい

# 要件定義

MVPを定義する

## 非会員

- 幹事
  - イベント情報を入力する
  - 日程の候補を出し、入力する
  - 開催場所を入力する（スキップ可）
  - 必要な情報の入力を完了すると、イベントURLが発行され、これを参加者に配布する
  - イベント情報、日程候補、開催場所は編集できる

- 参加者
  - 幹事が設定した候補日時に○△×で参加可否を入力する
  - 参加可否は入力のし直しが可能

## 会員

非会員の機能に加えて以下の機能を利用できる

- 複数の「無難な」お店の紹介を受けることができる

今後、くじ機能など複数の機能を追加予定

# 技術選定

以下の軸で選定

- 経済的デメリットが少ない
- 普段から使っており、慣れている
- 今回は難しい技術に挑戦するよりは、成功確度が高い技術で

## 使用技術一覧
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

## アーキテクチャ

MVCモデルに加えて、ビジネスロジックを管理するServiceクラス、データベース処理を管理するRepositoryクラスを追加する。

## 設計

### DB設計

[ER図](db/kanji_db.drawio.png)

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