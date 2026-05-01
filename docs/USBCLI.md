# シリアルコンソールコマンド

Pico の USB CDC/UART から利用するコンソール向けのコマンド一覧です。

## 使い方
- `help` で利用可能コマンドを表示します。

---

## backup
- 概要: SRAM バックアップ領域を操作します。
- 使い方:
  - `backup save` - SRAM 全域 `1MB` を Flash バックアップ領域へ保存
  - `backup restore` - Flash バックアップ領域の内容を SRAM 全域 `1MB` へ復元
  - `backup erase` - Flash バックアップ領域 `1MB` を全消去
- 備考:
  - 確認無くバックアップ/リストアが実行されるので注意ください。
  - データの有効性チェックが無いので、未初期化のバックアップ領域でも `restore` はそのまま実行されます。

---

## factory_reset
- 概要: 設定を全消去して再フォーマットし、保存データを初期化します。
- 使い方:
  - `factory_reset` - 使い方を表示
  - `factory_reset confirm` - ファクトリーリセットを実行

---

## help
- 概要: 利用可能なコマンドを一覧表示します。

---

## hostname
- 概要: mDNS ホスト名の表示・変更を行います。
- 使い方:
  - `hostname` - 現在のホスト名を表示
  - `hostname <name>` - ホスト名を保存
- 備考:
  - 設定したホスト名 + `.local` でアクセスできるようになります。
  - 未設定時のデフォルト値は `eseram` です。

---

## ip
- 概要: ネットワーク設定の表示・変更を行います。
- 使い方:
  - `ip show` - 現在の IP 設定を表示
  - `ip dhcp` - DHCP モードへ切替
  - `ip static <address> <netmask> <gateway> <dns>` - 固定 IP に設定

---

## ping
- 概要: ICMP ping を実行します（IPv4 のみ）。
- 使い方: `ping <ip>`

---

## set_wifi
- 概要: Wi-Fi の SSID/パスワードを保存します。
- 使い方: `set_wifi <ssid> <password>`
- 備考: 保存後に Wi-Fi 設定を再読み込みします。

---

## sram
- 概要: SRAM 操作コマンドです。
- 使い方:
  - `sram read ADDR` - 指定アドレスの読み出し
  - `sram write ADDR VALUE` - 指定アドレスへ書き込み
  - `sram dump ADDR` - 256 バイトのダンプ
  - `sram fill ADDR SIZE VALUE` - 範囲を値で埋める
  - `sram check [CS_MASK [TEST_MASK [SIZE]]]` - SRAM チェック
  - `sram test {fill|check}` - テストパターンで fill/check

---

## status
- 概要: Wi-Fi と CPLD の状態を表示します。
- Wi-Fi の出力:
  - `Wi-Fi: Not Initialized`
  - `Wi-Fi: Not Connected`
  - `Wi-Fi: Connecting`
  - `Wi-Fi: Connected (No IP)`
  - `Wi-Fi: Connected (IP: <ip>)`
- CPLD の出力（読み取り成功時）:
  - `CPLD Status: 0xXX`
  - `ENABLE: ON|OFF`
  - `MODE: Flat ROM` または `MODE: Megarom 8k|16k bank`
  - `WriteProtect: ON|OFF`
  - `MSX Power: ON|OFF`

---

## ver
- 概要: CPLD と Pico ファームウェアのバージョンを表示します。
- 出力例:
  - `CPLD: v<数字>`（読み取り失敗時は `CPLD: unknown`）
  - `pico: <VERSION>`

---
