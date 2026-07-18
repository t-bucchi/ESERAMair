# TCP/IP UNAPI support

> [!Warning]
> ESERAMair による TCP/IP UNAPI は実験的なリリースです。仕様や動作は今後変更される可能性があります。

## TCP/IP UNAPI とは？

TCP/IP UNAPI とは、Nextorの作者の Konamiman さんが策定した MSX 用のネットワークスタックのAPI仕様です。
このAPIを実装することで MSX の TCP/IP UNAP 対応の各種コマンドからネットワーク機能を使うことができるようになります。

ちょっとややこしいのですが、ネットワークAPIを「UNAPI」と簡略化して表現することも多いのですが、厳密には UNAPI は、MSXの汎用的なAPI仕様のことを指します。
さらに、ネットワーク関連のUNAPIとして、Ethernet UNAPI と TCP/IP UNAPI があります。

### UNAPI とは？

UNAPI は上記の通り、MSXの拡張機能を呼び出す汎用的なAPI仕様です。IDを使って拡張機能を検索し、それを呼び出すための仕様が策定されており、ネットワーク機能に限らず、様々な拡張機能を提供できるようになります。

詳しくは以下のドキュメントを参照ください。
- [MSX UNAPI specification 1.1](https://github.com/Konamiman/MSX-UNAPI-specification/blob/eb7e978c4c2133bf5d31a470281940f5ee44c4f5/docs/MSX%20UNAPI%20specification%201.1.md)
- [Introduction to MSX-UNAPI](https://github.com/Konamiman/MSX-UNAPI-specification/blob/eb7e978c4c2133bf5d31a470281940f5ee44c4f5/docs/Introduction%20to%20MSX-UNAPI.md)

### Ethernet UNAPI, TCP/IP UNAPI とは？

Ethernet UNAPI と TCP/IP UNAPI は、どちらも UNAPI 準拠の API です。
Ethernet UNAPI はリンク層相当の機能を提供する API であり、TCP/IP UNAPI はアプリケーションに TCP, UDP, DNS, PING などのネットワーク機能を提供する APIです。

- [Ethernet UNAPI specification](https://github.com/Konamiman/MSX-UNAPI-specification/blob/eb7e978c4c2133bf5d31a470281940f5ee44c4f5/docs/Ethernet%20UNAPI%20specification%201.1.md)
- [TCP-IP UNAPI specification](https://github.com/Konamiman/MSX-UNAPI-specification/blob/eb7e978c4c2133bf5d31a470281940f5ee44c4f5/docs/TCP-IP%20UNAPI%20specification.md)

どちらもネットワーク機能を提供するため混同されがちですが別物です。
Ethernet UNAPI は NIC のドライバ相当で、TCP/IP などのプロトコル機能を持ちません。

そのため、Ethernet UNAPI のみが実装された Ethernet カートリッジで TCP/IP の通信を行うには、InterNestor Lite (INL.COM)というプロトコル・スタックを常駐させる必要があります。
常駐させると、InterNestor Lite が TCP-IP UNAPI を提供するようになり、TCPなどのプロトコルを使えるようになります。

## ESERAMair の TCP/IP UNAPI サポート

ESERAMair の Nextor driver では Ethernet UNAPI は提供せず、TCP/IP UNAPI を直接提供してます。
そのため、INL.COM を常駐させる必要はありません。

現在実装されている機能です。
ただ、実験的な実装なので不安定です。

**実装済**
- IP アドレスやネットワーク状態の取得
- PING（ICMP echo）
- DNS による名前解決
- UDP 通信
- TCP 通信
- DHCP / 固定 IP などのネットワーク設定

**未実装**
- RAW IP
- TCP passive (TCPサーバ機能)

PING.COM や HGET.COM, HUB.COM などは動作することを確認していますが、やや不安定です。
[konamiman さんのページ](https://www.konamiman.com/msx/msx-e.html#networking) にアプリケーションが公開されていますので使ってみてください。
