# PROPERTY-CONTENT — 物件ごとに変える場所(文言・素材・色)

生成時にトークン(二重波括弧で囲んだ大文字名)は answers.json から自動置換済み。後から直すときはこの表のファイルを編集する。
直したら `node <skill>/scripts/check.js .` を実行し、`public/sw.js` の `VERSION`(例 `xxx-v1` → `v2`)を上げてキャッシュを更新する。

## 1. 名前・色(トークン由来)
| 内容 | 場所 |
|---|---|
| アプリ名(長) APP_NAME | `public/*.html` の `<title>` / `<a class="logo" aria-label>`、`public/manifest.webmanifest` の name、`public/app.js`(タイトル末尾の置換)、`public/sw.js` コメント、`scripts/build-pages.js`(404ページ)、`public/tool/index.html` |
| 短い名前 APP_SHORT_NAME(ホーム画面のアイコン下) | `public/manifest.webmanifest` short_name、各HTMLの `apple-mobile-web-app-title`、`public/app.js`(インストール手順の図・通知テスト)、`public/sw.js`(通知の既定タイトル)、`public/admin/*` |
| 物件名 PROPERTY_NAME / 英語名 PROPERTY_NAME_EN | 画面上の物件名は Firestore `property/main`(管理画面「物件」で変更可。初期値は `scripts/lib/seed-data.js`)。HTMLの `.pname`/`#footName` は読み込み後に上書きされる。`public/tool/tool.js` `T.prop`、`public/tool/index.html`、`public/tool/mod-media.js`(パノラマの文字) |
| タグライン PROPERTY_TAGLINE | `scripts/lib/seed-data.js` と `lib/db.js`(ローカル用)の `property.tagline` → Firestore `property/main.tagline` |
| エリア名 AREA_NAME | トップのコピー、seedの「周辺環境ガイド」、営業ツールのサンプル文 |
| テーマ色 THEME_COLOR / 背景色 BACKGROUND_COLOR | `public/manifest.webmanifest`(theme_color / background_color)、`public/index.html` `register.html` `install.html` の `<meta name="theme-color">`。画面の配色(赤 #c00000 / 青 #0b3d91)は `public/app.css` 冒頭の CSS 変数(全面変更はしない方針) |
| キャッシュ名 CACHE_PREFIX | `public/sw.js` の `VERSION`、`package.json` の name |
| GitHub / Firebase の値 | `.firebaserc`(Firebaseプロジェクト)、`DEPLOY-free.md` `README.md`(手順書の値)。**ビルド時の Firebase設定・管理者メールは GitHub リポジトリ変数 `FIREBASE_CONFIG` `ADMIN_EMAILS` から注入(コードに書かない)** |

## 2. 物件固有の文言(1か所: `property-content.json`)
`property-content.json` の `tokens` が既定値(生成時に `public/index.html` へ埋め込み)。生成後は **`public/index.html` を直接編集**する。
トップページ構成: HERO(物件名・タグライン) → CONCEPT(見出し・本文・特徴3つ) → DESIGN → LOCATION(見出し・写真3枚+キャプション) → PLAN(間取り図) → MEMBERS MERIT(4枚) → OUTLINE(物件概要表。物件名・タグラインはFirestoreから表示) → 会員CTA。
| トークン | 場所 |
|---|---|
| HOME_HERO_EYEBROW / HOME_CONCEPT_HEADING_HTML / HOME_CONCEPT_LEAD_HTML / HOME_FEAT1_* / HOME_FEAT2_* | `public/index.html` #concept, hero |
| HOME_DESIGN_HEADING_HTML | `public/index.html` #design |
| HOME_LOCATION_HEADING_HTML / HOME_LOC_ALT_* / HOME_LOC_CAP_* | `public/index.html` #location |
| HOME_MERIT4_BODY | `public/index.html` #merit(4枚目) |
| 物件概要表の固定行(公開状況・お問合せ) | `public/index.html` OUTLINE |
| 会員登録フォームの文言・同意文 | `public/register.html` |
| インストール手順(iOS/Android/PC)の文言と図 | `public/app.js`(`ART` / 手順配列)、`public/install.html` |

## 3. ライブラリ(会員限定メニュー)の初期データ
`scripts/lib/seed-data.js`(Firebase用。`seed-firestore.js` が投入)と `lib/db.js`(ローカル `npm start` 用)の **2つを同じ内容に保つ**。
タグ・コンテンツ(フォルダ/CGギャラリー/VR/PDF/動画/リンク)・Push例・デモ顧客。本番投入は `node seed-firestore.js --no-demo`。
VR/PDF/リンクの URL は `https://example.com/...` のダミー。**本番は管理画面「ライブラリ」で実URLに差し替える**(seedを直すより管理画面が安全。Firestoreに投入済みのseedは再実行しても上書きされない。`--force` は管理画面の編集を消す)。
CGギャラリー(`gallery`)の7枚は `scripts/lib/seed-data.js` の `gallery`(画像スロットのファイル名を参照)。

## 4. 営業ツール `/tool/`(任意機能・サンプル内容)
`public/tool/` は営業担当向けのサンプル(ブック・スライド・周辺マップ・パノラマ等)。文面はすべて **サンプル** で、物件固有ではない一般的な表現に置換済み。
本番で使うなら編集: `mod-books.js`(パンフレット/Quality Book/スライドの文章・諸元)、`mod-misc.js`(周辺施設とマップ、資金計画の初期値)、`mod-media.js`(ギャラリー表題・パノラマ方位ラベル)、`mod-plans.js`(間取り)、`tool.js`(`T.prop` 物件名)。使わないなら触らなくてよい(顧客アプリからは別URL)。

## 5. 画像・動画スロット(ファイル名は固定。同名で差し替える)
テンプレートには ES CON ロゴ以外の素材を同梱していない。**無いと画像が欠ける(動画は任意)。** `common_logo.svg` は生成時に物件名の仮ロゴ(白文字SVG)を自動作成。
置き場所 `public/assets/img/`(画像)、`public/assets/video/`(動画)。サイズは推奨(元実装の実寸)。1ファイル100MB未満(GitHub制限)、画像は圧縮して 1MB 前後目安(トップMVは最大でも約3MB)。

| ファイル | 用途 / 使用箇所 | 推奨サイズ | 必須 |
|---|---|---|---|
| `common_escon.svg` | ES CON ロゴ(同梱済・共通) 全ページ・管理画面 | SVG | 同梱 |
| `common_logo.svg` | 物件ロゴ(白・透過のSVG)。トップのヒーロー、営業ツール、`sw.js` | SVG(横長) | 自動仮作成 |
| `top_mv.jpg` | PC用ヒーロー画像・動画ポスター・管理画面/営業ツールのパノラマ元・seed `heroImage` | 2880x1620(最低1920x1080) | 必須 |
| `top_mv_sp.jpg` | スマホ用ヒーロー(縦長) seed `heroImageSp` | 1046x1722 (約9:16) | 必須 |
| `og_img.jpg` | SNS共有画像 `og:image`(ビルド時に絶対URL化) | 1200x630 | 推奨 |
| `top_concept.jpg` | CONCEPT 外観CG / Pushプレビュー / seedサムネ | 1965x1328 (3:2) | 必須 |
| `top_design_1.png` | DESIGN エントランスホール(営業ツールでも使用) | 698x335 以上 (約2:1) | 必須 |
| `top_design_2.png` | DESIGN エントランス外観 | 1436x869 (約16:10) | 必須 |
| `top_loca_1.jpg` | LOCATION メイン写真(縦長) | 803x1295 | 必須 |
| `top_access_2.jpg` | LOCATION サブ1(キャプション=HOME_LOC_CAP_1) | 425x285 以上 (3:2) | 必須 |
| `top_access_3.jpg` | LOCATION サブ2(キャプション=HOME_LOC_CAP_2) | 594x410 以上 (約3:2) | 必須 |
| `top_plan_1.png` | PLAN 間取り図 / 営業ツール | 824x875 以上 | 必須 |
| `top_entry_merit_2.jpg` | MERIT カード1 / ギャラリー「リビング」 | 780x436 (16:9。元は390x218) | 必須 |
| `top_entry_merit_3.jpg` | MERIT カード2 / ギャラリー「エクステリア」 | 780x436 | 必須 |
| `top_entry_merit_4.jpg` | MERIT カード3 | 780x436 | 必須 |
| `top_entry_merit_6.jpg` | MERIT カード4 / ギャラリー「キッチン」 | 780x436 | 必須 |
| `common_nav_img.jpg` | 営業ツールのみ: 夕景ワイド/ブック挿絵 | 501x815 (縦長) | 任意 |
| `common_moments.jpg` | 営業ツールのみ: ギャラリーのバナー(濃色背景に contain) | 1232x280 | 任意 |
| `../video/top.mp4` | PC用ヒーロー動画(音なし・ループ・幅761px以上のみ読み込み) | 1920x1080 / 〜15MB / 30秒前後 | 任意 |
| `../video/top_sp.mp4` | 物件紹介ムービー(seedの「物件紹介ムービー」、videoの代替) | 1080x1080 / 〜15MB | 推奨 |
| `../video/top_access.mp4` | アクセス/周辺ムービー(seedと営業ツール) | 1920x1080 / 〜12MB | 任意 |

アイコン(`public/icons/`)は `node <skill>/scripts/make-icons.js <1024px正方形PNG> --out public/icons` で一括生成(master / 192 / 512 / maskable-512 / apple-touch 180 / favicon 32)。同梱は ES CON のロゴ入り既定アイコン。物件ごとに差し替える。
スロットを増やす/名前を変える場合: HTML/JS の参照元と `public/sw.js` の SHELL(オフラインキャッシュ対象)も更新する。
