# 環境構築について

今回は[Docker](https://www.docker.com/)を使って環境を構築します。  
サーバサイドのプログラミングの環境をPC上に1から構築しようとすると、かなり複雑になってしまいます。

最近のサーバサイドプログラミングではDockerという仮想化技術を使って簡単に開発環境を構築することが出来ます。  
今回もDockerを使って開発環境を構築します。


# Windowsでの開発環境構築

以下の手順で準備します。

事前にWindowsターミナルをインストールしてください。  
https://docs.microsoft.com/ja-jp/windows/terminal/install


VScodeのインストールは[こちら](https://www.javadrive.jp/vscode/install/index1.html) の記事を参考にインストールしてください。



1. WSLのバージョンを確認
1. Linux（Ubuntu）環境を用意する
1. Docker Desktop for Windowsのインストール
1. プロジェクトのソースコードを取得する
1. dockerを起動する
1. ブラウザでアクセスして動作確認する


## WSLのバージョン確認

```
PS C:\Users\polidog> wsl --list --verbose
```

WSL2が有効になっていない方はこちらの記事を参考に設定してください。
https://algorithm.joho.info/unix/windows-subsystem-for-linux-install/


## Linux（Ubuntu）環境を用意する

Microsoft Storeより `Ubuntu 20.04 LTS` を選択してインストールしてください。


## Docker Desktop for Windowsのインストール

https://docs.docker.com/desktop/windows/install/
上記のサイトよりDocker Desktop for Windowsを落としてきてください。


インストールが終わったらDocker Desktopを立ち上げて、設定ボタンをクリックします。  
Resourcesをクリックして `Enables integration with additional distros` にチェックを入れてください。  
またここではUbuntu-20.04を有効にしてください。  

下記のURLに詳しい説明が乗っています。
https://codeaid.jp/blog/docker-windowswsl2/

## プロジェクトのソースコードを取得する

WindowsターミナルよりUbuntu-20.04を選択し、以下のGitコマンドを使ってソースコードを取得してください。

```
$ cd
$ git clone https://github.com/polidog/procan-2023-code
```

## dockerを起動する

先程取得したソースコードのディレクトリに移動します。

```
$ cd procan-2023-code
```

その後dockerコマンドで立ち上げます。


```
$ docker compose up -d
```


## ブラウザでアクセスして動作確認する

起動が終わったらブラウザで以下のURLにアクセスしてください。

[http://localhost/](http://localhost/)


```
Hello, world.
```

と表示されていたらセットアップは完了です。


## その他参考資料

[MS公式サイト](https://docs.microsoft.com/ja-jp/windows/wsl/tutorials/wsl-containers)

# Macでの環境構築

VSCodeのインストールは[こちら](https://www602.math.ryukoku.ac.jp/Prog1/vscode-mac.html)の記事を参考にしてください。


1. Docker Desktop for Macのインストール
1. プロジェクトのソースコードを取得する
1. dockerを起動する
1. ブラウザでアクセスして動作確認する

## Docker Desktop for Macのインストール

 下記サイトからDocker Desktop for Macをインストールしてください。
 https://hub.docker.com/editions/community/docker-ce-desktop-mac
 
 CPUがIntelの方は「Mac with Intel chip」を選択してください。
 M1の方は「Mac with Apple chip」を選択してください。
 
## プロジェクトのソースコードを取得する
 
ターミナルアプリを起動し以下のコマンドを実行しソースコードを取得してください。
 
 ```
$ cd
$ git clone https://github.com/polidog/procan-2023-code
```

## dockerを起動する

先程取得したソースコードのディレクトリに移動します。

```
$ cd procan-2023-code
```
 
その後dockerコマンドで立ち上げます。


```
$ docker compose up -d
```


## ブラウザでアクセスして動作確認する

起動が終わったらブラウザで以下のURLにアクセスしてください。

[http://localhost/](http://localhost/)
 
 ```
Hello, world.
```

と表示されていたらセットアップは完了です。
