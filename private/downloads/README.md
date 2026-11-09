# private/downloads

Appleなどから自分でダウンロードする、**秘密ではない大きなファイル**を置くフォルダです。このREADME以外はGitの対象外で、Dockerイメージにも入りません。再配布できないものもあるので、Gitには含めません。

## 置くファイル

| ファイル | 用途 | 入手先 |
|---|---|---|
| `Xcode_XX.X.xip` | Darwin SDKの生成(約2GB) | Apple Developerの「More Downloads」 |

## 使い方

コンテナ内から `/workspace/private/downloads/` で見えます。

```sh
xtool sdk install /workspace/private/downloads/Xcode_XX.X.xip
xtool sdk status
```

SDKのインストール後は、`.xip` を削除しても動作に影響しません。ただし、SDKを入れ直すときに再ダウンロードが必要になります。
