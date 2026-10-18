# suiren

Rust を学びながら作る、自分用の macOS ブラウザ。
ページ表示には Chromium Embedded Framework（CEF）を使い、ブラウザ UI にネイティブ Liquid Glass を取り入れる。

## 開発

Rust / Cargo、Xcode の開発ツール、CMake、Ninja が必要。
Rust / Cargo は 1.99.1 を使用する。

Homebrew でビルドツールを揃える場合：

```sh
brew install cmake ninja
```

初回ビルド時には、`cef-rs` が対応するビルド済み CEF SDK を自動で取得する。
ビルド時には、選択中の Xcode の SDK を明示してコンパイラと揃える。

```sh
SDKROOT="$(xcrun --sdk macosx --show-sdk-path)" cargo run
```

現時点では `cef-rs`（クレート名 `cef`）154.5.0 を追加した段階。`sandbox` feature のみを有効にしている。
実行すると `Hello, world!` を表示して終了する。CEF の初期化とウィンドウの起動は次の段階で実装する。

## MVP の範囲

- 1 つのウィンドウで外部 Web ページを表示する。
- URL 入力、戻る・進む・再読み込みを使える。
- タブを作成・切り替え・閉じることができる。
- URL・タイトル・読み込み中・読み込み失敗を UI に反映する。
- タブごとのページとナビゲーション履歴を独立させる。
- Liquid Glass はブラウザ UI に使い、Web ページ自体は透明化しない。

拡張機能、ブックマーク、同期、パスワード管理、履歴の永続化、自作エンジン、他 OS への対応は MVP に含めない。

## 最低限の振る舞い

- 通常の閲覧対象は HTTP / HTTPS。URL 入力、リンク、リダイレクト、新規ウィンドウ要求に同じナビゲーション方針を適用する。
- 外部ページとブラウザ UI を分離し、外部ページから特権 Rust IPC や OS 操作 API を呼べないようにする。
- 不正な入力、通信失敗、読み込み中のタブ終了でも操作を続けられるようにする。

## 最初の検証

1. 手元の macOS・SDK・Rust の環境を確認する。
2. Rust + CEF で最小ウィンドウを起動し、HTTPS ページを 1 枚表示する。
3. ネイティブ Liquid Glass の UI と組み合わせ、重なり・リサイズ・入力フォーカスを確認する。
4. 使用 API、起動手順、制約と結果を記録し、構成を決める。

概要設計は [docs/architecture.md](docs/architecture.md) にまとめる。
Rust + AppKit（`objc2`）+ [cef-rs](https://github.com/tauri-apps/cef-rs) を初期構成とし、macOS 26 以降を設計対象にする。
SDK・依存バージョンと実機での動作は最初の検証で確認し、細かな操作仕様は実装する段階で決めて記録する。

## 進め方

必要な操作の仕様を小さく決め、実装・動作確認を行ってから次に進む。
状態の所有者、リソースの寿命、イベントの流れ、エラー処理を自分で説明できることも目標にする。

進捗と完了条件は [Issue #1](https://github.com/expsh13/suiren/issues/1) で管理する。
