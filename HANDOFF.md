# HANDOFF — 工程表メーカー

Claude Code などで作業を引き継ぐときの要点。

## 現状
- `index.html` 単一ファイル。動作確認済みの機能は README のとおり。
- 未着手・要検討：
  - 祝日を「工程表ごと」に持つオプション（今は全工程表で共通）
  - 工程の並べ替えをドラッグ操作に（今は↑↓ボタン）
  - 週単位・月単位の表示（今は日単位のみ）

## 構成（index.html 内）
- `<style>`：設計図調のデザイン。色は `:root` の CSS 変数に集約
- `<script>`（即時関数）
  - **定数**：`PALETTE`（区分色12色）、`DEFAULT_CATS`、`jpHolidays(y)`（祝日計算）
  - **状態** `state`：`{ holidays, cats, patterns, tone, docs[], current, settingsOpen }`
    - `docs[]` の各要素：`{ id, name, skipSun, skipHol, viewStart, viewEnd, tasks[] }`
    - `tasks[]` の各要素：`{ name, cat, start:'YYYY-MM-DD', days, note }`
    - `doc()` が現在選択中の工程表を返す
  - **日付**：`endOf(task)` が終了日（休日設定を反映）。`viewRange()` が表示期間
  - **色**：`eff(hex)` が全体トーン適用後の実表示色。描画・Excel は必ず `eff()` 経由
  - **描画**：`renderDocs / renderHol / renderCats / renderEntry / renderGantt`、まとめて `renderAll()`
  - **Excel**：`btnXlsx` ハンドラ内。固定列7（No/工種/区分/開始/終了/日数/備考）＋日付列。`FIX` を変えると列位置が連動

## 守っている方針
- 色指定 UI はブラウザ標準のカラーピッカーを使わず、パレット式＋全体調整
- 工程表の見出し3段は `rowspan` を使わない（固定レイアウト＋stickyで列ずれが出た）
- Excel は書式のみ、関数は使わない（Excel 2016 互換）
- 保存キーを変える場合は `loadState()` に移行処理を足す

## 動作確認のしかた
- ローカルでは `index.html` を直接開くだけ。Excel出力のみ cdnjs への接続が必要
- 祝日計算の検算：`jpHolidays(2025)` が 2/24・11/24（振替）を含むこと、`jpHolidays(2026)` が 9/22（国民の休日）を含むこと
