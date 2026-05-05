# Roll A Ball 新人研修資料

Unityプロジェクト `RollABallTemplate` を6回 x 95分で段階的に完成させるための講習資料です。

## 構成

- `decks/`: PowerPoint本体。各回14枚、講師ノート入り。
- `notes/`: PPTXノートと同内容を確認しやすくしたMarkdown講師ノート。
- `source/coverage.md`: スクリプト・関数・主要フィールドの網羅表。
- `source/deck-data.json`: 生成に使ったスライド構成データ。
- `source/screenshot-manifest.json`: PPTXに取り込んだスクリーンショット一覧。
- `source/pptx-validation.json`: PPTX内部検証結果。
- `source/qa-report.md`: 生成物の確認結果と未確認事項。
- `previews/`: スライド構成確認用のコンタクトシート。
- `videos/README.md`: 動画素材を使わない方針のメモ。

## 進め方

1. 第1回で完成形を見せ、すぐに最小のプレイヤー移動へ戻る。
2. 第2回以降は、カメラ、アイテム、クリア、タイマー、カウントダウン、タイトル画面の順に肉付けする。
3. 演習時間は設けず、講師が実装しながらコード抜粋と画面変化を説明する。

## 注意

PPTXにはスクリーンショット画像とコード抜粋を直接配置しています。PowerPointのノート欄にも講師ノートを入れています。

`previews/` のPNGはスライド構成を素早く確認するためのコンタクトシートです。保存済みPPTXをPowerPointでレンダリングしたものではありません。
