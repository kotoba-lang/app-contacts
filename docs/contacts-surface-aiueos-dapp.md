# app-contacts（面宣言）— itonami（dapp）上の分散型設計

状態: 第 1 スライス実装（2026-10-20）。正本はこの文書。
追跡: Linear JUN-128（親 JUN-116）。

## 位置づけ

app-contacts は appview の面（contacts-mcp-component）—— 現状は**自己宣言の
情報ページのみ**（SvelteKit scaffold の忠実な port。contacts 機能は元から
無い。route-count 0）。backend dispatcher（app.ts）と未配線の xrpc-proxy が
別に存在する。ここでの判定コアは「面が宣言するメタデータの整合」。

## このスライス: 面宣言の整合判定コア

| 層 | 実体 | 状態 |
|---|---|---|
| 判定コア guest | `aiueos/contacts_surface.cljk` — route-count は routes の数と一致 / kind は閉じた集合（appview/worker/component/app）/ source path の明示 / xrpc の claim と実設定の一貫 | **実装済み・実測** |
| 記述子 | `aiueos/contacts-surface.edn` | **実装済み** |
| parity | `cljs/test/contacts/surface_parity_test.cljk` — 既存オラクル `app_test.cljk` のピン値（default-db）が guest 規則を満たす、壊れた宣言は検出される | **実装済み・実測** |

実測: `amu test aiueos/contacts_surface.cljk` → **15/15**（jvm-kir / js / wasm）。
parity → 6 assertions / 0 failures。

## 実測メモ（境界の正直な記録）

- parity は **fixture 形** —— `contacts.app` は reagent / re-frame を require
  し、kbb でロードできない（npm 依存）。既存テスト `app_test.cljk` が
  ピン留めした default-db の値（field for field）をオラクルとして使う。
- 宣言の値は Svelte scaffold からバイトどおり移植されたもので、
  **wrangler.jsonc からの「訂正」は意図的に行われていない**（正本の注記）。
  この不一致そのものを xrpc-mismatch ビットの運用対象にするのが今後。

## 未実装境界

- contacts 機能そのもの（本 app には存在しない）。
- xrpc-proxy（未配線）の接続判定。backend dispatcher の判定。
- この面宣言判定は app-tia 等の同形状 port にも展開可能 —— 共通化は
  fleet の別スライス。
- mon（DNA）化、aiueos session 載せ（fleet 共通）。
