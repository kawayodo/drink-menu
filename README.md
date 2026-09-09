# drink-menu

川淀のドリンクメニュー。ビルド不要のバニラ HTML/CSS/JS 単一ページアプリで、GitHub Pages でそのまま配信している。

公開URL: https://kawayodo.github.io/drink-menu/

## ファイル構成

```
index.html   マークアップ + 全ロジック(インラインscript) + ドリンクデータ
style.css    スタイル一式
assets/logo.png  ロゴ画像
QR_334749.png    案内QRコード(未コミット/gitignore対象にするか要検討)
```

ビルドツール・パッケージマネージャ・外部依存は無し。`index.html`を直接ブラウザで開けば動く。

## 画面遷移

`.screen`クラスを持つ4つの`<div>`を`.active`クラスの付け外しで切り替える(SPA的だがルーターは無い、`showScreen(id)`関数のみ)。

1. `age-gate` — 年齢確認。20歳未満を選ぶとソフトドリンクのみ表示するモードになる
2. `menu-screen` — カテゴリ別メニュー一覧、カート個数バッジ
3. `order-screen` — 客側の注文内容確認・数量変更
4. `staff-screen` — 店員に見せる用の画面。常に日本語固定・価格非表示

状態は`sessionStorage`(`kawayodo_age_confirmed`, `kawayodo_lang`)とページ内メモリ(`cart`, `itemState`)のみ。サーバー通信・永続化は無く、リロードすると注文内容は消える。

## 多言語対応

`LANGUAGES`(en/ja/ko/zhHans/zhHant)を軸に、UI文言は`UI_TEXT`、カテゴリ名は`CATEGORIES`、各ドリンクの`name`/`desc`は`DRINKS`配列の各要素に言語別オブジェクトとして直接持たせている。翻訳はビルド時ではなく実行時に`currentLang`で出し分け(i18nライブラリ不使用)。

店員画面(`renderStaff`)だけは言語切り替えの影響を受けず、`slipName`(伝票名, 日本語)を使う。

## ドリンクデータ (`DRINKS`配列)

メニューの内容はすべて`index.html`内の`DRINKS`配列にハードコードされている。**データの正本は`川淀_ドリンクメニュー.xlsx`(リポジトリ外、親ディレクトリ)** — Excelを更新したら手動でこの配列に反映する必要がある(自動同期の仕組みは無い)。

各要素の主なフィールド:

- `category` — `CATEGORIES`のキー(wine/sparkling/sake/reishu/beer/shochu/soft)
- `slipName` — 店員画面用の伝票表記(日本語のみ)
- `requiresGlassCount` — trueなら「本数+グラス数(または徳利数+お猪口数)」の2軸入力、falseなら単純な個数入力
- `vesselType` — `"bottle"`(本/グラス)か`"tokkuri"`(徳利/お猪口)。`VESSEL_LABELS`で単位表記を切り替える
- `servingStyles` — 飲み方選択肢(熱燗/ロック/水割り等)。`SAKE_STYLES`/`SHOCHU_STYLES`/`UMESHU_STYLES`として定義済みの配列を再利用

カートのキー(`cartKey`)は`id + 本数 + グラス数 + 飲み方`の組み合わせで生成しており、同じドリンクでも本数/飲み方が違えばカート内で別行になる。


## デプロイ

GitHub Pagesが`main`ブランチのルートから直接配信している(ビルドステップ・GitHub Actions無し)。`main`にpushすれば数分でそのまま反映される。
