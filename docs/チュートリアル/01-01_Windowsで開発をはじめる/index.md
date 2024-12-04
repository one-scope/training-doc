[戻る](../../../README.md)

# 01-01. Windows で開発をはじめる

- [01-01. Windows で開発をはじめる](#01-01-windows-で開発をはじめる)
  - [WSL をインストールにして、Ubuntu 環境を構築する](#wsl-をインストールにしてubuntu-環境を構築する)
    - [WSL のインストール](#wsl-のインストール)
    - [（オプション）Windows Terminal をインストールする](#オプションwindows-terminal-をインストールする)
    - [（オプション）Windows Terminal でシェルの補完候補がない場合の警告音を消す](#オプションwindows-terminal-でシェルの補完候補がない場合の警告音を消す)
  - [WSL 上の Ubuntu に Git 環境を構築する](#wsl-上の-ubuntu-に-git-環境を構築する)
    - [参考：ターミナル（Bash）の基本操作](#参考ターミナルbashの基本操作)
    - [参考：基本的な Linux コマンド](#参考基本的な-linux-コマンド)
    - [gh のインストール](#gh-のインストール)
    - [gh で GitHub にログインして認証情報をセットアップする](#gh-で-github-にログインして認証情報をセットアップする)
    - [gitでのユーザ名と電子メールを設定する](#gitでのユーザ名と電子メールを設定する)
  - [Docker Desktop をインストールして WSL バックエンドを有効化する](#docker-desktop-をインストールして-wsl-バックエンドを有効化する)
  - [Visual Studio Code をインストールして、Dev Container 環境を構築する](#visual-studio-code-をインストールしてdev-container-環境を構築する)
    - [Visual Studio Code をインストールする](#visual-studio-code-をインストールする)
    - [Visual Studio Code の基本的なショートカット](#visual-studio-code-の基本的なショートカット)
    - [Visual Studio Code の基本的な設定を行う](#visual-studio-code-の基本的な設定を行う)
    - [必要な拡張機能をインストールする](#必要な拡張機能をインストールする)
      - [拡張機能「WSL」のインストール](#拡張機能wslのインストール)
      - [拡張機能「Dev Containers」のインストール](#拡張機能dev-containersのインストール)
  - [Visual Studio Code でリポジトリを開き、開発を行う](#visual-studio-code-でリポジトリを開き開発を行う)
    - [リポジトリを WSL ファイルシステム上に git clone する](#リポジトリを-wsl-ファイルシステム上に-git-clone-する)
    - [開発用のブランチを作成する](#開発用のブランチを作成する)
    - [Visual Studio Code を起動して、Dev Container 環境に接続する](#visual-studio-code-を起動してdev-container-環境に接続する)

## WSL をインストールにして、Ubuntu 環境を構築する

### WSL のインストール

公式ドキュメントを参照して WSL をインストールしてください。
<https://learn.microsoft.com/ja-jp/windows/wsl/install>

### （オプション）Windows Terminal をインストールする

WSL を起動するターミナルとして、Windows Terminal をセットアップしておくと便利です。  
まず、公式ドキュメントに従って Windwos Terminal をインストールします。  
<https://learn.microsoft.com/ja-jp/windows/terminal/install>

次に、Windows Terminal を起動したときに WSL が起動するようにプロファイルを設定します。  
Windows Terminal を起動し、タイトルバーを右クリック、「設定」を選択します。  
画面左下に「JSON ファイルを開く」というボタンがあるので、押下します。

JSON ファイルを見ると、`profiles` の `list` に各ターミナル向けのプロファイルが列挙されています。  
WSL2 のプロファイルは `source` が `Windows.Terminal.Wsl` になっています。  
この WSL2 のプロファイルの `guid` を、設定ファイルの `defaultProfile` にセットしてください。

```json
{
    "defaultProfile": "{07b52e3e-de2c-5db4-bd2d-ba144ed6c273}",
    "profiles":
    {
        "list":
        [
            {
                "guid": "{07b52e3e-de2c-5db4-bd2d-ba144ed6c273}", // これを defaultProfile にセットする
                "hidden": false,
                "name": "Ubuntu-20.04",
                "source": "Windows.Terminal.Wsl"
            }
        ]
    }
}
```

これで Windows Terminal を起動すると、自動的に WSL の Ubuntu に接続できます。

### （オプション）Windows Terminal でシェルの補完候補がない場合の警告音を消す

ターミナルで補完などしたときに候補が無い場合に Windows の警告音が出ます。  
耳障りなので、消してしまいましょう。

Windows Terminal の設定ファイルの WSL2 のプロファイルに `"bellStyle": "none"` を追加します。

## WSL 上の Ubuntu に Git 環境を構築する

ここから、Ubuntu でのコマンドライン上での作業となります。  
Windows Terminal などを起動し、WSL に接続してください。

### 参考：ターミナル（Bash）の基本操作

| キー   | 説明                                     |
| ------ | ---------------------------------------- |
| ↓      | ひとつ後に入力したコマンドを入力します。 |
| ↑      | ひとつ前に入力したコマンドを入力します。 |
| Tab    | コマンドやファイル名を補完できます。     |
| Enter  | 入力したコマンドを実行します。           |
| Ctrl+c | 実行中のプログラムを停止します。         |
| Ctrl+a | カーソルを先頭に移動します。             |
| Ctrl+e | カーソルを末尾に移動します。             |
| Ctrl+k | カーソルより右側を削除します。           |
| Ctrl+u | カーソルより左側を削除します。           |
| Ctrl+r | コマンド履歴を検索します。               |
| Ctrl+l | 画面をクリアします。                     |

### 参考：基本的な Linux コマンド

| コマンド                                                | 説明                                                                 |
| ------------------------------------------------------- | -------------------------------------------------------------------- |
| pwd                                                     | 現在のディレクトリを表示します。                                     |
| cd [ディレクトリ名]                                     | 指定したディレクトリへ移動します。                                   |
| ls -la                                                  | 現在のディレクトリ内のファイルやディレクトリを一覧します。           |
| cp [コピー元ファイル名] [コピー先ファイル名]            | ファイルをコピーします。                                             |
| cp -r [コピー元ディレクトリ名] [コピー先ディレクトリ名] | ディレクトリをコピーします。                                         |
| mv [ファイル名] [移動先のファイル名]                    | ファイルを移動します。名前を変えたい場合もこのコマンドを使用します。 |
| rm -f [ファイル名]                                      | ファイルを削除します。                                               |
| rm -rf [ディレクトリ名]                                 | ディレクトリを削除します。                                           |
| mkdir -p [ディレクトリ名]                               | ディレクトリを作成します。                                           |
| less [ファイル名]                                       | ファイルの内容を閲覧します。終了するには `q` キーを押下します。      |

### gh のインストール

GitHub の操作、とくにプライベートリポジトリを扱うときの認証情報の解決に使用します。

公式ドキュメントに従い、コマンドを入力してください。  
<https://github.com/cli/cli/blob/trunk/docs/install_linux.md>

### gh で GitHub にログインして認証情報をセットアップする

次のコマンドを実行します。

```bash
gh auth login
```

ターミナル上に認証コードが表示されるので、コピーします。

Web ブラウザが起動するので、先ほどコピーした認証コードを入力してください。  
※ Web ブラウザが起動しない場合は、ターミナル上に表示された URL にアクセスしてください

ターミナルに戻り、Enter キーを押下して、セットアップを完了します。

### gitでのユーザ名と電子メールを設定する

次のコマンドを実行します．

```bash
git config --global user.name "YOUR_NAME"
git config --global user.email "YOUR_EMAIL"
```

## Docker Desktop をインストールして WSL バックエンドを有効化する

公式ドキュメントに従いインストールしてください。  
<https://docs.docker.com/desktop/wsl/>

## Visual Studio Code をインストールして、Dev Container 環境を構築する

Visual Studio Code は vscode と表記されることが多いです。

### Visual Studio Code をインストールする

公式サイトからインストーラをダウンロードして、ウィザードに従ってインストールしてください。  
<https://code.visualstudio.com/download>

### Visual Studio Code の基本的なショートカット

| ショートカット | 効果               | 説明                                                                                                                                                                       |
| -------------- | ------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Ctrl+Shift+p   | コマンドパレット   | コマンドパレットを表示します。      Visual Studio Code やその拡張機能が提供する強力なコマンドをすばやく実行します。                                                        |
| F2             | シンボルの名前変更 | 変数名や関数名を一括で変更します。文字列置換と異なり、シンボル（変数や関数など）を認識して一括で変更できるので、誤爆がありません。ファイルを跨いで変更することも可能です。 |
| F12            | 定義へ移動         | 関数などの実装箇所にジャンプします。関数のシグネチャやコメント、実装内容を確認したくなったときに使用します。                                                               |

### Visual Studio Code の基本的な設定を行う

Visual Studio Code を起動し、`Ctrl+,` を入力します。

「設定」タブが開かれるので、画面右上の![設定（JSON）を開く](image.png)をクリックし、JSON 形式の設定ファイルを開きます。

次の内容を入力して保存してください。

```json
{
    "terminal.integrated.allowChords": false,
    "files.eol": "\n",
    "files.trimTrailingWhitespace": true,
    "[markdown]": {
        "files.trimTrailingWhitespace": false
    },
    "files.autoSave": "afterDelay",
    "files.autoSaveDelay": 1000,
    "editor.mouseWheelZoom": true
}
```

`files.autoSave` の値は `afterDelay` 以外でもよいですが、無効にはしないでください。
「ファイルの保存を忘れてしまって、意図しない動作をする」状況を予防するためです。
※ 無効にする場合は自己責任で「保存忘れ」が起こらないように徹底してください

### 必要な拡張機能をインストールする

`Ctrl+Shift+x` を入力して、拡張機能メニューを表示します。

#### 拡張機能「WSL」のインストール

Visual Studio Code を WSL 上の Ubuntu で使用するために、「WSL」拡張機能をインストールします。

検索ボックスに「ms-vscode-remote.remote-wsl」と入力します。  
検索結果の一覧から「WSL」を選択し「インストール」ボタンを押下します。

#### 拡張機能「Dev Containers」のインストール

Dev Container は Docker を利用した開発環境の共通化のしくみです。  
Dev Container を利用すると、開発時に利用するOS、OSにインストールされているツール、エディタの拡張機能、フォーマッタや静的テストなどのエディタの設定などをチームで統一できます。  
開発環境のセットアップ手順の簡略化や、個人ごとの環境の違いによるトラブルの防止に役立ちます。

検索ボックスに「ms-vscode-remote.remote-containers」と入力します。  
検索結果の一覧から「Dev Containers」を選択し「インストール」ボタンを押下します。

## Visual Studio Code でリポジトリを開き、開発を行う

### リポジトリを WSL ファイルシステム上に git clone する

WSL に接続して git clone を行います。

git clone するときのディレクトリについて気を付けるべきことがあります。  
WSL の Ubuntu からは、Ubuntu のファイルシステムと Windows のファイルシステムの両方が見えている状態です。  
`df` コマンドを実行してみてください。これはマウントされているファイルシステムを一覧するコマンドです。  
ここで Filesystem の欄が `C:\` や `drvfs` になっているファイルシステムが見つかるはずです。これが Windows のファイルシステムです。  
Windows のファイルシステムは Linux のファイルシステムと異なり、通常はファイルごとに書き込み権限や実行権限などを細かく設定できません。また、WSL からの Windows ファイルシステムの利用は非常に低速です。  
こういった理由から、通常の開発では Windows のファイルシステムを利用すべきではありません。  
git clone するときのディレクトリも Linux のファイルシステム上のディレクトリにしましょう。  
たとえば、「~/workspace/github.com/one-scope/リポジトリ名」などです。  

ディレクトリを作成するには次のコマンドを実行します。

```bash
mkdir -p ~/workspace/github.com/one-scope
```

次のコマンドで clone したいディレクトリへ移動して、リポジトリを clone します。

```bash
cd ~/workspace/github.com/one-scope
git clone https://github.com/one-scope/example-repository
```

clone できたら、リポジトリのディレクトリへ移動します。

```bash
cd ./example-repository
```

### 開発用のブランチを作成する

次のコマンドでブランチを作成し、作成したブランチに移動します。
ブランチはプルリクエストと対応付けて、ひとつのまとまった機能開発やバグフィックス単位で作成します。

```bash
git checkout -b my-branch
```

※ `git branch` コマンドを実行すると、現在のブランチを確認できます。

### Visual Studio Code を起動して、Dev Container 環境に接続する

clone したリポジトリのルートディレクトリで次のコマンドを実行して Visual Studio Code を起動します。

```bash
code .
```

Visual Studio Code が起動したら、`Ctrl+Shift+p` を入力し、コマンドパレットを表示します。

コマンドパレットから `Dev Containers: Rebuild and Reopen in Container` を実行します。  
コマンドパレットの入力は補完されるので、おそらく「rebuild」を入力すれば候補に出てきます。  
![コマンドパレット](image-1.png)

Docker イメージをビルドするため、しばらく時間がかかります。  
※ 画面右下の ![ログの表示](image-2.png) をクリックすると、ビルドログを確認できます。

画面左下の表示が ![開発コンテナー](image-3.png) となれば成功です。
