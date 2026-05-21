# 統合支払明細ビューア

新聞シフト管理 / アスクル管理 / デリバリー管理 の 3 ツールに横断して支払明細を閲覧できる単一サイト。

## 使い方

1. NexPort と同じメール・パスワードでログイン
2. 初回のみ電話番号を登録 (3ツールに同じ番号が登録されている必要あり)
3. 月を選んで読み込み → 3ツール並列で取得して表示

## 構成

- 静的 HTML 1 枚 (GitHub Pages 配信)
- NexPort Supabase の `profiles.phone` を ID とする
- 各ツールの Supabase Edge Function `pay-statement-by-phone` を並列に叩く

| ツール | プロジェクト ID | EF |
|--|--|--|
| 新聞 (shift-manager) | nccognptoprhwsbjnwcu | pay-statement-by-phone |
| アスクル (askul-manager) | erfcsnzdooswgpvgrapb | pay-statement-by-phone |
| デリバリー (delivery-manager) | ymdaakiwxkjotlmzveom | pay-statement-by-phone |

## 認証

- ビューアサイト自身は NexPort Supabase の `signInWithPassword` を使用
- 各ツール EF は anon キーで叩く (verify_jwt=true)
- 認可は「電話番号を知っている人=本人」というソフトな前提
- 管理者 (`role=admin` または `super_admin`) は任意の電話番号で他人の明細も検索可能

## 管理者運用

- 各ツールの管理画面で「確定保存」ボタンを押すと closed_*_statements テーブルに保存
- アスクル: 既存の閉じ込み機能 (`closed_payment_statements`)
- 新聞: 支払明細画面の「📤 統合明細ビューアに公開」バー (`closed_pay_statements`)
- デリバリー: monthly_payments テーブル (未確定なら live 集計)
