# エスコン プライムサイト 西麻布 (（仮称）港区西麻布2丁目プロジェクト 会員限定PWA)

ES CON 物件向け「会員登録 → ホーム画面追加 → 限定情報 / Push通知 → 管理画面」PWA。
本番構成: **GitHub Pages(静的フロント) + Firebase Spark(Firestore + Auth, 無料) + GitHub Actions(Push送信)**。サーバー不要。
公開手順は `DEPLOY-free.md`、画面・文言・素材の変更箇所は `PROPERTY-CONTENT.md`、仕様は `SPEC.md` / `FIREBASE-CLIENT.md`。

## ローカル確認(Express版・Firebase不要)
    npm install && npm start      # http://localhost:3000  (PORT で変更)

- 顧客PWA: `/`  管理画面: `/admin/`  営業ツール: `/tool/`
- 管理パスワード: `admin` (環境変数 `ADMIN_PASSWORD` で上書き)
- デモ顧客(山田 太郎) トークン: `demo-token-gentei-0001`
  - ブラウザで `localStorage.setItem('aplika.token','demo-token-gentei-0001')`
- データ: `data/db.json` (初回起動で自動シード。**コミット禁止**。削除すると再シード)

## 本番ビルド(GitHub Actions が自動実行)
    node scripts/build-pages.js --mode firebase --base /escon-prime-nishiazabu-2/    # env: FIREBASE_CONFIG, ADMIN_EMAILS
    node scripts/make-rules.js escon_prime_nishiazabu@ikiiki.com                            # firestore.rules 再生成
    cd scripts && npm install && npm test                                   # Push送信ロジックのテスト(ネットワーク不要)
    node scripts/test-firebase-api.js                                       # Firestoreシム(偽SDK)のテスト

## 管理者
escon_prime_nishiazabu@ikiiki.com  (Firebase Auth にメール/パスワードのユーザーを手動で追加すること)
