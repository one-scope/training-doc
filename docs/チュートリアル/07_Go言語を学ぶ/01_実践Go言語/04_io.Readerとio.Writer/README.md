[戻る](../README.md) | [NotebookLM を開く](https://notebooklm.google.com/notebook/72ccb774-4924-45b5-9241-c5b183370e0e)

# `io.Reader` と `io.Writer`

Go 言語の `io.Reader` と `io.Writer` は、データの読み書きを抽象化するインターフェースであり、標準ライブラリや独自実装を通じて柔軟なデータ処理を可能にします。

## ストリーム

ストリーム（Stream）は、データを順番に処理するための概念です。  
すべてのデータを一度にメモリに読み込まず、小さな単位で少しずつ逐次処理していくことで、メモリに収まらないような巨大なデータであっても一定のメモリ使用量で取り扱うことができます。  
また、ストリームを別のストリームに受け渡すことで、メモリ使用量を一定に保ちつつデータの変換も可能です。さまざまな種類のストリームを組み合わせることで、柔軟にデータ変換のパイプラインを組み立てることが可能になります。  
Go 言語の標準ライブラリでは、`io.Reader` や `io.Writer` がストリームの典型的な例です。

## `io.Reader` と `io.Writer` の基本

`io.Reader` はデータを読み込むストリーム（入力ストリーム）を、`io.Writer` はデータを書き込むストリーム（出力ストリーム）を表現するインターフェースです。  
`io.Reader` や `io.Writer` を利用することで、ファイルやネットワーク、メモリなどさまざまなデータソースのストリームを統一的に取り扱うことができます。  
また、`io.Reader` や `io.Writer` を満たすことで、さまざまなデータ変換を行うストリームも実装可能です。  
たとえば、標準ライブラリでは GZIP 圧縮をしながら書き込む `gzip.Writer` などがあります。

Go 言語の `io.Reader`、 `io.Writer` は次のように定義されています。  
それぞれのインターフェースを満たした型を `io.Reader`、`io.Writer` として取り扱うことができます。

```go
type Reader interface {
    Read(p []byte) (n int, err error)
}

type Writer interface {
    Write(p []byte) (n int, err error)
}
```

## 標準ライブラリでの利用例

標準ライブラリでは次のような型が `io.Reader` や `io.Writer` を満たしています。

### `io.Reader` を満たす型の例

| 型                    | 概要                                 |
| --------------------- | ------------------------------------ |
| `*bytes.Buffer`       | メモリ上のバイトバッファへの読み取り |
| `*bytes.Reader`       | メモリ上のバイト列の読み取り         |
| `*strings.Reader`     | メモリ上の文字列の読み取り           |
| `*bufio.Reader`       | バッファ付きのリーダー               |
| `*os.File`            | ファイルの読み取り                   |
| `*http.Request.Body`  | HTTPリクエストのボディの読み取り     |
| `*http.Response.Body` | HTTPレスポンスのボディの読み取り     |

### `io.Writer` を満たす型の例

| 型                    | 概要                                 |
| --------------------- | ------------------------------------ |
| `*bytes.Buffer`       | メモリ上のバイトバッファへの書き込み |
| `*bufio.Writer`       | バッファ付きのライター               |
| `*os.File`            | ファイルへの書き込み                 |
| `http.ResponseWriter` | HTTPレスポンスの書き込み             |
| `*log.Logger`         | ログ出力                             |

これらの型は `io.Reader` や `io.Writer` を受け取る関数で統一的に取り扱うことができます。  
たとえば、標準ライブラリでは次のような関数が `io.Reader` や `io.Writer` を受け取ります。

### `io.Reader` や `io.Writer` を扱う関数

| 関数                                                                     | 概要                                                                                                      |
| ------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------- |
| `io.ReadAll(r io.Reader) ([]byte, error)`                                | `io.Reader` からすべてのデータを読み取る                                                                  |
| `bufio.NewReader(r io.Reader)`                                           | `io.Reader` にバッファリングを追加した `io.Reader` をつくる                                               |
| `io.Copy(dst io.Writer, src io.Reader) (written int64, err error)`       | `src` の `io.Reader` から `dst` の `io.Writer` へデータをコピーする                                       |
| `io.MultiWriter(w ...io.Writer) io.Writer`                               | 複数の `io.Writer` をまとめ、一度の書き込みですべての `io.Writer` にデータを書き込む `io.Writer` をつくる |
| `fmt.Fprintf(w io.Writer, format string, a ...interface{}) (int, error)` | 指定したフォーマットにしたがって、`io.Writer` に書き込む                                                  |
| `json.NewDecoder(r io.Reader) *json.Decoder`                             | `io.Reader` から JSON をデコードする                                                                      |

このように、無数にある具体的な入力ストリームや出力ストリームを `io.Reader`、`io.Writer` として抽象化し統一的に取り扱うことで、ストリームの具体的な実装に依存せずさまざまな操作を組み合わせることができるようになります。

## 独自の `io.Reader` と `io.Writer` の実装

`io.Reader` や `io.Writer` を満たす実装をすることで、特定のデータ処理をカスタマイズできます。

### `io.Reader` を実装する

以下は、元の `io.Reader` から読み取ったデータを小文字に変換して読み込む `io.Reader` の実装例です。

```go
import io

type LowercaseReader struct {
    r io.Reader
}

func (l *LowercaseReader) Read(p []byte) (int, error) {
    n, err := l.r.Read(p)
    for i := 0; i < n; i++ {
        // ASCII 大文字 'A'-'Z' を小文字 'a'-'z' に変換する
        if p[i] >= 'A' && p[i] <= 'Z' {
            p[i] = p[i] - 'A' + 'a'
        }
    }
    return n, err
}
```

### `io.Writer` を実装する

以下は、書き込まれたデータを大文字に変換しながら元の `io.Writer` に書き込む `io.Writer` の実装例です。

```go
import io

type UppercaseWriter struct {
    w io.Writer
}

func (u *UppercaseWriter) Write(p []byte) (int, error) {
    converted := make([]byte, len(p))
    for i, b := range p {
        if b >= 'a' && b <= 'z' {
            // 'a' - 'A' の差分を引いて大文字に変換
            converted[i] = b - ('a' - 'A')
        } else {
            converted[i] = b
        }
    }
    return u.w.Write(converted)
```

----
[前へ](../03_インターフェースによる抽象化/README.md) | [次へ](../05_errorの判定と伝搬方法/README.md)
