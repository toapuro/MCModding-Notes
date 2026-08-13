# 環境構築

Mod開発を始めるには、まず開発環境の構築が必要です。

プラグインや開発サポートが豊富なため、ここでは開発ツール（IDE）として IntelliJ を使用します。

## IntelliJ のセットアップ

1. **IntelliJ IDEAのインストール**
    
    [公式ダウンロードページ](https://www.jetbrains.com/ja-jp/idea/download/?section=windows)

2. **日本語化 (任意)**
    
    IntelliJを起動してランチャーメニューが表示されたら、サイドバーのPluginsタブから {==Japanese Language Pack==} と検索してインストールします。
    インストール後に IntelliJ を再起動すれば日本語化が反映されます。
    ![](../../assets/intellij/intellij-japanese-ext.png)

3. **Minecraft Development プラグインをインストール**

    同じくPluginsタブから {==Minecraft Development==} と検索してインストールし、完了したら一度 IntelliJ を再起動してください。


## 前提知識

スキップしても問題ありませんが、迷ったときに見返すと理解しやすくなります。

| 用語 | 備考 |
| --- | --- |
| Mixin | これをオンにしない場合、依存関係を設定する時に面倒なので、オンにしておくことをおすすめします |
| License | その Mod の著作権の扱い方を宣言するもので、依存関係を設定する時、確認する必要があります |
| Group ID | パッケージの指定。ドメインを逆に書く。ドメインを持っていなければ `io.github.作者名` のような形式が良い |
| Package | パッケージ。クラスをグループで管理できる仕組みで、実態としてはフォルダとほぼ同じ |
| Artifact ID | 基本的にModのIDを書いておく |
| Class | Java でコードを書く基本単位。1クラス1ファイルで機能ごとに分割できる |

**Modローダーの選択**

| プラットフォーム | 備考 |
| --- | --- |
| NeoForge | 1.20.4 以降はこちら。1.20.4 以降のバージョンを開発するほとんどの開発者が移行済み。 |
| Forge | 1.20.4 未満ならこちら |

1.20.1は特別にNeoForgeとForgeのどちらにも対応しています。

## Mod開発環境のセットアップ

方法はいくつかあります。

NeoForgeであれば [1, (NeoForge) Mod ジェネレータの利用](#1-neoforge-mod-ジェネレータの利用) を推奨します。

Forgeであれば [4, (NeoForge/Forge) IntelliJプラグイン経由で生成](#4-neoforgeforgefabric-intellijプラグイン経由で生成) を推奨します。

### 1, (NeoForge) Mod ジェネレータの利用

NeoForge (1.20.4 以降)  
[NeoForge Mod Generator](https://neoforged.net/mod-generator/)

### 2, (NeoForge) MDKをダウンロード

[NeoForge MDK一覧](https://github.com/orgs/NeoForgeMDKs/repositories)

ここにあるリポジトリの中から対象のバージョンを探して、
右上の Code->Download ZIP からダウンロード・解凍し、build.gradle を開けば IDE が立ち上がるはずです。

!!! tip
    `git clone --depth 1 https://github.com/~~` でクローンしても構いません(depth=1 は余分なコミット履歴を省くため)。

### 3, (Forge) Forge MDK[^1]の利用

[Forge MDK](https://files.minecraftforge.net/net/minecraftforge/forge/)

こちらからバージョンを選択して MDK をダウンロードし解凍、build.gradle を開きます。

!!! tip
    Latest と Recommended の違いは基本的にありませんが、後者をお勧めします。

[^1]: MDKはMod Developer Kitの略で、いわゆるテンプレートです。

### 4, (NeoForge/Forge) IntelliJプラグイン経由で生成

プロジェクトを新規作成するとき左下にあるジェネレータから Minecraft を選択し、各項目を入力して作成を押してください。

!!! note
    JDKの指定が必要な場合は [Java JDK(IntelliJ IDE)](#java-jdkintellij-ide) を参考にしてみてください。

## Java JDK(IntelliJ IDE)

JDK は、Java を実行するための基盤一式だと捉えると分かりやすいです。

以下のテーブルのように、マイクラバージョンごとに使用するJDKバージョンが異なり、基本的に開発環境も対応するJDKに合わせます。

| MC バージョン | JDK バージョン |
| ------------- | -------------- |
| 1.16.x        | JDK 8          |
| 1.17.x        | JDK 16         |
| 1.18.x        | JDK 17         |
| 1.19.x        | JDK 17         |
| 1.20.x        | JDK 17         |
| 1.21.x        | JDK 21         |

### ダウンロード

IntelliJ であれば、プロジェクトを開いた状態で左上の ≡ メニュー->ファイル->プロジェクト構成->プロジェクトと進み、SDK[^2]の指定を開き、{==JDKのダウンロード...==}を選択することでダウンロードできます。

![JDKのダウンロード](../../assets/intellij/jdk-download.png)

JDKバージョンは先程のテーブルを参考に設定してください。

ベンダー[^3]の選択ができると思いますが、特にこだわりがなければ {==JetBrains Runtime==} がおすすめです。

!!! info
    Jetbrains Runtime ではホットスワップという、実行中にコードを変更して反映させることができる機能が使えます。

IDE外にも導入しておきたいのであれば以下のベンダーからダウンロードすることをお勧めします。

- [Adoptium](https://adoptium.net/temurin/releases)

- [OpenJDK](https://jdk.java.net/25/)

[^2]: JavaではJDKのことを指す
[^3]: Java関連のベースキット(JDK等)を提供する企業やサービス

## テンプレートの編集

MDKから生成した場合は適切な値へ変更する箇所がいくつかあります。

- `gradle.properties` の `mod_id` や `mod_name` 等のプロパティ
- `src/main/java` 以下のパッケージ
- `src/main/java` 以下の `*Mod.java` の `MODID` やクラス名
- `src/main/resources/*.mixins.json` のファイル名や `"package"`, `"refmap"`

以下例
```gradle title="gradle.properties"
minecraft_version_range=[1.20.1] // 単一バージョンのみ対応の場合[バージョン]のように記述
...
mod_id=example_mod // Mod ID
mod_name=ExampleMod // Mod名
mod_license=MIT // 好きなライセンスを指定
mod_group_id=dev.toapuro // グループID。#前提知識を参照
mod_authors=toapuro, another_author // 作者一覧
mod_description=An example mod // Modの説明
```

分からない用語は [#前提知識](#前提知識) を参照してください。

## ビルド・実行の方法

右側にある以下のような gradle アイコンを押すと、ビルドに関連する操作ができます。

![](../../assets/intellij/gradle.png)

jar にビルドする場合は `Tasks->build->build`、

開発環境で実際に動作を確認したいのであれば `Tasks->forgegradle runs->runClient` を実行。

!!! tips

    `build.gradle` を変更した場合には、サイドバーの左上にループのようなアイコンをクリックすることで、変更を適用することができます。

初心者向けの解説は一旦ここまでです。