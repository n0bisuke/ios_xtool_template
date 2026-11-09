# AGENTS.md

<!-- プロジェクトの概要に書き換える。アプリ本体のフォルダ名も書く。 -->
SwiftとxtoolでiOSアプリを開発するリポジトリです。環境の使い方は `README.md` を参照してください。

## 作業の記録

- **作業を始めるとき:** `logs/HANDOFF.md` を読み、現在の状況と次の作業を確認する。
- **作業を終えるとき:** `logs/HANDOFF.md` を最新の状態に書き換え、`logs/YYYY-MM-DD.md` に実行した内容と結果を追記する。同じ日のファイルがあれば追記する。
- **今後の対応候補:** `logs/BACKLOG.md` に書く。載っているだけでは着手せず、実装前に了承を得る。対応したら更新する。
- **長く残す知見:** 手順は `README.md`、作業のルールは `AGENTS.md`、その時点の状況と経緯は `logs/` に書く。
- **スクリーンショットなどの一時ファイル:** `logs/tmp/` に置く。Gitの対象外なので公開されない。
- **公開リポジトリの場合:** `logs/` も公開される。秘密鍵、Issuer ID、Team ID、UDID、Profile ID、個人名、他人の端末名、Macのユーザーパスは書かない。

## 制約

- 作成、編集、ビルドはコンテナ内で行う。Xcode GUIは使わない。
- Swift、xtool、Darwin SDKを再インストールしない。
- `*.p8` などの秘密鍵やApple Developerの認証情報を読まない、表示しない、送信しない。コミットもしない。
- Appleの公開APIだけを使う。未確認のAPIを推測で実装しない。
- 次の操作はユーザーに確認してから行う。
  - `xtool dev build --sign`、`xtool dev run`、`xtool ds`(Developerアカウントや実機に変更が及ぶ可能性がある)
  - コミットとプッシュ
  - **コードの実装や変更。** 不具合や要望の報告、感想、「〜したい」「〜できる?」は、実装の依頼とは限らない。原因の調査と対応案の提示までにとどめ、「実装してよいか」を確認し、了承を得てから着手する。

## コマンド

```sh
xtool dev build             # 署名なしビルド(アプリのフォルダで実行)
xtool devices --no-wait     # 端末一覧
xtool sdk status            # SDKの状態
xtool auth status           # 認証の状態
```

`xtool devices` は既定で端末が見つかるまで待ち続けるので、`--no-wait` を付けてください。端末の認識には、Macで `node scripts/usbmuxd-relay.mjs` を起動しておく必要があります。

`swift build` ではなく `xtool dev build` を使ってください。

## APIの調べ方

次の順で一次情報を確認します。

1. **SDKの `.swiftinterface`:** 実際に使えるシグネチャと `@available` を確認する。

   ```text
   /root/.swiftpm/swift-sdks/darwin.artifactbundle/Developer/Platforms/iPhoneOS.platform/Developer/SDKs/iPhoneOS.sdk/System/Library/Frameworks/<Framework>.framework/Modules/<Framework>.swiftmodule/arm64e-apple-ios.swiftinterface
   ```

2. **Apple公式ドキュメント:** developer.apple.comの通常のURLは本文を取得できないことがあります。末尾に `.md` を付けると取得できます。

## 既知の注意点

- **Capability:** App Store Connect APIでは有効にできないものがある。その場合はユーザーにApple DeveloperのWebサイトでの設定を依頼する。
- **Bundle ID:** アプリの `xtool.yml` の `bundleID` を変更する。生成物は変更しない。xtoolはApple側に `XTL-<ID>.` の接頭辞付きで登録する。
- **APIキー認証:** `xtool ds teams list` は使えない。Team IDは `xtool ds certificates list` の `team id` で確認できる。
- **並行処理:** Swift 6の厳格な並行性チェックで警告を出さない状態を保つ。

## 検証状況

ビルド成功は、実機で動くことを意味しません。報告では、確認済みの内容と未確認の内容を分けてください。
