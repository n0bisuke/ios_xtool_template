# iOSアプリ開発環境テンプレート

XcodeのGUIを使わず、MacのOrbStack上のLinuxコンテナで、SwiftとxtoolによるiOSアプリの作成、ビルド、署名、実機インストールを行うためのテンプレートです。`wifi-aware-playground`(AwareChat)で動作を確認した仕組みを流用しています。

## 構成

```text
Mac
├── OrbStack: Dockerコンテナ(Linux ARM64)
│     Swift + Darwin SDK + xtool + Claude Code
│     └─ USBMUXD_SOCKET_ADDRESS=host.docker.internal:27015
├── scripts/usbmuxd-relay.mjs(Node.js、127.0.0.1:27015で待ち受け)
│     └─ /var/run/usbmuxd(macOS)
└── iPhone(USB)
```

コンテナはMacの `usbmuxd` を中継経由で使うので、iPhoneをコンテナに直接つなぐ必要はありません。中継は `127.0.0.1` だけで待ち受けるので、LANには公開されません。

## 新しいプロジェクトの作り方

**1. このフォルダを新しい場所にコピーします(Mac)。**

```sh
cp -R template ~/path/to/my-app
cd ~/path/to/my-app
git init
```

**2. `compose.yml` の `name:` を変えます。** 他のプロジェクトと同じ名前にしないでください。

**3. APIキーを置きます。** `.p8` を `secrets/apple-api-key.p8` という名前で置きます。ファイル名にKey IDを含めないので、`compose.yml` をそのまま公開できます。`secrets/` と `*.p8` はGitの対象外です。

**4. コンテナを起動します。**

```sh
docker compose run --rm swift
```

**5. 初回だけ、コンテナ内でSDKとログインを用意します。** SDKを他のプロジェクトと共有する場合は不要です(`compose.yml` のコメントを参照)。

```sh
xtool sdk install /workspace/<Xcodeの.xip>   # .xipはApple Developerのサイトから取得
xtool auth login --mode key                  # Key pathは /run/secrets/apple-api-key.p8
xtool sdk status
xtool auth status
```

**6. アプリを作ってビルドします。**

```sh
xtool new <AppName> --skip-setup
cd <AppName>
xtool dev build
```

`xtool.yml` の `bundleID` を自分のものに変えてください。

**7. `AGENTS.md` と `logs/` をプロジェクトに合わせて書き換えます。**

## 実機で試す

1. Macで中継を起動し、そのままにします(Node.jsが必要)。

   ```sh
   node scripts/usbmuxd-relay.mjs
   ```

2. iPhoneをUSBでつなぎ、ロックを解除します。
3. コンテナ内で確認し、インストールします。

   ```sh
   xtool devices --no-wait
   xtool dev run --usb -u <UDID>
   ```

`xtool dev run` は、Apple Developer側にApp ID、証明書、Profileを作成または更新します。

## 分かっている注意点

- **Capability:** App Store Connect APIでは有効にできないものがある(Wi-Fi Awareは409になった)。その場合はApple DeveloperのWebサイトで、App IDに有効化する。xtoolはApp IDを `XTL-<ID>.<Bundle ID>` の形で登録するので、そのIDを探す。
- **`zip`:** `xtool dev run` に必要。`Dockerfile` に入っている。
- **端末が見えない:** 接続直後は数秒かかることがある。中継の起動、ロック解除、「このコンピュータを信頼」を確認する。
- **2台同時接続:** `xtool dev run` が `Error: unknown` になったことがある。`--usb` を付けるか、1台ずつつなぐ。
- **Wi-Fi経由:** Finderで「Wi-Fi接続時にこのiPhoneを表示」を有効にしても、`usbmuxd` の一覧には出なかった。USBを使う。
- **シミュレータ:** コンテナでは起動できない。
- **バージョン:** `Dockerfile` はxtoolの `latest` を取得する。動作確認はSwift 6.4.1、xtool 1.24.0。

## 終了と再開

- `exit` でコンテナは消えますが、ソースコードとNamed Volume(SDK、認証、Claude Codeの設定)は残ります。
- `docker compose down -v` はNamed Volumeまで消すので、実行しないでください。
- 使わないときは、Macの中継を `Ctrl+C` で止めます。
