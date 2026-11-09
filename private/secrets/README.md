# private/secrets

秘密情報を置くフォルダです。**このREADME以外はGitの対象外**で、コミットされません。AIエージェントには、このフォルダのファイルを読ませないでください。Dockerイメージにも入りません(`.dockerignore` で除外)。

## 置くファイル

| ファイル名 | 中身 | 入手先 |
|---|---|---|
| `apple-api-key.p8` | App Store Connect APIの秘密鍵 | App Store Connectの「ユーザとアクセス > 統合 > App Store Connect API」 |

```text
private/secrets/
├── README.md            このファイル(Gitに含まれる)
└── apple-api-key.p8     自分で置く(Gitの対象外)
```

ダウンロードした `AuthKey_XXXXXXXXXX.p8` を、名前を変えて置きます。ファイル名にKey IDを残さないので、`compose.yml` をそのまま公開できます。

```sh
mv ~/Downloads/AuthKey_XXXXXXXXXX.p8 private/secrets/apple-api-key.p8
```

コンテナ内では `/run/secrets/apple-api-key.p8` として読み取り専用で見えます。`xtool auth login --mode key` の「Key path」には、このパスを入力します。

## 注意

- **`.p8` は一度しかダウンロードできません。** なくしたらAPIキーを再発行します。
- **置く前にコンテナを起動しないでください。** Dockerが `apple-api-key.p8` という名前の空のフォルダを作ります。そうなったら、コンテナを終了してからそのフォルダを削除し、`.p8` を置き直します。
- **漏れたら:** App Store ConnectでAPIキーを失効させ、新しいキーで `xtool auth login --mode key` をやり直します。
- Key ID、Issuer IDは秘密鍵ではありませんが、ログやコミットに書かないでください。
