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

## GitHub Actions

プルリクエストと `main` / `master` への push で CI を実行します。フロントエンドと
Rust の変更を判定し、該当する検証とビルドを実行します。共通設定の変更や比較元を
取得できない場合は両方を検証します。フロントエンドのソースだけを変更した場合は
関連テストを実行し、ファイルの追加・削除や設定変更時は全テストを実行します。
依存関係と Rust のビルド結果はキャッシュされます。
既存ファイルにフォーマット違反があるため、CI では lint と型チェックに加えて
ワークフローのフォーマットを確認します。全体の `vp check` は違反解消後に有効化できます。

Actions の「Package」を手動実行すると、Linux、macOS、Windows の配布物を
成果物として7日間保存します。署名、公開、配布先へのデプロイは設定していません。
GitHub Actions の参照はコミット SHA に固定し、Dependabot で更新を確認します。

### コード品質

`vp run lint:biome` は TypeScript の静的検査を実行します。フォーマットは Vite+ に
任せ、Biome のフォーマッターは無効にしています。`vp run lint:fallow` は未使用コードや
依存関係を検査します。導入前からの検出結果は `.fallow-baseline.json` に記録しており、
解消した項目は確認のうえ基準から削除してください。新しい検出は CI を失敗させます。
`vp run lint:duplicates` はコードの重複も検査します。

Rust は `cargo fmt --manifest-path src-tauri/Cargo.toml --check` と
`cargo clippy --locked --manifest-path src-tauri/Cargo.toml --all-targets -- -D warnings`
で確認します。CI の `cargo-audit` はロックファイルの脆弱性を毎回および毎週確認し、
保守終了などの警告も表示します。`cargo-machete` は未使用の Rust 依存関係を検出します。
既存の未使用依存 `serde` と `serde_json` は一時的に対象外にしています。
JavaScript の本番依存関係も `vp pm audit -- --prod` で監査します。
