[戻る](../README.md)

# `curl` コマンドの基本

- [`curl` コマンドの基本](#curl-コマンドの基本)
  - [`curl` コマンドとは](#curl-コマンドとは)
  - [基本的な使いかた](#基本的な使いかた)
    - [主なオプション](#主なオプション)
  - [curl コマンドの使用例](#curl-コマンドの使用例)
    - [ファイルのダウンロード](#ファイルのダウンロード)
    - [REST API の実行](#rest-api-の実行)

## `curl` コマンドとは

`curl` コマンドは、URL を指定してインターネット上のサーバーとデータをやり取りするコマンドです。  
インターネット上のファイルのダウンロードや、REST API のクライアントとして使用されます。

## 基本的な使いかた

`curl` コマンドの基本的な構文は次のとおりです。

```bash
curl [オプション] URL
```

### 主なオプション

| オプション                       | 意味                                                                                                                                                            |
| -------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `-X {メソッド}`                  | HTTPリクエストのメソッドを指定する<br/>`{メソッド}` には `GET`、`POST`、`PUT`、`DELETE`、`HEAD`、`OPTIONS` などを指定する                                       |
| `-H "{ヘッダー名}: {値}"`        | HTTPリクエストのヘッダーに `{ヘッダー名}: {値}` のヘッダーを追加する                                                                                            |
| `-L`                             | リダイレクト（ステータスコード `3xx`）を自動的に追跡する                                                                                                        |
| `-d "{データ}"`                  | HTTPリクエストのボディに `{データ}` を指定する                                                                                                                  |
| `-O`                             | 指定した URL からファイルをダウンロードし、URL のファイル名で保存する                                                                                           |
| `-o {ファイル名}`                | 指定した URL からファイルをダウンロードし、`{ファイル名}` のファイル名で保存する                                                                                |
| `-u "{ユーザー名}:{パスワード}"` | Basic 認証をサポートするサービスに認証情報を指定する<br/>ユーザー名だけを指定することで、インタラクティブにパスワードを入力できる（履歴にパスワードが残らない） |
| `--compressed`                   | サポートされているエンコーディング形式（`gzip` や `deflate` など）のレスポンスを自動でデコードする                                                              |
| `-v`                             | リクエストとレスポンスの詳細な情報を表示する                                                                                                                    |
| `-f`                             | HTTPリクエストが失敗（ステータスコード `4xx`、`5xx`）した場合にエラーを表示する                                                                                 |
| `-s`                             | リクエスト結果のみを表示し、他の情報（表示進行状況やエラーメッセージ、警告など）を表示しない                                                                    |
| `-S`                             | `-s` を指定したとき、エラー時にエラーメッセージを表示する                                                                                                       |

## curl コマンドの使用例

### ファイルのダウンロード

ファイルをダウンロードする

```bash
curl -LO "https://example.com/assets/icon.png"
```

`tar.gz` ファイルをダウンロードしながら展開する

```bash
curl -fsSL --compressed "https://example.com/assets/archive.tar.gz" | tar tar -xz -C /tmp
```

### REST API の実行

POST メソッドの REST API を実行する

```bash
curl -X POST \
  -H "Accept: application/json" \
  -H "Content-Type: application/json" \
  -d '{"id": "alice", "name": "Alice"}' \
  "https://example.com/rest/v1/users"
```

GET メソッドの REST API を実行する

```bash
curl -X GET \
  -H "Accept: application/json" \
  "https://example.com/rest/v1/users/alice"
```

PUT メソッドの REST API を実行する

```bash
curl -X PUT \
  -H "Accept: application/json" \
  -H "Content-Type: application/json" \
  -d '{"name": "Alice"}' \
  "https://example.com/rest/v1/users/alice"
```

DELETE メソッドの REST API を実行する

```bash
curl -X DELETE \
  -H "Accept: application/json" \
  "https://example.com/rest/v1/users/alice"
```

認証情報（Basic 認証）を指定して REST API を実行する

```bash
curl -X GET \
  -u alice \
  -H "Accept: application/json" \
  "https://example.com/rest/v1/users/alice"
```

認証情報（Bearer トークン）を指定して REST API を実行する

```bash
curl -X GET \
  -H "Authorization: Bearer your_token" \
  -H "Accept: application/json" \
  "https://example.com/rest/v1/users/alice"
```
