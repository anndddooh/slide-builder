# slide-builder

Markdownで書かれた**原案**を、内容を省略せずに**分かりやすいMarp形式のスライド**
（`.md`）へ変換するClaude Code用スキルです。

NotebookLMのスライド生成と違い、成果物は **編集可能な Marp の `.md`**。
あとから自由に手直しできます。

## できること

- 原案の**全文を保持**したまま、1トピック1スライドに再構成（章立ての再編はする）。
- 構造図（フロー・シーケンス・関係図など）は **Mermaid** で自動生成し、SVG化して埋め込み。
- 概念図・イラストは **画像生成AI（ChatGPT等）用のプロンプト（spec）** を用意。
  ユーザーが画像を生成して配置すると、スキルが自動で埋め込む。
- 出力は **テーマCSSをインラインした自己完結の `slides.md`**。
  VS Code の Marp 拡張で、設定ゼロでそのままプレビュー/PDF書き出しができる。

## 前提

- **Node.js**（Mermaidのレンダリングに `npx @mermaid-js/mermaid-cli` を使用。
  グローバルインストールは不要、初回のみ実行が遅い）。
- プレビュー/エクスポート用に **VS Code の Marp 拡張**（[Marp for VS Code]）を推奨。

[Marp for VS Code]: https://marketplace.visualstudio.com/items?itemName=marp-team.marp-vscode

## インストール

```
/plugin marketplace add anndddooh/slide-builder
/plugin install slide-builder@anndddooh-slide-builder-marketplace
```

> ローカルから試す場合は、リポジトリのパスを直接指定できます:
> `/plugin marketplace add /path/to/slide-builder`

## 使い方

スキルは2フェーズで動き、同じスキルが状態を見て自動でフェーズを判定します。

### 前半 — スライドと spec を生成

```
/slide path/to/原案.md
```

または「`path/to/原案.md` からスライドを作って」と依頼するだけでも起動します。

出力先 `原案-slides/` に `slides.md`・Mermaid図・画像生成AI用の `fig-*.spec.md` が
作られます。画像生成AI図が必要な箇所は、いったらプレースホルダ画像が入ります。

### 画像生成（手作業）

各 `fig-NN.spec.md` を ChatGPT にアップロードし、こう伝えます:

> 添付ファイルの「画像生成プロンプト」以降の指示に従って画像を生成してください。
> 冒頭の `---` で囲まれたメタ情報は無視してください。

生成された画像を `assets/fig-NN.png` という名前で保存します。

### 後半 — 画像を埋め込み

もう一度スキルを実行すると、画像がそろっていれば自動で `slides.md` に埋め込まれます。
一部しかそろっていなければ、未生成の図を一覧表示して待機します。

## 出力ディレクトリ

```
原案-slides/
  slides.md            自己完結のMarpスライド（テーマCSSインライン済み）
  assets/
    placeholder.svg    未生成figの仮表示
    fig-01.spec.md      画像生成AI用プロンプト
    fig-01.png          ← ユーザーが生成して配置
    dia-01.mmd          Mermaidソース（編集用に保存）
    dia-01.svg          レンダリング済みの図
```

## リポジトリ構成

```
slide-builder/                リポジトリルート（= クローン先フォルダ）
  .claude-plugin/
    marketplace.json          マーケットプレイス定義
  slide-builder/              プラグイン本体
    .claude-plugin/plugin.json
    commands/slide.md         /slide コマンド（薄いラッパー）
    skills/slide-create/
      SKILL.md                スキル本体（全ロジック）
      assets/                 テーマCSS・テンプレート・mmdc設定
```

## ライセンス

MIT
