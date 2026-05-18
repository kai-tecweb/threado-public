# 変更履歴

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
