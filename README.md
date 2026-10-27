# Tauri + React + Typescript

This template should help get you started developing with Tauri, React and Typescript in Vite.

## Recommended IDE Setup

- [VS Code](https://code.visualstudio.com/) + [Tauri](https://marketplace.visualstudio.com/items?itemName=tauri-apps.tauri-vscode) + [rust-analyzer](https://marketplace.visualstudio.com/items?itemName=rust-lang.rust-analyzer)

## BlazeでRustの開発ビルドを実行する

[Blazeの利用ガイド](https://github.com/indexable-inc/blaze/blob/main/docs/usage.md)と
[Cargo連携の詳細](https://github.com/indexable-inc/blaze/blob/main/docs/cargo.md)を参照。
macOSではApple Siliconが必要です。未導入の場合は
[公式のインストール手順](https://github.com/indexable-inc/blaze/blob/main/docs/install.md)に従い、
`cargo-blaze`とそのエンジンを同じディレクトリに置いてPATHを通してください。

```sh
vp install
vp run blaze:status
vp run dev:blaze
```

`dev:blaze`はTauriのrunnerに`cargo-blaze`を指定します。フロントエンドの起動と
Rust変更時の再起動はTauriが担当します。通常の開発は`vp run tauri dev`で実行できます。
`cargo blaze enable`によるユーザー全体のCargo設定変更は不要です。

Rustだけを操作する場合は、リポジトリのルートから次を使います。

```sh
vp run blaze:build
vp run blaze:check
vp run blaze:test
vp run blaze:status
vp run blaze:stop
```

各Rustコマンドは`src-tauri/Cargo.toml`を指定します。初回ビルドは依存関係のコンパイルや
ツールチェーンの準備に時間がかかります。`blaze:status`でデーモンの状態と
Cargoへのフォールバック理由を確認してください。インストール済みのバージョンによって
動作が異なり、コマンドが成功してもBlazeによる高速化が有効とは限りません。

Blazeのチェックだけで完了とせず、変更の最終確認には通常のCargoも使ってください。

```sh
cargo check --manifest-path src-tauri/Cargo.toml
cargo test --manifest-path src-tauri/Cargo.toml
```

配布用ビルドは従来どおり`vp run tauri build`を使います。
