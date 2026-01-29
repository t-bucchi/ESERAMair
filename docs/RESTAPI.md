# REST API仕様

ESERAMair は REST API を実装しています。

WebUI で SRAM の内容を操作するために作ったものですのであまり充実はしていませんが、
面白い使いみちがあるかもしれないので公開します。

# 注意事項

- Raspberry Pi Picoで動かしていることもあり、認証や暗号化は行っていません。<br />
閉じられたネットワークで使うなど、セキュリティ対策はネットワーク環境側で行ってください。
- 一度に1つのリクエストしか受け付けられません
- パラメータのチェックなどは最低限しかしていないので、あまり無茶なリクエストを投げたりしないでください
- Pico がハングしたら、MSXの電源をOFF/ONしてください。MSXのリセットでは Pico はリセットされないため

# エンドポイント

https ではなく http で、ESERAMair の IPアドレスの ポート 8080 にアクセスします。

例:
```
$ curl http://192.168.1.100:8080/hello
{ "message": "Hello, world!!" }
```

## GET /hello
- テスト用のエンドポイントです。固定メッセージを返します。
- Response:
  - 200 OK
  - Content-Type: application/json
  - Body: `{ "message": "Hello, world!!" }`


## GET /ram
- 1MBのSRAMの内容をバイナリで取得します。
- Parameters:
  - start: 開始アドレス（省略時0）
  - size: サイズ (省略時は末尾まで)
  - filename: 省略可
- Response:
  - 200 OK
  - Content-Type: application/octet-stream
  - Body: SRAMデータ
- Error:
  - 400 Bad Request: `start` が範囲外など
  - 500 Internal Server Error: バス制御取得失敗など



## POST /ram
- SRAMにデータを書き込みます。
- Parameters:
  - start: 書き込み開始アドレス
- Request:
  - Body: data to write
  - Headers:
    - Content-Length: 必須（書き込みサイズ）。`start + Content-Length <= TOTAL_RAM_SIZE` を満たす必要あり。
- Response:
  - 200 OK
  - Content-Type: application/json
  - Body: `{}`
- Error:
  - 400 Bad Request: `start` が範囲外など
  - 500 Internal Server Error: バス制御取得失敗など



## GET /fat/status
- FATドライバおよびマウント状態を返します（状態照会エンドポイント）。
- ポリシー: 状態判定が可能な限りは常に 200 を返し、詳細は Body で表現します。
- Response:
  - 200 OK
  - Content-Type: application/json
  - Body（例）:
    - ドライバなし: `{"driver":false, "mountable":false, "reason":"no_driver"}`
    - ドライバあり・マウント不可: `{"driver":true, "mountable":false, "reason":"mount_failed"}`
    - マウント可能（容量取得成功）:
      ```json
      {
        "driver": true,
        "mountable": true,
        "total_size": 917504,
        "free_size": 10240,
        "fat_type": "FAT12"
      }
      ```
    - マウント可能（容量取得失敗）: `{"driver":true, "mountable":true, "reason":"getfree_failed"}`
- フィールド:
  - `driver` (bool): NEXTOR ドライバ検出可否
  - `mountable` (bool): FAT のマウント可否
  - `reason` (string, 任意): 追加情報。`no_driver` / `mount_failed` / `getfree_failed` など
  - `total_size`, `free_size`, `fat_type`: `mountable=true` かつ容量取得成功時のみ付与
- エラー（500）:
  - 500 Internal Server Error: 状態自体が判定不能な内部障害（例: `{"error":"bus_timeout"}`）



## GET /fat/files
- FATファイルシステムのファイル/ディレクトリ一覧をJSONで返します。
- Query Parameters:
  - `path` (optional): 取得するパス。未指定時はルート（`/`）。
- Response:
  - 200 OK
  - Content-Type: application/json
  - Body:
    ```json
    [{
      "name":"DIR1",
      "size":0,
      "date":"2001/01/01 01:01:01",
      "type":"dir"
    },{
      "name":"FILE1.BIN",
      "size":123,
      "date":"2001/01/01 01:01:01",
      "type":"file"
    }, ...]
    ```
- Error:
  - 400 Bad Request: `path` に `.` や `..` が含まれてるなど、リクエストが不正
  - 404 Not Found: ファイルやディレクトリが存在しない
- 備考:
  - 日付フォーマットは `YYYY/MM/DD HH:MM:SS`（日付情報が無い場合は空文字列）。
  - 返却されるのは指定ディレクトリの直下のみ。`..` などの仮想エントリは含まれない。



## GET /fat/files/&lt;filename&gt;
- 指定ファイルをダウンロードします。`<filename>` にはサブディレクトリを含めることができます（例: `DIR1/FILE.BIN`）。
- Response:
  - 200 OK
  - Content-Type: application/octet-stream
- Error:
  - 404 Not Found: ファイル未発見
  - 400 Bad Request: パスが不正、またはディレクトリを指定した場合



## POST /fat/files/&lt;filename&gt;
- 指定ファイルにアップロードします。`<filename>` にはサブディレクトリを含めることができます。親ディレクトリは事前に存在している必要があります。
- Request:
  - Body: data to write
- Response:
  - 200 OK
  - Content-Type: application/json
  - Body: `{}`
- Error:
  - 400 Bad Request: パスが不正、またはディレクトリを指定した場合
  - 404 Not Found: 親ディレクトリが存在しない



## POST /fat/directories/&lt;dirname&gt;
- 指定パスにディレクトリを作成します。`<dirname>` にはサブディレクトリを含めることができます。親ディレクトリは事前に存在している必要があります。
- Request:
  - Body: なし
- Response:
  - 200 OK
  - Content-Type: application/json
  - Body: `{ "result": "created" }`
- Error:
  - 400 Bad Request: パスが不正
  - 404 Not Found: 親ディレクトリが存在しない
  - 409 Conflict: 同名のファイルまたはディレクトリが既に存在する
  - 500 Internal Server Error: FAT 操作に失敗した



## DELETE /fat/files/&lt;filename&gt;
- 指定ファイルまたはディレクトリを削除します。`<filename>` にはサブディレクトリを含めることができます。
- ディレクトリを指定した場合は配下のファイル・ディレクトリを含めて再帰的に削除されます。
- Response:
  - 200 OK
  - Content-Type: application/json
  - Body: `{ "result": "deleted" }`
- Error:
  - 400 Bad Request: パスが不正
  - 404 Not Found: 指定パスが存在しない
  - 500 Internal Server Error: FAT 操作で削除できなかった


## POST /fat/format
- FATファイルシステムをフォーマットします。
- Request:
  - Body: なし
- Response:
  - 200 OK: フォーマット成功（Body: `{"result":"formatted"}`）
  - 500 Internal Server Error: フォーマット失敗
