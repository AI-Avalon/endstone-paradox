# Paradox Native 2.0.1
*他の言語で読む: [English](README.md).*

WindowsおよびLinux x86-64上の **Endstone 0.11.11 / BDS 1.26.51.1 (protocol 2193)** 向けの、C++23アンチチート監視・モデレーション・管理プラグイン。[Visual1mpactのParadox](https://github.com/Visual1mpact/Paradox_AntiCheat)をベースとし、v6.9.1までレビュー済みです。

このプラグインはネイティブの `.dll` または `.so` として動作します。Endstone自体は通常のランタイムとブートストラップを使用します。Python版の実装は [`legacy/python`](legacy/python) にアーカイブされています。両方の実装を同時に読み込まないでください。

**[安定版 v2.0.1 をダウンロード](https://github.com/TheNINJALLO/endstone-paradox/releases/tag/v2.0.1)** · **[ドキュメント](https://theninjallo.github.io/endstone-paradox/)** · **[リリースノート](native/RELEASE_NOTES.md)**

以前のv2.0.0プレビューからのアップグレード: v2.0.1では、ラップされたプレイヤーのコマンド認証と時期尚早なAFK初期化を修正しています。[インストールガイド](https://theninjallo.github.io/endstone-paradox/#/gettingstarted)、[設定リファレンス](https://theninjallo.github.io/endstone-paradox/#/configuration)、[コマンドリファレンス](https://theninjallo.github.io/endstone-paradox/#/commands/moderation)、および[全54モジュールのページ](https://theninjallo.github.io/endstone-paradox/#/modules/overview)にネイティブリリースに関する記載があります。

## インストール・移行方法

1. サーバーを停止し、`plugins/paradox` およびワールドのバックアップを取得します。
2. `plugins` フォルダから古い Paradox の wheel ファイルを削除するか、Endstone が使用している Python 環境から `endstone-paradox` をアンインストールします。
3. 安定版リリースからプラットフォームに合った ZIP と SHA-256 ファイルをダウンロードし、チェックサムを検証して展開します。アーカイブ内の `plugins/` フォルダにある **いずれか1つ** のネイティブバイナリをサーバーの `plugins/` にコピーします (Windows の場合は `endstone_paradox.dll`、Linux の場合は `endstone_paradox.so`)。Linux では OpenSSL 3 (`libssl3` / `libssl3t64`) が必要です。アップグレードの際は以前の Paradox バイナリを削除してください。
4. 既存の `plugins/paradox/config.toml` と `paradox.db` はそのまま保持します。ローダーは SQLite の整合性バックアップ API を使用して `paradox.db.pre-native.bak` を作成します。既存のテーブルはそのまま維持されます。
5. Endstone を起動し、`Paradox 2.0.1 native enabled; 54 modules` と表示されるか確認します。サーバーはプロトコル 2193 をサポートしている必要があります。コンソールから `ac-about` と `ac-debug-db` が機能します。
6. スタッフメンバーがオンラインの状態で、コンソールからクリアランス（権限）を付与します: `ac-setclearance "Player Name" 4`。サーバーポリシーを有効にする前に、[移行ノート](native/MIGRATION.md) を確認してください。

同梱されているBDS ZIPは検証用入力ファイルであり、**再配布はされません**。[`verify-server.py`](native/tools/verify-server.py) は、ピン留めされた公式のメタデータに対してアーカイブと実行可能ファイルの両方をチェックします。

## ラグと誤検知について

高い、または未知のping、ジッター、サーバーティックの遅延、パケットのバーストや欠損、チャンクの未読み込み、参加、テレポート、リスポーン、ディメンション移動、ノックバック、エフェクト、および特殊な移動の発生時は、移動検知が一時停止します。これにより、以前のサンプルがクリアされ、安定した更新がある回復期間が必要になります。

ヨー(視点)の変更なしや直線的な移動は、自動経路の違反として判定されることはありません。物理、エイム、クリック、採掘、およびインベントリのヒューリスティックによる監視結果は記録されますが、それらが繰り返されたからといってキックやBANに直接繋がることはありません。タイマー違反や度重なる極端なリーチ攻撃については、健全な証拠が必要です。不正な入力はキャンセルされる場合がありますが、自動的にBANされることはありません。**本システムによる自動BANは存在しません。**

`soft` はデフォルトの強制モードです。`logonly` は処理を行わずに結果を記録します。`hard` は、裏付けのある証拠が繰り返し確認された場合にキックを許可します。スタッフによる明示的なBAN、ホワイトリストやロックダウン制限、AFK設定、および土地やコンテナのポリシーは、管理者の個別の操作として扱われます。

リリース検証には、ネイティブ環境での回帰テスト、実際のBDS統合テスト、およびWindowsとLinuxでのゲームプレイ、レイテンシ、ジッター、パケットロス、通信切断、復旧をテストする2つのスクリプトクライアントが含まれています。ただし、これらはすべてのクライアント環境、カスタムアイテム、物理的相互作用、またはネットワーク条件で誤検知がないことを保証するものではありません。`hard` 強制モードを適用する前に、自身のゲームプレイ環境でテストを行ってください。詳細は[モジュール監査](native/MODULE_AUDIT.md)および[検証記録](native/VALIDATION.md)を参照してください。

## 共通コマンド

スペースを含むプレイヤー名は引用符で囲んでください。

| コマンド | 目的 |
| --- | --- |
| `/ac-gui`, `/ac-guiitem` | ゲーム内コントロールとメニューコンパス |
| `/ac-modules`, `/ac-modstate <module> on\|off` | 54個のモジュールの確認/設定 |
| `/ac-mode soft\|hard\|logonly` | 強制モードの設定 |
| `/ac-case <player>`, `/ac-history <player>` | 保存された検出結果の確認 |
| `/ac-evidencereplay <player>` | 最新の位置情報リプレイの確認 |
| `/ac-watch <player> [seconds]` | プレイヤーに対するレビューアラートの受信 |
| `/ac-exempt <player> <module\|all> [seconds]` | 特定のチェックの一時停止 |
| `/ac-setclearance <player> <1..4>` | スタッフクリアランスの割り当て |
| `/ac-ban`, `/ac-unban`, `/ac-kick`, `/ac-freeze`, `/ac-punish` | 手動モデレーション |
| `/ac-allowlist add\|remove\|list <player>` | 認証履歴を使用した検出免除 |
| `/ac-whitelist add\|remove\|list\|on\|off` | オフラインプレイヤーを含むアクセスポリシー |
| `/ac-lockdown on\|off [kick]` | 新規参加のロック。`kick` が明示されない限りオンラインプレイヤーは保持されます |
| `/ac-home set\|delete\|list\|tp [name]`, `/ac-waypoint ...` | 座標とディメンション付きのロケーション |
| `/ac-tpa <player>`, `/ac-tpa accept\|deny`, `/ac-tpr [radius]` | テレポートリクエストと安全なランダムテレポート |
| `/ac-pvp [on\|off\|status\|global]` | 個人/サーバーのPvPポリシーと戦闘クールダウン |
| `/ac-landclaim create\|delete\|list\|trust\|untrust [name] [value]` | 土地ポリシー。クレイム作成前に有効にしてください |
| `/ac-chunkborders`, `/ac-worldborder <radius> [x] [z]` | チャンク表示と円形ワールドボーダー |
| `/ac-invsee <player>`, `/ac-inventory-editor <player> <slot> clear\|<item> [amount]` | インベントリ管理 |
| `/ac-invclone <player>`, `/ac-vanish`, `/ac-rank <player> <rank>` | スタッフユーティリティ |
| `/ac-ping`, `/ac-tps`, `/ac-debug-db`, `/ac-about` | 診断 |

モデレーションにはクリアランス3、または対応する `paradox.*` 権限が必要です。セキュリティや設定の変更にはクリアランス4、またはスタッフ権限が必要です。Endstoneオペレーターはデフォルトでスタッフ権限が付与されます。通常のユーティリティ権限はデフォルトで true、`paradox.bypass` はデフォルトで false です。ネイティブハンドラーは GUI とウェブコマンドのルーティングのために権限を再確認します。

## Webインターフェースと統合機能

新規インストールの場合、`127.0.0.1:8080` にバインドされます。`plugins/paradox/web-token.txt` からアクセストークンを読み取り、接続に使用してください。トークンによって管理者アクセスが許可されます。既存のホスト/ポート設定は保持されます。リモートアクセスの場合は、リバースプロキシでHTTPS終端を行うか、SSHトンネルを使用してください。

ダッシュボードには、オンラインの接続健全性、モジュールの状態、最近の証拠が表示され、Paradoxコマンドをサーバーのスレッドにキューイングします。SQLiteへの書き込みとHTTPS統合は、制限されたワーカー上で実行されます。グローバル統合機能は新規インストール時には無効です。利用する場合はHTTPSの `api_url` とオプションの `api_key` を明示的に設定してください。名前のみのリモートレコードはレビュー用証拠として残るだけで、プレイヤーをBANすることはありません。Discordへの通知は設定されたHTTPS Webhookを使用します。詳細は[設定と移行](native/MIGRATION.md)を参照してください。

## ビルドと出典

[native/BUILDING.md](native/BUILDING.md) を参照してください。CMakeによって依存関係が固定されています。デコーダーは割り当て/深度検査の予算を適用します。[`references.lock.json`](native/references.lock.json) は、要求された7つのEndstoneリポジトリ、アップストリームのParadox、および両方のサーバーチェックサムを記録しています。

EndstoneのネイティブプラグインAPIは、サーバーフックとABI境界を提供します。このポートは、プライベートBDSオフセットを推測したり、2つ目のデツアーセットをインストールしたりしません。提供されているLinuxバイナリはストリップされているため、`dwarf2cpp` はそこから完全なプライベートクラスヘッダーを生成することはできません。この制約とプロトコルダンパーの証拠は [VALIDATION.md](native/VALIDATION.md) に記録されています。

ドキュメントサイトとWikiのエクスポートは `docs/` のソースを共有しています。モジュールページは、監査済みのネイティブレジストリから生成されます。`python native/tools/sync-docs.py --check` を実行して整合性を確認してください。編集とWikiの公開については、[ドキュメントの保守](docs/maintenance.md)を参照してください。

GPL-3.0-or-later。オリジナルParadox 作成者: Visual1mpact、Endstoneポート 作成者: TheNINJALLO。サードパーティへの通知は [`native/licenses`](native/licenses) にあります。
