# Threado v3.0.0-beta1 顧客向けセットアップガイド

最終更新: 2026-05-19（β 1 リリース版）
対象読者: Threado β テスター

> ✅ **本ガイドは β 顧客が読んで完結できることを目指しています**。ドメイン取得・DNS・Meta OAuth App 作成すべてご自身で実施いただきます。
>
> ⚠️ **β 期間中（90 日）はセットアップに関するご質問をお受けできません**。お困りの際は「自己解決ガイド」セクションをご活用ください。

---

## このガイドで達成すること

1. **Step 1**：インフラを選び Linux VM を立ち上げる
2. **Step 2**：ドメイン取得 + DNS 設定（**2 パターン**から選択：独自ドメイン / Threado β サブドメイン）
3. **Step 3-A**：Cloudflare Configuration Rule（パターン A のみ、パターン B は配布担当が代行）
4. **Step 4**：Threado をインストール（`git clone` + `setup.sh`）
5. **Step 5**：ライセンスキーを入力
6. **Step 6**：画像/動画投稿の使い方
7. **Step 7**：Meta Threads OAuth App を作成（顧客自身）⭐ 最も複雑
8. **Step 8**：初期設定ウィザード
9. **Step 9**：動作確認チェックリスト

**所要時間**：
- パターン A（独自ドメイン）：合計 2-3 時間（OCI 在庫待ちで延びる場合あり）
- パターン B（Threado β サブドメイン、β 推奨）：合計 1.5-2 時間（DNS / Cloudflare の手間が省ける）

---

## β 期間中の重要事項

### サポート方針（β 期間 = 90 日間）

- 本ガイド + 自己解決ガイド + ネット検索で **すべて自力解決**してください
- β テスター 2 名（オズさん・あゆむさん）は技術知識ある前提で配布しています
- 開発側で発見した重要修正は配布担当から手動配信通知が来ます（`git pull` してください）

### β 期間中のサポート除外範囲（すべて顧客自己解決）

- セットアップ手順の質問・トラブル
- VPS / OCI の申込・課金トラブル
- Meta 開発者アカウント取得・OAuth App 設定
- ドメイン・DNS・Cloudflare 設定（**パターン A 選択時**。パターン B では配布担当が代行）
- HTTPS 証明書のトラブル
- Meta API のエラー対応

### ライセンスキー

- 配布形式：**β 期間限定 90 日キー**
- 形式：`XXXX-XXXX-XXXX-XXXX`（ハイフン区切り英数字）
- 期限切れ後は read_only モード（投稿不可、閲覧のみ）
- β 終了後に正式版で正規キーへの切替方法を案内

---

## 用意するもの

- クレジットカード（OCI 登録時の本人確認用、課金は発生しません）
- 電話番号（OCI 登録時の SMS 認証用）
- メールアドレス（Threado / Meta / Cloudflare / ドメイン業者で個別または共通）
- パソコン（Windows / Mac / Linux どれでも、SSH ターミナルが使えるもの）
- 独自ドメイン購入予算（年 1,500-2,000 円）
- **Threado β ライセンスキー**（配布担当から配布、90 日有効）
- **Cloudflare アカウント**（無料、Step 2 で作成）
- **Meta 開発者アカウント**（無料、Step 7 で作成。Facebook アカウントベース）
- **Threads アカウント**（運用したい個人/法人アカウント、スマホアプリも必要）

---

## インフラ選択肢

Threado v3.0 は Linux サーバ上で動作します。**サーバの調達方法は 2 パターン**から選べます。

### Option A：OCI Always Free（月額 0 円）

| 項目 | 値 |
|---|---|
| 月額費用 | **0 円**（Always Free 枠の範囲内、永続無料）|
| スペック | Ampere A1 ARM（最大 4 OCPU / 24 GB RAM）|
| アーキ | ARM64（aarch64）|
| 取得難度 | やや高い（在庫切れの可能性あり、PAYG 化推奨）|
| 即日利用 | △（在庫待ちが発生する場合あり）|

**こんな人にお勧め**：月額コストを 0 円に抑えたい、時間に余裕がある、ARM64 で問題ない。

### Option B：さくら VPS（月額 約 1,760 円）

| 項目 | 値 |
|---|---|
| 月額費用 | **約 1,760 円**（2GB プラン、年払いで 12% 割引あり）|
| スペック | 2 GB RAM / 50 GB SSD / 仮想 3 コア |
| アーキ | x86_64 |
| 取得難度 | 低い（メール登録 → 数分で利用可能）|
| 即日利用 | ◎（クレジットカード登録後すぐ起動）|

**こんな人にお勧め**：今すぐ動かしたい、登録の手間を減らしたい、x86_64 環境に慣れている。

### どちらを選んでも、Step 2 以降は完全に共通

VM が立ち上がってドメインを取得した後の手順は**インフラを問わず同じ**です。インフラの差は「Step 1：VM を立ち上げる」の中だけ。

---

## Step 1：VM を立ち上げる

選んだインフラに応じて、対応する小節へ進んでください。

- **Option A を選んだ場合** → 「Step 1-A：OCI 編」へ
- **Option B を選んだ場合** → 「Step 1-B：さくら VPS 編」へ

### Step 1-A：OCI 編

#### 1-A-1. OCI 無料アカウントを作る

1. https://www.oracle.com/cloud/free/ を開く
2. 「Start for free」をクリック
3. 国（Japan）・メールアドレス・名前を入力
4. 認証メールが届くのでリンクをクリック
5. パスワード設定、組織情報入力（個人なら自分の名前で OK）
6. クレジットカードを登録（**請求は発生しません**、本人確認のみ）
7. SMS で電話番号認証
8. 登録完了 → OCI コンソールにログイン

> 💡 **ポイント**：「Always Free」と「30 日間無料トライアル」が同時に始まります。30 日経過後もトライアル部分が切れるだけで、Always Free 枠は永続無料です。

#### 1-A-2. PAYG（従量課金）へのアップグレード（推奨）

無料トライアル中は OCI の安定性が落ちる場合があります。PAYG にアップグレードすると Always Free 枠は引き続き無料で、安定性が向上します。

1. OCI コンソール右上のメールアドレス → 「Manage account」
2. 「Upgrade to Paid」をクリック
3. 画面の指示に従う

#### 1-A-3. 課金アラートを必ず設定

1. OCI コンソール → ハンバーガーメニュー → 「Billing & Cost Management」
2. 「Budgets」→ 「Create Budget」
3. 「Monthly Budget Amount」を **$1 USD** に設定
4. 「Budget Alert」を「50%」「100%」で 2 段階通知
5. 通知メールアドレスを登録 → 保存

#### 1-A-4. Compute Instance を作成

1. OCI コンソール → ハンバーガーメニュー → 「Compute」→ 「Instances」
2. 右上の「Create Instance」をクリック
3. 以下の設定を選ぶ：

| 項目 | 値 |
|---|---|
| Name | `threado-server`（任意） |
| Image | **Ubuntu 22.04** を選択 |
| Shape | **VM.Standard.A1.Flex**（ARM、Always Free） |
| OCPU 数 | 1（最小、後で拡張可能） |
| Memory | 6 GB |
| Boot Volume | 50 GB（Always Free 範囲内） |

4. **SSH キー**：「Generate a key pair for me」を選び、**両方ダウンロード**（`.key`（秘密鍵）と `.pub`（公開鍵）の 2 つ。`.key` は絶対に他人に渡さない）
5. 「Create」をクリック
6. VM が起動するまで 1〜3 分待つ

> ⚠️ **「Out of capacity」エラーが出たら**：東京/大阪リージョンの Ampere ARM は人気があり時々在庫切れになります。リージョンを「US East (Ashburn)」等に切り替えて試すか、PAYG にアップグレードしてから再試行。

#### 1-A-5. パブリック IP を確認

Instance の詳細ページに「Public IP Address」が表示されるのでメモ（例：`152.69.123.45`）。

#### 1-A-6. ファイアウォール（VCN）の設定

Threado に外部からアクセスするため、80 / 443 ポートを開放：

1. Instance の詳細ページの「Primary VNIC」セクションで「Subnet」のリンクをクリック
2. 「Default Security List」をクリック
3. 「Add Ingress Rules」をクリックし以下を 2 つ追加：

| Source CIDR | IP Protocol | Destination Port Range |
|---|---|---|
| 0.0.0.0/0 | TCP | 80 |
| 0.0.0.0/0 | TCP | 443 |

4. 「Add Ingress Rules」をクリックして保存

#### 1-A-7. VM に SSH 接続

```bash
chmod 600 ssh-key-XXXX.key
ssh -i ssh-key-XXXX.key ubuntu@152.69.123.45
```

Windows は PowerShell で同じコマンドを実行（Windows 10/11 は ssh 標準搭載）。

→ Step 2 へ進む。

---

### Step 1-B：さくら VPS 編

#### 1-B-1. さくらの会員登録

1. https://vps.sakura.ad.jp/ を開く
2. 右上の「お申し込み」をクリック
3. さくらインターネットの会員登録（メール認証）
4. 支払い方法を登録（クレジットカード推奨、銀行振込も可）

#### 1-B-2. VPS プランを選択

「VPS for ホビー」ではなく「**さくらの VPS**」（標準プラン）を選択。

| 項目 | 推奨値 |
|---|---|
| プラン | **2GB プラン**（約 1,760 円/月、画像/動画投稿を多用する場合は 4GB プラン推奨）|
| リージョン | 東京 第 1 / 東京 第 2 / 大阪 / 石狩 のいずれか |
| OS | **Ubuntu 24.04 amd64**（標準 OS から選択）|
| 仮想化方式 | 標準（KVM）|
| ストレージ | プラン標準（2GB プランなら 50GB SSD）|

#### 1-B-3. VPS 起動 + 初期情報の受領

1. 申し込み完了 → 数分で VPS 起動
2. 登録メールアドレスに以下が届く：
   - **IPv4 アドレス**（例: `153.126.131.215`）
   - **初期ユーザ名**: `ubuntu`
   - **初期パスワード**: メール記載

#### 1-B-4. VM に SSH 接続

```bash
ssh ubuntu@153.126.131.215
```

メールに記載のパスワードを入力（カーソルは動かないが正常）。

#### 1-B-5. SSH 鍵認証に切り替え（推奨）

ローカル PC で以下を実行：

```bash
ssh-keygen -t ed25519 -N "" -f ~/.ssh/id_ed25519
ssh-copy-id ubuntu@153.126.131.215
```

#### 1-B-6. passwordless sudo + スワップ追加（推奨）

VPS にログインした状態で（sudo パスワードを 1 回だけ要求）：

```bash
sudo bash -c '
echo "ubuntu ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/90-ubuntu-nopasswd
chmod 440 /etc/sudoers.d/90-ubuntu-nopasswd
fallocate -l 2G /swapfile
chmod 600 /swapfile
mkswap /swapfile
swapon /swapfile
grep -q /swapfile /etc/fstab || echo "/swapfile none swap sw 0 0" >> /etc/fstab
echo OK
'
```

→ Step 2 へ進む。

---

## Step 2：ドメイン取得 + DNS 設定（2 パターンから選択）

β 期間中は **2 パターンから選択** できます。**β 中は Threado β サブドメインの利用を推奨**します（最速で動作確認可能）。正式版移行時に独自ドメインへの切替も可能です。

| 項目 | パターン A：顧客独自ドメイン | **パターン B：Threado β サブドメイン（β 推奨）** |
|---|---|---|
| URL 例 | `https://threado.your-domain.com/` | `https://<your-name>-threads.ses-jv.jp/` |
| ドメイン取得 | 必要（年 1,500-2,000 円） | 不要 |
| Cloudflare アカウント | 自分で作成 | 配布担当が代行 |
| DNS 設定 | 自分で設定 | 配布担当が代行（VM IP を伝えるだけ） |
| Cloudflare Configuration Rule | 自分で設定（次の Step 3-A） | 配布担当が代行（Step 3 不要） |
| 所要時間 | 30-60 分（DNS 反映待ち含む） | **5-10 分**（配布担当作業を含む） |
| 正式版移行時 | URL 変更なし | 独自ドメインへ移行可能（任意） |
| メリット | 完全自己完結、長期 URL 固定 | 最速で動作確認、ドメイン代不要 |
| デメリット | 設定の手間 + 年間コスト | URL が `*.ses-jv.jp` 固定（β 期間中のみ） |

選んだパターンに応じて、対応する小節へ進んでください：

- **パターン A を選んだ場合** → 「Step 2-A：顧客独自ドメイン」へ
- **パターン B を選んだ場合（β 推奨）** → 「Step 2-B：Threado β サブドメイン」へ

---

### Step 2-A：顧客独自ドメイン（完全自己完結）

> パターン B（Threado β サブドメイン）を選んだ方は本セクションを飛ばして「Step 2-B」へ進んでください。

#### 2-A-1. ドメインを取得

安価で日本語サポートのある業者：

- お名前.com — https://www.onamae.com/
- ムームードメイン — https://muumuu-domain.com/
- Cloudflare Registrar — https://www.cloudflare.com/products/registrar/（Cloudflare で完結できるため最もシンプル）

`.com` / `.net` で年 1,500〜2,000 円程度。

#### 2-A-2. Cloudflare アカウント作成

1. https://dash.cloudflare.com/sign-up を開く
2. メールアドレス + パスワード入力 → アカウント作成
3. メール認証を完了

#### 2-A-3. Cloudflare にドメインを登録

1. Cloudflare ダッシュボード → 「Add a site」
2. ドメイン名を入力 → 「Continue」
3. プラン選択：**Free Plan（無料）**
4. Cloudflare が既存 DNS レコードを自動取得 → 「Continue」

#### 2-A-4. ドメイン業者で Cloudflare ネームサーバを設定

Cloudflare 画面に表示されている 2 本のネームサーバ（例：`anna.ns.cloudflare.com` / `nick.ns.cloudflare.com`）をドメイン取得業者の管理画面で設定：

1. ドメイン業者の管理画面でドメイン詳細を開く
2. 「ネームサーバ設定」を Cloudflare の 2 本に書き換え（既存のネームサーバは削除）
3. 保存
4. Cloudflare 側に戻り「Check nameservers」をクリック
5. 反映に最大 24 時間（実際は数十分が多い）

反映完了後、Cloudflare ダッシュボードの「Overview」ページにドメインが「**Active**」と表示される。

> ⚠️ Cloudflare Registrar でドメインを取得した場合、この 2-A-4 はスキップ可能（ネームサーバは初めから Cloudflare）。

#### 2-A-5. DNS A レコードを設定

Cloudflare ダッシュボード → 該当ドメイン → 「DNS」→ 「Records」：

1. 「Add Record」をクリック
2. 以下を入力：
   - **Type**: A
   - **Name**: `threado`（または `@` でドメイン直）
   - **IPv4 address**: VM のパブリック IP（例：`152.69.123.45`）
   - **Proxy status**: **Proxied（オレンジ雲 ON）** ⭐ 必須
   - **TTL**: Auto
3. Save

例：ドメイン `example.com` + Name `threado` で `threado.example.com` が VM に向く。

#### 2-A-6. DNS 反映を確認

```bash
nslookup threado.example.com
```

Cloudflare の IP（`172.67.x.x` または `104.21.x.x` 等）が返ってくれば OK。VM の IP は Proxy 経由のため隠蔽されます。

→ 次に **Step 3-A：Cloudflare Configuration Rule** を実施。

---

### Step 2-B：Threado β サブドメイン（β 推奨）

> パターン A（顧客独自ドメイン）を選んだ方は本セクションを飛ばして「Step 3-A」へ進んでください。

ドメイン取得 / Cloudflare アカウント作成 / DNS 設定 / Configuration Rule すべて **配布担当が代行**します。顧客は VM IP と希望サブドメイン名を伝えるだけ。

#### 2-B-1. 希望サブドメイン名を決める

`<your-name>-threads.ses-jv.jp` の形式。`<your-name>` 部分を以下のルールで決める：

- 英小文字 + 数字 + ハイフン（`-`）のみ
- 1〜20 文字
- 例：`yamada-threads` / `ozkawa-threads` / `ayumu-threads`

> 💡 既に `oz-threads.ses-jv.jp` と `ayumu-threads.ses-jv.jp` は β 用に確保済み（2026-05-19 時点）。

#### 2-B-2. 配布担当に発行依頼

メールまたは LINE で以下を送信：

```
件名: Threado β サブドメイン発行依頼

希望サブドメイン: <your-name>-threads.ses-jv.jp
VM パブリック IP: 152.69.123.45（Step 1 で確認した値）
```

#### 2-B-3. 配布担当からの返信を待つ

配布担当が以下を実施（数時間〜半日以内）：

- Cloudflare DNS A レコード追加（`<your-sub>.ses-jv.jp` → 顧客 VM IP、Proxy ON）
- Cloudflare Configuration Rule（SSL Full strict）追加
- ※ Caddyfile への追記は **顧客自身**が Step 4-3 で実施します

返信内容：「DNS / Cloudflare 設定完了しました。Step 4 以降を進めてください」

#### 2-B-4. DNS 反映を確認

```bash
nslookup <your-sub>.ses-jv.jp
```

Cloudflare の IP（`172.67.x.x` または `104.21.x.x`）が返ってくれば OK。VM の IP は Proxy 経由のため隠蔽されます。

→ **Step 3 はスキップ**（配布担当が Configuration Rule を代行済）、**Step 4 へ進む**。

---

## Step 3-A：Cloudflare Configuration Rule（SSL Full strict 上書き）— パターン A 専用

> パターン B（Threado β サブドメイン）を選んだ方は本ステップ全体をスキップして「Step 4」へ進んでください。

### 3-A-1. なぜ必要か

Cloudflare のデフォルト SSL/TLS モードは「Flexible」ですが、Threado は HTTPS でバックエンドにアクセスする必要があるため **Full (strict)** に上書きします。ゾーン全体を Full (strict) にすると他のサイトに影響するため、**Configuration Rule で対象ホスト名だけ上書き**するのがベストプラクティス。

### 3-A-2. Configuration Rule を作成

Cloudflare ダッシュボード → 該当ドメイン → 「Rules」→ 「Configuration Rules」：

1. 「Create rule」をクリック
2. 入力：
   - **Rule name**: `Threado SSL Full strict`（任意）
   - **When incoming requests match**: **Hostname** equals `threado.your-domain.com`
   - **Then the settings are**: **SSL → Full (strict)**
3. Deploy

### 3-A-3. Bot Fight Mode の確認（外向き通信用）

Threado は VM から `license.ses-jv.jp` 等への外向き HTTPS 通信を行います。Cloudflare ダッシュボードの「Security」→「Bots」で **Bot Fight Mode が ON の場合**、Python urllib のデフォルト UA が `error 1010` で弾かれる事例がありますが、Threado は内部で User-Agent を明示しているため通過します。本ステップでの追加設定は不要。

### 3-A-4. 確認

```bash
curl -sI https://threado.your-domain.com/ | head -5
```

HTTPS が通れば OK（Threado が立ち上がっていない場合は 502 だが、TLS 接続自体は成功すれば次の Step 4 へ）。

---

## Step 4：Threado をインストール

本ステップは**完全に顧客自身**で実施いただきます。

### 4-1. リポジトリ clone（deploy key 経由）

β テスター用 SSH 鍵は **Threado 専用に新規生成**してください（既存の `~/.ssh/id_ed25519` 等を使い回さない）。理由：Threado 用 Deploy Key を顧客 VM 単位で発行・無効化できるようにするため、および他リポジトリへの不正アクセスを防ぐため。

#### 4-1-a. SSH 鍵を VM 上で作成

```bash
ssh-keygen -t ed25519 -C "threado-deploy" -f ~/.ssh/id_ed25519_threado -N ""
```

- `-t ed25519`: 推奨アルゴリズム（短くて安全）
- `-C "threado-deploy"`: 鍵のコメント（何の鍵か判別用）
- `-f ~/.ssh/id_ed25519_threado`: ファイル名（既存鍵と衝突しない名前）
- `-N ""`: パスフレーズなし（git 自動化用）

実行すると以下の 2 ファイルが生成されます：

| ファイル | 内容 | 共有 |
|---|---|---|
| `~/.ssh/id_ed25519_threado` | **秘密鍵**（絶対に共有しない） | ❌ |
| `~/.ssh/id_ed25519_threado.pub` | **公開鍵**（配布担当に送付するのはこちら） | ✅ |

#### 4-1-b. 公開鍵を確認 + 配布担当に送付

```bash
cat ~/.ssh/id_ed25519_threado.pub
```

出力例：

```
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAI...UPLi+8a== threado-deploy
```

**この 1 行**をすべてコピーして、メールまたは LINE で配布担当に送付してください。

> ⚠️ **送付するのは `.pub` の方（公開鍵）のみ**。`.pub` がついていない `~/.ssh/id_ed25519_threado`（秘密鍵）は **絶対に外部に出さないでください**。
>
> ⚠️ 改行や前後の空白が混入しないよう注意。`cat` 出力をそのままコピー&ペーストするのが確実。

#### 4-1-c. 配布担当からの登録完了通知を待つ

配布担当が受領後、threads-control-center リポジトリの Deploy Key として登録します（数時間〜半日以内に返信）。

返信内容例：

```
Deploy Key 登録完了しました。以下のコマンドで git clone してください：

    cd ~
    git clone threado-github:kai-tecweb/threads-control-center.git threado

その後の手順は Step 4-2 以降を参照してください。
```

#### 4-1-d. SSH config を設定

```bash
cat >> ~/.ssh/config <<'EOF'
Host threado-github
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_threado
    IdentitiesOnly yes
EOF
chmod 600 ~/.ssh/config
```

ポイント：

| 設定 | 意味 |
|---|---|
| `Host threado-github` | ホストエイリアス（任意の名前、`threado-github:` で始まる URL で使う） |
| `HostName github.com` | 実際の接続先 |
| `User git` | GitHub の SSH ユーザ名は固定で `git` |
| `IdentityFile ~/.ssh/id_ed25519_threado` | Threado 用秘密鍵を指定 |
| `IdentitiesOnly yes` | 他の SSH 鍵を試行させない（重要、SSH agent に他の鍵があると誤認される） |

#### 4-1-e. 接続テスト（推奨）

clone する前に SSH 接続が成立するかテスト：

```bash
ssh -T threado-github
```

成功時の出力：

```
Hi kai-tecweb/threads-control-center! You've successfully authenticated, but GitHub does not provide shell access.
```

`Permission denied (publickey).` が出る場合は次のいずれか：

- 配布担当側の Deploy Key 登録が未完了 → 返信を待つ
- SSH config の `IdentityFile` パスが間違い → `cat ~/.ssh/config` で確認
- 公開鍵の改行混入で登録時に壊れた → 配布担当に「公開鍵を再送するので登録し直してください」依頼

#### 4-1-f. clone

```bash
cd ~
git clone threado-github:kai-tecweb/threads-control-center.git threado
cd threado
```

> ⚠️ URL は `threado-github:` で始まる**こと**（4-1-d の SSH config Host エイリアスを参照）。`git@github.com:` ではない点に注意。後者だと個人 SSH 鍵が使われてしまい認証失敗します。

#### 4-1-g. clone 後の確認

```bash
cd ~/threado
git log --oneline | head -5
git tag -l | tail -5  # v3.0.0-beta1 が表示されれば最新
```

`v3.0.0-beta1` タグが見えれば β 1 リリース版を取得できています。

### 4-2. setup.sh を実行

```bash
bash setup.sh
```

このスクリプトが自動で以下を行います：

1. システムパッケージのインストール（Python venv / Caddy 等）
2. Python 仮想環境（venv）作成
3. 依存パッケージインストール
4. Playwright Chromium インストール
5. Caddy（HTTPS 終端）インストール
6. systemd サービス登録 + 自動起動（`threado-main` / `threado-cloud`）

実行時間：約 5〜10 分

### 4-3. Caddyfile を編集（ドメインを設定）

```bash
sudo cp Caddyfile.example /etc/caddy/Caddyfile
sudo nano /etc/caddy/Caddyfile
```

冒頭の `{$DOMAIN}` 部分を **自分のドメイン名**に置き換える（複数箇所あり、すべて）：

| パターン | 書き換え後の例 |
|---|---|
| パターン A（独自ドメイン） | `threado.your-domain.com` |
| パターン B（Threado β サブドメイン） | `<your-name>-threads.ses-jv.jp`（例：`yamada-threads.ses-jv.jp`） |

```
<your-domain> {
    ...
}
```

`Caddyfile.example` にはライセンス認証・メディア配信のルート分岐（`/license` `/api/license/*` `/static-additions/*` `/cloud/health` `/media/*` + `forward_auth`）がすでに入っているので、ドメイン書き換え以外の編集は不要。

保存（`Ctrl+O` → Enter → `Ctrl+X`）。

### 4-4. Caddy を起動

```bash
sudo systemctl restart caddy
sudo systemctl enable caddy
```

初回起動時に Let's Encrypt から HTTPS 証明書が自動発行されます（30 秒〜2 分）。

### 4-5. 動作確認

ブラウザで自分のドメイン（`https://<your-domain>/`）を開く。

- 🔒 鍵マークが付き、**ライセンスキー入力画面** にリダイレクトされれば成功
- 鍵マークが出ない・接続エラーの場合は「自己解決ガイド」を参照

---

## Step 5：ライセンスキーを入力

### 5-1. ライセンス画面

ブラウザで自分のドメイン（`https://<your-domain>/`）を開くと、**ライセンスキー入力画面**にリダイレクトされます。

### 5-2. キーを入力

配布担当から受け取った 90 日 β キー（`XXXX-XXXX-XXXX-XXXX` 形式）を貼り付け：

- 大文字小文字は自動補正されます
- ハイフンは自動挿入されます

「認証する」ボタンをクリック。

### 5-3. 認証成功時の挙動

- メイン画面（ダッシュボード）に遷移
- `data/license_state.json` にキーと有効期限が保存される
- ライセンスサーバへの heartbeat は 1 日 1 回バックグラウンド送信

### 5-4. ネット不通時の挙動

ライセンスサーバ（`license.ses-jv.jp`）へのアクセスが 7 日間途切れると read_only モード（投稿不可、閲覧のみ）に切替。

### 5-5. キー期限切れ（90 日後）

read_only モードに切替。β 期間終了後、配布担当から正式版移行（正規キー）の案内があるまでお待ちください。

---

## Step 6：画像/動画投稿の使い方

### 6-1. 投稿モーダルでメディアアップロード

1. メイン画面 → 「投稿を作成」ボタン → 投稿モーダルが開く
2. テキストを入力
3. フッターの **「📷 メディア」ボタン** をクリック
4. ファイル選択ダイアログから JPEG / PNG / WebP / MP4 を 1 つ選択
5. アップロード完了 → 「📷 ファイル名 ✕」のバッジが表示

### 6-2. 対応形式と制限

| 項目 | 値 |
|---|---|
| 対応形式 | JPEG / PNG / WebP / MP4 |
| サイズ上限 | **50 MB** |
| 1 投稿あたり | 1 メディアまで（カルーセル未対応） |
| 動画推奨 | MP4（H.264）、短編（30 秒以内）+ 1080p 以下 |

### 6-3. 取り消し

- バッジの「✕」をクリック → 添付取消
- 投稿モーダルを閉じると自動クリア

### 6-4. メディアの保存先

VM の `data/media/<uuid>.<ext>` に UUID 名で保存。Threads API が `https://<your-domain>/media/<uuid>.<ext>` から取得します。投稿後の自動削除は β 期間中行わないため、ストレージ管理は手動でお願いします（`rm data/media/*.jpg` 等）。

---

## Step 7：Meta Threads OAuth App を作成（顧客自身）⭐ 最も複雑

> ⚠️ **このステップが本ガイドで最も複雑です**。Meta の管理画面は頻繁に変更されるため、本ガイドの説明と実機が異なる場合があります。**Step 7 の所要時間目安：30-60 分**。

本ステップは**完全に顧客自身**で実施いただきます。Meta 側の制約により、OAuth App 作成は自動化不可能（将来も永遠に顧客手動）。

### 7-1. Meta 開発者アカウント取得

1. https://developers.facebook.com/ にアクセス
2. 右上「Log In」→ Facebook アカウントでログイン（Facebook 個人アカウントが必要）
3. 「Get Started」→ 開発者登録（電話番号認証あり）
4. 開発者プロフィール入力

### 7-2. Threads アプリを作成

1. 「My Apps」→ 「Create App」
2. 「Use Case」で **「Other」** を選択（他のテンプレートでは Threads が出ない）
3. 「App type」で **「Business」** を選択
4. アプリ名 + 連絡先メール入力 → 作成

### 7-3. Threads API を追加

1. 作成された App の管理画面 → 左サイドバー「Add product」
2. **「Threads API」** を選んで「Set up」をクリック
3. Threads API 設定画面が開く

### 7-4. ⭐ 3 本のコールバック URL を登録（必須）

Threads API → 「Use cases」→ 「Threads API」→ 「Settings」（または「Configurations」）で以下の **3 本を必ず全部** 登録：

| 設定項目 | 登録 URL（`<your-domain>` を自分のドメインに置換） |
|---|---|
| **Valid OAuth Redirect URIs** | `https://<your-domain>/oauth/threads/callback` |
| **Deauthorize Callback URL** | `https://<your-domain>/api/meta/deauthorize` |
| **Data Deletion Request URL** | `https://<your-domain>/api/meta/data-deletion` |

`<your-domain>` の具体例：

| パターン | `<your-domain>` の値 |
|---|---|
| パターン A（独自ドメイン） | `threado.your-domain.com` |
| パターン B（Threado β サブドメイン） | `<your-name>-threads.ses-jv.jp`（例：`yamada-threads.ses-jv.jp`） |

> ⚠️ **3 本全て登録しないと App の承認が完了しません**。Meta のチェックで「OAuth Redirect URI のみ」だと「Data Deletion Callback URL not set」エラーで Threads API が有効化されません。
>
> ⚠️ **自分のドメインに必ず置き換えてください**。例として `<your-domain>` 部分をそのまま登録してはいけません。

3 本のパスはすべて Threado の実装側で受け口が用意済み（`app/main.py` で `/oauth/threads/callback` `/callback` `/api/meta/deauthorize` `/api/meta/data-deletion` を処理）。

### 7-5. ⭐ Threads テスターを招待 → Threads スマホアプリで承認（必須）

> 開発者モード（未審査の App）では、Threads アカウントを「テスター」として App に紐付けないと OAuth フローが完走しません。**Threads OAuth で最も詰まりやすいポイント**です。

#### 7-5-a. テスター招待（Meta 管理画面）

1. Threads API 設定 → 「Roles」→ 「Test Users」（あるいは「Threads Tester」セクション）
2. 「Add Threads Tester」をクリック
3. 招待したい Threads アカウントの **ユーザー名**（@マークなし）を入力
4. 「Send Invitation」

#### 7-5-b. ⭐ Threads スマホアプリで承認（必須・忘れがち）

招待された Threads アカウントを所有する端末で：

1. **Threads アプリ（スマホ）** を開く
2. 右上の **「≡」（2 本線メニュー）** をタップ
3. 「**アカウント**」をタップ
4. 「**ウェブサイトのアクセス許可**」をタップ
5. 「**App and Websites**」→ 「**Invites**」を選択
6. 招待された Threado App が表示されるので **「許可」** をタップ

> ❌ Meta 管理画面側で「Test User」を追加しただけでは OAuth が通りません。**Threads スマホアプリで明示的に「許可」する必要があります**。これが今日（2026-05-19）の検証で判明した知見です。
>
> 💡 メニュー階層が見つからない場合：Threads アプリのバージョン差で多少表記が変わることがあります。「**Invites**」というキーワードで端末内検索すると辿りつけます。

### 7-6. ⭐ App ID と App Secret を取得（必須・正しい方を使う）

Threads API 設定画面に **App Secret が 2 種類** 表示されることがあります：

| 表記 | 用途 | Threado で使う？ |
|---|---|---|
| Facebook の App Secret（上段） | Facebook Graph API 用 | ❌ |
| **Threads の App Secret（下段）** | Threads OAuth 専用 | ✅ |

> ⚠️ **下段（Threads の App Secret）を使ってください**。上段（Facebook の App Secret）を入力すると OAuth は通るが Threads API 呼び出しで `Invalid OAuth access token` エラーになります。これも今日（2026-05-19）の検証で判明した知見です。

App ID と Threads の App Secret をメモしておく（次の 7-7 で Threado に入力する）。

### 7-7. Threado でアカウント追加

1. Threado メイン画面 → 「設定」or 「アカウント」タブ
2. 「新規アカウント追加」ボタン
3. 以下を入力：
   - **アカウント名**: 任意（例：`メインアカウント`）
   - **Threads ユーザー名**: 招待した Threads アカウントの @ユーザー名
   - **App ID**: Step 7-6 でメモした値
   - **App Secret**: Step 7-6 の **Threads の App Secret（下段）**
   - **Redirect URI**: `https://<your-domain>/oauth/threads/callback`（Step 7-4 で登録した OAuth Redirect URI と**完全一致**させる）
4. 保存

### 7-8. OAuth 認証を実行

1. アカウント一覧の該当アカウント → 「再認証」または「認証」ボタン
2. **新しいタブ** で Meta の OAuth 同意画面が開く（v3.0 で popup から新タブに変更）
3. ログイン → 「許可する」をクリック
4. Threado の `/oauth/threads/callback` に自動リダイレクト → 「認証完了」表示
5. 元のタブに戻ってリロード → アカウントが「認証済み」表示に

### 7-9. うまくいかないとき（OAuth トラブル早見表）

| エラー | 原因 | 対処 |
|---|---|---|
| `redirect_uri_mismatch` | Threado に入力した Redirect URI と Meta 管理画面の登録 URI が違う | 両者を完全一致させる（末尾スラッシュも統一） |
| `Invalid OAuth access token` | Facebook の App Secret を使っている | Threads の App Secret（下段）に差し替え |
| OAuth 同意画面で「このアプリは未承認です」 | Threads テスター承認をしていない | Step 7-5-b（Threads スマホアプリで承認）を再確認 |
| 「Data Deletion URL not set」 | 3 本のコールバック URL を全部登録していない | Step 7-4 で 3 本全部登録 |
| App が「In Review」のまま | β 期間中は In Review に出す必要なし | Development mode のままで OK |

---

## Step 8：初期設定ウィザード

Threado メイン画面右下の「セットアップマニュアル」ボタンから既存の setup-manual.html を開けます。アカウント追加後の各機能（自律投稿・PDCA・シナリオ・ナレッジ等）の初期設定を順番に進めるためのチェックリストです。

### 推奨初期設定

| 設定 | 推奨値（初期） |
|---|---|
| **Safe mode** | true（最初は ON で動作確認、テキスト投稿が問題なくなったら false） |
| 自律投稿 | 一時 OFF（手動投稿で動作確認後に ON） |
| AI 呼び出し予算 | 100 回/日（デフォルト） |
| 投稿ジャンル | 自分のアカウントのテーマ |
| Target audience | 自分の想定読者層 |
| 投稿時間帯 | アカウント設定の `auto_posting_start` / `auto_posting_end` で指定 |
| Preferred CLI | Claude Code または Codex CLI |

setup-manual.html の各セクションを順番に埋めていくと、安全なデフォルトで動き始められます。

---

## Step 9：動作確認チェックリスト

セットアップが完了したか、以下を全て ✅ できるかチェック：

### 接続まわり

- [ ] `https://<your-domain>/` で SPA トップが開く（HTTPS 鍵マーク付き）
- [ ] ライセンスキー入力後、メイン画面に遷移できる
- [ ] `data/license_state.json` にライセンス情報が保存されている

### Threads 連携

- [ ] アカウント追加成功（OAuth 認証済み表示）
- [ ] Safe mode = true でテスト投稿（シミュレーション）が成功
- [ ] Safe mode = false でテキスト投稿（実 Threads API）が成功
- [ ] 「📷 メディア」ボタンから画像（JPEG/PNG）投稿が成功
- [ ] 動画（MP4）投稿が成功

### バックグラウンド機能

- [ ] PDCA（[設定]→[PDCA]）が起動している
- [ ] 自律投稿（[設定]→[自律投稿]）が起動している（OFF 中でもジョブテーブルにエントリが見える）
- [ ] シナリオリプライ（[シナリオ]タブ）が動作する
- [ ] ナレッジカード（[ナレッジ]タブ）が生成される

### ライセンス

- [ ] ライセンスサーバ heartbeat が成功している（`data/license_state.json` の `last_heartbeat_at` が更新される）
- [ ] read_only モードのテスト：手動で `data/license_state.json` を削除 → ブラウザリロード → ライセンス画面に戻る

これら全てクリアで **β セットアップ完了**。

---

## 自己解決ガイド（β 期間中はサポート不提供のため必須）

### よくあるトラブルと自己解決手順

#### 1. setup.sh の途中で止まる

```bash
sudo journalctl -xe
```

エラーメッセージを Google 検索 → 同様事例を参考に修復。よくある原因：

- apt update がネットワーク問題で失敗 → リトライ
- ARM64 で対応していないパッケージ → 別パッケージ名を試す

#### 2. Caddy が HTTPS 証明書を取得しない

```bash
sudo journalctl -u caddy -f
```

- DNS が反映されていない → 30 分待ってから `sudo systemctl restart caddy`
- Cloudflare Proxy（オレンジ雲）が OFF → ON に変更
- Cloudflare の SSL モードが「Off」になっている → Step 3 を再実行
- VCN / ファイアウォールで 80 ポートが閉じている → Let's Encrypt は HTTP-01 challenge で 80 を使う
- Let's Encrypt のレート制限（同一ドメインで何度も発行を試した）→ 1 時間待つ

#### 3. ライセンス画面が出ない（502 / 404）

```bash
sudo systemctl status threado-main threado-cloud caddy
```

- どれかが `inactive (dead)` なら `sudo systemctl restart <name>` で再起動
- すべて `active (running)` なのに 502 → `sudo journalctl -u threado-cloud -f` でログ確認

#### 4. ライセンスキーが「無効」と表示

- キー入力ミス → 配布メールから再コピー（前後の空白に注意）
- ライセンスサーバ通信不能 → `curl -I https://license.ses-jv.jp/` で確認
- 期限切れ（90 日後）→ β 期間終了、正式版で正規キー切替を待つ

#### 5. OAuth 認証で `redirect_uri_mismatch`

- Threado 内の Redirect URI と Meta 管理画面の登録 URI を完全一致させる
- 末尾スラッシュ、`http` vs `https`、`www` 有無も確認
- Step 7-4 で **3 本全て** 登録したか確認

#### 6. OAuth で「このアプリは未承認です」

- Threads スマホアプリで承認を完了したか（Step 7-5-b）
- App が「Development mode」にあるか（「In review」ではない）
- Threads テスター招待の有効期限切れ → 招待し直す

#### 7. OAuth 後に `Invalid OAuth access token`

- Facebook の App Secret を使っている → **Threads の App Secret（下段）** に差し替え（Step 7-6）

#### 8. 投稿が公開されない

- Safe mode = true になっていないか確認（[設定]→[基本]）
- 該当アカウントの OAuth スコープに `threads_content_publish` があるか
- 投稿ジョブの状態を確認：

```bash
sudo journalctl -u threado-main -f | grep -i "publish\|jobs"
```

#### 9. メディアアップロードが 415 や 413

- 415：JPEG / PNG / WebP / MP4 以外 → 形式変換
- 413：50 MB 超 → 動画の長さ・解像度を下げる
- 400：multipart form の field 名が `file` でない → ブラウザのキャッシュをクリア

#### 10. それでも分からない時

- ネット検索（エラーメッセージ + Threads / Meta / Caddy 等のキーワード）
- 公式ドキュメント：
  - Meta Threads API: https://developers.facebook.com/docs/threads
  - Cloudflare DNS: https://developers.cloudflare.com/dns/
  - Caddy: https://caddyserver.com/docs/
- AI に聞く（Claude / ChatGPT 等。プロンプトに `data/logs/` や `journalctl` のエラーを貼る）

β 期間中（90 日）は開発側へのお問い合わせは受け付けておりませんので、技術知識を活用して自己解決をお願いします。

---

## 運用上の推奨

### バックアップ

`data/` ディレクトリに DB（`threado.db`）・メディア・ライセンス状態がすべて入っています。VM のスナップショット機能（OCI / さくら VPS のコントロールパネル）で定期的にバックアップを取ることを推奨します。

手動バックアップ例：

```bash
cd ~/threado
sudo systemctl stop threado-main
sqlite3 data/threado.db ".backup data/threado-$(date +%Y%m%d).db"
sudo systemctl start threado-main
```

### アップデート（手動 `git pull` + 通知）

β 期間中は手動更新方式：開発側から「アップデート通知」のメール / LINE が届いたら以下を実行。

```bash
cd ~/threado
# DB バックアップ（推奨）
sudo systemctl stop threado-main
sqlite3 data/threado.db ".backup data/threado-$(date +%Y%m%d).db"

# 取得 + 反映
git pull
bash setup.sh   # 依存変更があれば反映される

# 再起動
sudo systemctl restart threado-main threado-cloud
```

### ログ確認

```bash
# Threado メインサーバのログ
sudo journalctl -u threado-main -f

# Threado cloud（ライセンス + メディア）のログ
sudo journalctl -u threado-cloud -f

# Caddy（HTTPS）のログ
sudo journalctl -u caddy -f

# 全部まとめて見る
sudo journalctl -f
```

---

## サポート

β 期間中（90 日）はサポートを提供しておりません。

- ドキュメント: 本ガイド + threado-public Git リポジトリ
- ライセンス期限：配布メール記載の `expires_at` を確認
- β 期間終了後：配布担当から正式版移行案内（正規キー切替）を待つ

---

## 改訂履歴

| 日付 | 内容 |
|---|---|
| 2026-05-17 | 初版下書き |
| 2026-05-19 | **v3.0.0-beta1 β 1 リリース版へ昇格**：9 Step 構成 + 2 パターンドメイン併記 + Meta OAuth App 設定の 3 本コールバック URL / Threads スマホ承認 / Threads App Secret 手順 + 画像/動画投稿の使い方 + 初期設定ウィザード + 動作確認チェックリスト + 自己解決ガイド（10 個の頻出トラブル別手順）+ deploy key 経由 git clone 手順（7 サブステップ） |
