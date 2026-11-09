# private

リポジトリには含めず、**後から自分で用意するファイル**をまとめるフォルダです。各フォルダの `README.md` 以外はGitの対象外で、Dockerイメージにも入りません。

| フォルダ | 置くもの | 秘密か | AIエージェント |
|---|---|---|---|
| [`secrets/`](secrets/README.md) | App Store Connect APIの秘密鍵(`.p8`) | **秘密** | **読ませない** |
| [`downloads/`](downloads/README.md) | Xcodeの `.xip` など、大きな入手物 | 秘密ではない | 使ってよい |

```text
private/
├── README.md                 このファイル
├── secrets/
│   ├── README.md
│   └── apple-api-key.p8      自分で置く
└── downloads/
    ├── README.md
    └── Xcode_XX.X.xip        自分で置く
```

新しい種類のファイルを置くときは、秘密かどうかで `secrets/` か `downloads/` を選びます。迷ったら `secrets/` に置きます。
