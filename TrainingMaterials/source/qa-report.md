# QA Report

## 生成結果

- PPTX: `TrainingMaterials/decks` に6本生成。
- Markdown講師ノート: `TrainingMaterials/notes` に6本生成。
- 各PPTX: 14スライド、14ノートを確認。
- PPTX内の画像メディア: 各PPTXで検出済み。
- PPTX内のコード抜粋: 各PPTXで主要メソッド名を検出済み。
- 講師ノート: 各PPTXの `notesSlides` 内に `講師ノート` を検出済み。
- 網羅表: `TrainingMaterials/source/coverage.md` に全スクリプト・主要フィールド・関数の対応スライドを整理済み。

## 確認用プレビュー

`TrainingMaterials/previews` に各回のコンタクトシートPNGを生成しています。

このPNGはスライド構成データから作った確認用プレビューであり、PowerPoint/LibreOfficeで保存済みPPTXをレンダリングした結果ではありません。

## 未確認事項

- この環境では `soffice` / `libreoffice` が見つからなかったため、保存済みPPTXのヘッドレスレンダリングによる文字折り返し・重なり確認は未実施です。
- 最終配布前にPowerPointで各PPTXを開き、ノート欄、スクリーンショット、コード抜粋の表示を目視確認してください。
