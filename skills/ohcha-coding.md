# ohcha-coding

> このスキルの用途:
> 小規模サイト・ LP の HTML/CSS/最小 JS 実装標準スキル（コーダー AI の役割）。
> コーディング・レスポンシブ実装・フォーム等の機能実装・実装レビューの場面で使用する。
> 何を作るか（構成・デザイン）は ohcha-web-design / ohcha-lp-design、
> 全体フローは ohcha-ai-site-building に従い、本スキルは「どう実装するか」だけを規定する。
> 「コーディングして」「HTML/CSS で組んで」「レスポンシブ対応して」
> 「フォームを実装して」「実装ルールは？」「コードをレビューして」などで必ず使用する。


---

# ohcha コーディング標準スキル

## このスキルの役割

小規模サイト・ LP を「軽く・壊れにくく・ AI で更新しやすく」実装するための標準。
設計判断（何を作るか）は上位スキルに従い、本スキルは実装判断（どう作るか）だけを担当する。

## 技術選定の定石（小規模・低コスト・ AI 運用前提）

- **静的 HTML/CSS ＋最小限の JS** を第一選択とする。ビルド工程（npm build 等）を持ち込まない
- フレームワーク・ CSS ライブラリは原則入れない（AI が全文を読める素の CSS が最も保守しやすい）
- JS は「ハンバーガーメニュー・アコーディオン・スムーススクロール」程度の振る舞いに限定。状態管理が要るなら設計を疑う
- CMS 的な更新は「スプレッドシート＋ AI 変換」等の運用側で解決する（ohcha-ai-site-building Phase 3）

## ファイル構成・命名

```
site/
├── index.html
├── {page}.html          … ページは浅いフラット構成
├── assets/
│   ├── css/style.css    … 1 ファイル基本（肥大したら base/layout/components に分割）
│   ├── js/main.js
│   └── img/             … 画像は用途-連番.webp（hero-01.webp）
└── docs/                … 戦略・運用ドキュメント（ai-site-building の標準セット）
```

- クラス名は **BEM ライト**（`block__element--modifier` を厳密にしすぎない範囲で）。`div1` `box2` のような無意味名は禁止
- ページ間で同じ部品（ヘッダ・フッタ・ CTA）は同じクラス名・同じマークアップに統一する（コピペ差異を作らない）

## CSS 設計

- **トンマナシートを CSS カスタムプロパティに変換してから書き始める**（ohcha-tone-manner の決定が唯一の色源）

```css
:root {
  --color-base: #FFFFFF;   /* BASE */
  --color-key:  #2E7D5B;   /* KEY  */
  --color-sub:  #F4E9B8;   /* SUB  */
  --color-font: #222222;   /* FONT（純黒禁止） */
  --font-body: "Noto Sans JP", sans-serif;
  --space-unit: 8px;       /* 余白は 8px の倍数 */
}
```

- 色・フォント・余白のベタ書きを禁止し、必ず変数経由にする（ブランド差し替えが 1 箇所で済む）
- セレクタのネストは 2 段まで。`!important` は原則禁止
- 余白は 8px グリッド（8/16/24/32/48/64…）で統一する

## レスポンシブ実装基準

- **モバイルファースト**：375px 幅を基準に書き、`min-width` のメディアクエリで PC へ拡張する
- ブレークポイントの目安: **768px（タブレット）／1024px（PC）** の 2 つまで。増やさない
- 文字サイズ・余白は `clamp()` で流動化してブレークポイントを減らす（例: `font-size: clamp(16px, 1.6vw, 18px)`）
- 幅は固定 px でなく `max-width` ＋ `width: 100%`。横スクロールが出たら即修正（公開ストップ級）
- 画像は `max-width: 100%; height: auto;` を全体に適用し、`width/height` 属性で CLS を防ぐ
- タップ領域 44px 以上・本文 16px 以上など UI 数値は **ohcha-web-design の数値基準**に従う

## 定番実装パターン

- **ヘッダ**: スマホはハンバーガー（`<button aria-expanded>` で開閉）、PC は横並びナビ。重要導線はハンバーガー内に隠さない
- **フォーム**: `<label>` 必須・`type="email/tel"` で適切なキーボード・`autocomplete` 付与・送信は静的サイトなら Google フォーム埋め込みか Formspree 系で足りる。文言ルールは ohcha-writing のマイクロコピー節
- **OGP ・メタ**: `title`（32 文字目安）・`description`・`og:title/description/image`（1200×630）・ favicon を全ページに。SNS シェアの見え方まで確認
- **アクセシビリティ最低線**: 画像に alt（装飾は alt=""）／見出し階層を飛ばさない／フォーカス可視（`:focus-visible`）／色だけに頼らない
- **構造化データ**: 店舗・事業サイトは JSON-LD（Organization / LocalBusiness）を head に入れる

## パフォーマンス

- 画像は **WebP** 基本・実表示サイズの 2 倍以内にリサイズ・ファーストビュー以外は `loading="lazy"`
- Web フォントは 2 書体まで・`display=swap`。アイコンフォントは使わない（SVG 直置き）
- 目標: PageSpeed Insights **90 点以上**（ohcha-lp-design と共通基準）

## AI への実装指示の型（ディレクター→コーダー間）

実装を AI に依頼するときは、次の 5 点をセットで渡す：
①対象ファイルパス ②何を・どこに（セクション名・クラス名）③デザイン根拠（トンマナ変数・参考スクショ）
④レスポンシブの振る舞い ⑤やらないこと（他セクションに触らない等）。
曖昧な「いい感じに」は禁止。修正依頼のテンプレは ohcha-ai-site-building の修正依頼テンプレを使う。

## 品質ゲート（実装完了の合格基準）

1. 375px ・ 768px ・ 1024px の 3 幅で横スクロール・崩れがないか
2. 色・フォント・余白がすべて CSS 変数（トンマナ）経由か
3. alt ・ label ・見出し階層・フォーカスの最低線を満たすか
4. OGP ・ favicon ・メタが全ページに入っているか
5. PageSpeed Insights 90 点以上か
6. 公開前レビュー（ohcha-site-review）に回したか

## 他スキルとの連携

- 全体フロー・開発ルールの上位: **ohcha-ai-site-building** ／ UI ・数値基準: **ohcha-web-design**
- LP 特有の実装（画像文字と HTML テキストの使い分け等）: **ohcha-lp-design**
- 配色・フォントの決定: **ohcha-tone-manner** ／ 文言: **ohcha-writing**
- 公開作業: **ohcha-deploy** ／ 公開前チェック: **ohcha-site-review**
