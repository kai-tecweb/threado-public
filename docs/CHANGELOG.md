# 変更履歴

## v3.0.0-beta1（β リリース開始・2026-05-19）

### 新機能（Linux 対応 + β 1 正式版）

- **画像 / 動画つき Threads 投稿**：投稿モーダルに「📷 メディア」ボタン追加。JPEG / PNG / WebP / MP4（サイズ上限 50 MB、1 投稿あたり 1 メディア）
- **ライセンスキー認証**：β 期間 90 日の β キー方式。`/license` 画面でキー入力 → 認証成功でメイン画面遷移
- **オフライン猶予 7 日間**：ライセンスサーバへの通信が 7 日途切れても動作継続（`read_only` モード切替まで猶予あり）
- **OAuth URL 新タブ動作**：Meta OAuth 認証画面が**新タブ**で開くようになりました（旧版はポップアップ）
- **9 Step セットアップガイド**：[docs/customer-setup-guide.md](customer-setup-guide.md) を「顧客自己完結レベル」に整備（851 行）

### セットアップフロー（v3.0.0-beta1）

詳細は [docs/customer-setup-guide.md](customer-setup-guide.md) を参照。

1. **Step 1**：OCI / さくら VPS で Linux VM 立ち上げ
2. **Step 2**：ドメイン取得 + DNS 設定（**2 パターン併記**）
   - パターン A：顧客独自ドメイン（年 1,500-2,000 円）
   - **パターン B：Threado β サブドメイン（β 推奨）** — Cloudflare 設定は配布担当が代行
3. **Step 3-A**：Cloudflare Configuration Rule（パターン A 専用）
4. **Step 4**：deploy key 経由 `git clone` + `setup.sh`
5. **Step 5**：ライセンスキー入力
6. **Step 6**：画像/動画投稿の使い方
7. **Step 7**：⭐ Meta Threads OAuth App 作成（顧客自身、所要 30-60 分）
   - 3 本のコールバック URL 登録（OAuth Redirect / Deauthorize / Data Deletion）
   - Threads テスター招待 + Threads スマホアプリで「許可」
   - Threads の App Secret（下段）使用 ⚠️ Facebook の App Secret ではない
8. **Step 8**：初期設定ウィザード
9. **Step 9**：動作確認チェックリスト

### β 期間中の運用方針

- **β 期間中（90 日）はサポート提供しません**
- セットアップ手順の質問・トラブル報告は **受け付けません**
- 顧客自己完結のため、ガイドに **「自己解決ガイド」セクション** を新設（10 個の頻出トラブル別手順）
- 開発側で発見した重要修正は手動配信（メール / LINE 通知 → 顧客側で `git pull`）

### 既知の制限事項（v3.0.0-beta1）

| 制限 | 内容 | 改善予定 |
|---|---|---|
| 1 投稿 = 1 メディアのみ | カルーセル投稿（複数画像）未対応 | 正式版で検討 |
| メディア自動削除なし | `data/media/` に蓄積、手動削除が必要 | 正式版で自動クリーンアップ追加予定 |
| スターターパック未同梱 | 投稿テンプレ / サンプルナレッジは含まれません | 正式版で再検討 |
| 不具合報告チャネル | β 中は受け付けない | β 結果次第で正式版で再開検討 |
| Threads OAuth App は顧客自作 | 統合 App（本番審査済）はなし | 顧客が自身の Meta 開発者アカウントで作成、これは Meta 側の制約により永遠に手動 |
| 失効警告 UI | 90 日切れ近接時のバナー表示なし | β フィードバック次第で正式版検討 |
| Meta API 制限 | 動画は MP4（H.264）推奨、解像度・長さ制限あり | Meta API 仕様に従う |
| ドメイン代 | パターン A 選択時は別途必要 | パターン B（β サブドメイン）選択で回避可能 |

### 自己解決ガイド掲載項目（カスタマーセットアップガイド内）

β 期間中はサポート提供しないため、以下の頻出トラブル別自己解決手順を掲載：

1. `setup.sh` の途中で止まる
2. Caddy が HTTPS 証明書を取得しない
3. ライセンス画面が出ない（502 / 404）
4. ライセンスキーが「無効」と表示
5. OAuth 認証で `redirect_uri_mismatch`
6. OAuth で「このアプリは未承認です」
7. OAuth 後に `Invalid OAuth access token`
8. 投稿が公開されない
9. メディアアップロードが 415 や 413
10. それでも分からない時のリソース案内（公式ドキュメント・AI 活用）

### 永遠に手動な操作（Meta 側の制約）

Meta API に App 作成エンドポイントが存在しないため、以下は将来の自動化アップデート後も顧客手動：

- Meta 開発者アカウント取得
- Threads App 作成（App Type = Business、Use Case = Other）
- 3 本のコールバック URL 登録（OAuth Redirect / Deauthorize / Data Deletion）
- Threads テスター招待 + Threads スマホアプリで「許可」
- App ID / App Secret 取得（**Threads の App Secret 下段を使用**、Facebook の App Secret ではない）

### 将来のアップデート予定（β 終了後 / 正式版）

| 機能 | 内容 |
|---|---|
| 価格決定・販売チャネル | 売り切り + 1 年無料アップデート |
| **DNS 自動化** | Threado UI からサブドメイン申請（Cloudflare 連携） |
| **Caddy 自動更新** | サーバ側の HTTPS 設定が自動化 |
| **VPS 自動デプロイ** | Threado UI で IP + サブドメイン入力 → 全自動セットアップ |
| 管理 UI | ライセンスサーバ簡易管理画面 |
| カルーセル投稿 | 1 投稿に複数画像対応 |
| メディア自動クリーンアップ | アップロード後の未使用ファイル自動削除 |

正式版完成時の理想フロー：

| # | 作業 | 自動化状態 |
|---|---|---|
| 1 | さくら VPS or OCI 契約 | 顧客（手動） |
| 2 | Threado 上で IP + サブドメイン入力 | 顧客（UI で完結） |
| 3 | DNS・SSL・Caddy・セットアップが全自動完了 | **自動** |
| 4 | 自分の URL でアクセス | 顧客 |
| 5 | ライセンスキー入力 | 顧客 |
| 6 | Meta App で OAuth 接続 | 顧客（**手動必須・永遠に**） |
| 7 | 使うだけ | 顧客 |

### Linux 動作環境（β 1 時点）

- OS：Ubuntu 22.04 / 24.04 LTS
- アーキ：ARM64（OCI Ampere）/ x86_64（さくら VPS / 他）
- Python：3.10 以上
- AI CLI：Claude Code または Codex CLI（顧客が自分のアカウントで認証）
- インフラ：OCI Always Free Tier / さくら VPS / 他 Linux VM
- HTTPS：Caddy + Let's Encrypt 自動発行
- 配信経路：Cloudflare Proxy → Caddy → app:8765 / cloud:8766

### Windows 版（v2.x）

Windows 版は v2.0 をもって機能凍結。継続使用は可能で、緊急バグ修正のみ対応します。新規機能は v3.0 系（Linux）に集約。

---

## v3.0 Phase 1（Linux 対応リリース・2026-05-19）

### 新機能

- **Linux サーバ対応**：OCI Always Free（月額 0 円）／さくら VPS（月額 約 1,760 円）どちらでも動作
- **systemd 常駐**：`threado-main.service` として自動起動
- **Caddy + HTTPS**：公開ドメイン経由でアクセス可能、Let's Encrypt 自動発行（Cloudflare Proxy ON のままでも動作確認済）
- **OCI Always Free Tier**：月額 0 円で運用可能（Ampere ARM A1）
- **さくら VPS**：即日利用可能、x86_64（Phase 1 実機検証はさくら VPS で実施）

### セットアップ

- `setup.sh` 一発で apt パッケージ・Python venv・Playwright・Caddy・systemd まで自動セットアップ
- 詳細手順は [docs/customer-setup-guide.md](customer-setup-guide.md) を参照（OCI / さくら VPS の両対応）

### 既存機能（全て Linux 上でもそのまま動作）

Windows v2.x の全機能が引き続き Linux 上でも動作します：

- 複数 Threads アカウント管理・投稿・予約投稿・スレッド投稿
- リサーチ（API keyword_search + Playwright スクレイピング）
- ナレッジ管理（カード・アップロード `.txt`/`.md`/`.pdf`/`.docx`）
- 自動返信ルール（キーワード + AI 生成 + 遅延送信）
- シナリオ分岐型自動リプライ
- 自律投稿（auto_planner）
- PDCA 改善ループ
- メトリクス取得・ダッシュボード
- AI CLI（Claude Code / Codex CLI、アカウント別 preferred_cli）

### 既知の制限事項（v3.0 系で順次対応予定）

- **画像・動画投稿**：v3.x の Phase 3 で対応予定（メディアアップロード機能）
- **ライセンスキー認証**：v3.x の Phase 2 で対応予定（パッケージ販売のための仕組み）
- **β 公開・一般リリース**：上記機能完成後

### Windows 版（v2.x）

Windows 版は v2.0 をもって機能凍結。継続使用は可能で、緊急バグ修正のみ対応します。
新規機能は v3.0 系（Linux）で開発します。

### 動作環境

- OS：Ubuntu 22.04 / 24.04 LTS（推奨）
- アーキ：ARM64（OCI Ampere）/ x86_64（さくら VPS / 他）
- Python：3.10 以上
- AI CLI：Claude Code または Codex CLI（顧客が自分のアカウントで認証）
- インフラ：OCI Always Free Tier / さくら VPS / 他 Linux VM

---

## v2.0.0（2026年5月）完了報告

### オズさんのご要望から実装
- シナリオ分岐型自動リプライ（キーワードトリガー＋ステップ配信）
- ナレッジアップロード機能（.txt/.md/.pdf/.docx対応）
- Codex CLI対応（Claude Code / Codex CLIの2択・アカウント別優先CLI設定）

### AIキット参考で追加
- ナレッジベース設計（集客・教育・販売・マーケティング知識をDB管理）
- PDCA精度向上（Engagement Rate導入・7日中期窓・投稿時刻シグナル）
- リサーチ→ナレッジ化精度向上（構造化分析・TOP/中位/下位比較）

### 岩崎判断で追加
- デスクトップアプリ化（ブラウザ不要・アイコンダブルクリックで起動）
- ワンセットセットアップ（setup.batで全自動・デスクトップアイコン自動作成）
- UI余白統一・CLI自動検出・初回セットアップウィザード
- OAuth改善・トークン期限7日前アラート・スコープ自動チェック
- ガイドページ更新（はじめに・PART0・PART1を最新手順に更新）
