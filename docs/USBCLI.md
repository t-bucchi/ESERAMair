# シリアルコンソールコマンド

Pico の USB CDC/UART から利用するコンソール向けのコマンド一覧です。

## 使い方
- `help` で利用可能コマンドを表示します。

---

## ver
- 概要: CPLD と Pico ファームウェアのバージョンを表示します。
- 出力例:
  - `CPLD: v<数字>`（読み取り失敗時は `CPLD: unknown`）
  - `pico: <VERSION>`

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

## help
- 概要: 利用可能なコマンドを一覧表示します。

---

## set_wifi
- 概要: Wi-Fi の SSID/パスワードを保存します。
- 使い方: `set_wifi <ssid> <password>`
- 備考: 保存後に Wi-Fi 設定を再読み込みします。

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

## sram
- 概要: SRAM 操作コマンドです。
- 使い方:
  - `sram read ADDR` - 指定アドレスの読み出し
  - `sram write ADDR VALUE` - 指定アドレスへ書き込み
  - `sram dump ADDR` - 256 バイトのダンプ
  - `sram fill ADDR SIZE VALUE` - 範囲を値で埋める
  - `sram check [CS_MASK [TEST_MASK [SIZE]]]` - SRAM チェック
  - `sram test {fill|check}` - テストパターンで fill/check
