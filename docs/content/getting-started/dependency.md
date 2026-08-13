# 依存関係

他の Mod やライブラリをプロジェクトに読み込むための設定は `build.gradle` の `dependencies` ブロックで行います。

## 依存関係の構成

Gradle では依存関係を `dependencies` ブロックで指定します。

指定の方法はいくつかあります。

| 構成 | 説明 | 用途の例 |
| :--- | :--- | :--- |
| `implementation` | コンパイル時と実行時の両方で依存関係を必要 | 必須の前提 Mod、常時使用するライブラリ |
| `compileOnly` | コンパイル時にのみ必要 | コンパイルにだけ必要な API、注釈プロセッサ |
| `runtimeOnly` | コンパイル時には不要、実行時にのみ必要 | 連携確認用の Mod（JEI など）、コンパイルコードには触れないが動作確認に必要な Mod |
| `annotationProcessor` | コンパイル時の注釈処理に使用される | Mixin などのプロセッサ |

## マッピングについて

Minecraft の Mod は通常、難読化されています。

開発環境でこれらを扱うために、ForgeGradle は `fg.deobf` という特別なメソッドを提供しています。

これを依存関係の宣言時に適用することで、指定した Jar ファイルを開発環境のマッピングに合わせて再マッピングして読み込んでくれます。

```gradle title="build.gradle"
dependencies {
    // JEI
    implementation fg.deobf("curse.maven:jei-238222:4712866")
}
```

## Mod の依存関係を追加する

### CurseMaven を使用する

[CurseMaven](https://www.cursemaven.com/) は、CurseForge 上のファイルを Maven 依存関係として簡単に扱えるようにする非公式サービスです。

1.  **リポジトリの追加**

    `repositories` ブロックに以下を追加します。
    ```gradle
    repositories {
        maven {
            url "https://cursemaven.com"
            content {
                includeGroup "curse.maven"
            }
        }
    }
    ```
    !!! info

        content の指定は必須ではありませんが、CurseMaven への無駄なリクエストを減らせます。

2.  **依存関係の記述**
    フォーマット: `curse.maven:<description>-<projectID>:<fileID>`
    *   **description**: 任意の識別用文字列（実際の解決には使われません）
    *   **projectID**: CurseForge プロジェクト ID (About Project 欄などに記載)
    *   **fileID**: `Files` タブで特定のファイルを開いた際の URL 末尾の数字

    ```gradle
    dependencies {
        // JEI の例 (Project ID: 238222, File ID: 4712866)
        compileOnly fg.deobf("curse.maven:jei-238222:4712866")
        runtimeOnly fg.deobf("curse.maven:jei-238222:4712866")
    }
    ```

    以上のように記述できますが、手間がかかります。

    CurseForgeでファイルを開くと、`Curse Maven Snippet` という欄に依存関係として使える記載があります。(例: [JEI](https://www.curseforge.com/minecraft/mc-mods/jei/files/7391695))

    これをそのままコピーすると楽です。

### Modrinth Maven を使用する

Modrinth も公式で Maven リポジトリを提供しています。([Modrinth Maven](https://support.modrinth.com/en/articles/8801191-modrinth-maven))

1.  **リポジトリの追加**
    
    ```gradle
    repositories {
        exclusiveContent {
            forRepository {
                maven {
                    name = "Modrinth"
                    url = "https://api.modrinth.com/maven"
                }
            }
            forRepositories(fg.repository) // Only add this if you're using ForgeGradle, otherwise remove this line
            filter {
                includeGroup "maven.modrinth"
            }
        }
    }
    ```

2.  **依存関係の記述**
    
    フォーマット: `maven.modrinth:<projectID>:<version>`

    * ProjectID: プロジェクトのID。URLにある。
    * Version: バージョン、ファイルを開いて`Version number`の欄にある

    例: [JEI](https://modrinth.com/mod/jei/version/p7yZWpEg)

    ```gradle
    dependencies {
        implementation fg.deobf("maven.modrinth:jei:15.20.0.129")
    }
    ```

### ローカルの Jar ファイルを使用する

Maven リポジトリに公開されていない Mod を開発環境に入れたい場合などは、プロジェクト内のフォルダから読み込むこともできます。

1.  プロジェクトルートに `libs` フォルダを作成し、そこに `.jar` ファイルを入れます。

2.  リポジトリの設定

    ```gradle
    repositories {
        flatDir {
            dirs "libs"
        }
    }
    ```
3.  `dependencies` に以下のように記述します。

    `group:name:version`の形式である必要があります。
    `group`は何でも良いので、ここでは`blank`としています。
    
    ファイル名は `name-version.jar` の形式になっている必要があります。

    ```gradle
    dependencies {
        // libs/jei-1.20.1-forge-15.20.0.129.jar
        implementation fg.deobf("blank:jei-1.20.1-forge:15.20.0.129")
    }
    ```
