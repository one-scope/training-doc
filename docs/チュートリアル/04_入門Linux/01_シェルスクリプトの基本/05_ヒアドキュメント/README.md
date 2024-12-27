[戻る](../README.md)

# ヒアドキュメント

ヒアドキュメントは、複数行に渡るテキストをコマンドに入力するための機能です。  
ヒアドキュメントは、`<<ラベル` で開始し `ラベル` で終了します。  
`ラベル` には任意の名前を使用できますが、慣習的に `EOS` (End Of String) や `EOF` (End Of File) が使われることが多いです。

次の例ではヒアドキュメントで指定したテキストを `cat` コマンドに入力して表示しています。

```bash
cat <<EOS
Hello, World!
This is a simple example of Here Document.
You can write multiple lines of text here.
EOS
```

また、ヒアドキュメントの中で変数を展開することも可能です。

```bash
name="Alice"
cat <<EOS
Hello, ${name}!
Welcome to the script.
EOS
```

変数の展開を禁止する場合は、ラベルを `''` で囲みます。

```bash
name="Alice"
cat <<'EOS'
Hello, ${name}!
Welcome to the script.
EOS
```

----
[前へ](../04_コマンド置換/README.md) | [次へ](../06_条件分岐とtestコマンド/README.md)
