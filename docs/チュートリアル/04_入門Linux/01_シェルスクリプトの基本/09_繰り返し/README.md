[戻る](../README.md) | [NotebookLM を開く](https://notebooklm.google.com/notebook/e315c1c1-2f3c-4d5e-bcab-34cb69a97938)

# 繰り返し

## `for` ループ

シェルスクリプトでの `for` ループは次のように記述します。  
`リスト` はスペース区切りの数値や文字列です。

```bash
for 変数 in リスト; do
  # 繰り返し実行する処理
done
```

次の例では、`1` から `5` までの数字を順番に表示します。

```bash
for i in 1 2 3 4 5; do
  echo "数字: ${i}"
done
```

`seq` コマンドを使って数字のリストをつくることもできます。

```bash
for i in $(seq 1 5); do
  echo "数字: ${i}"
done
```

数字のリストをつくるにはブレース展開も利用できます。

```bash
for i in {1..5}; do
  echo "数字: ${i}"
done
```

ワイルドカードを使用してディレクトリ内のファイルに対して処理を実行することも可能です。

```bash
for filename in *.txt; do
  echo "テキストファイル: ${filename}"
done
```

### `break` と `continue`

シェルスクリプトの `for` ループでも、`break` と `continue` が使えます。  
`break` は `for` ループ全体を終了し、`continue` は `for` ループ内の処理をスキップして次の繰り返しに進みます。

次の例では、`break` を使用して `i` が `3` のときに `for` ループを終了するようにしています。  
出力結果は `1 2` です。

```bash
for i in {1..5}; do
    if [ ${i} -eq 3 ]; then
        break  # i が 3 のとき、for ループを終了する
    fi
    echo ${i}
done
```

一方、次の例では、`continue` を使用して `i` が `3` のときにループ内の処理をスキップして次の繰り返しに進むようにしています。  
出力結果は `1 2 4 5` です。

```bash
for i in {1..5}; do
    if [ ${i} -eq 3 ]; then
        continue  # i が 3 のとき、以降の処理をスキップして次の繰り返しに進む
    fi
    echo ${i}
done
```

----
[前へ](../08_ブレース展開/README.md) | [次へ](../10_シェルオプション/README.md)
