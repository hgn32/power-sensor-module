# CLAUDE.md

このファイルはClaude Codeがこのリポジトリで作業する際のガイドとなる。

## プロジェクト概要

分電盤の各ブレーカーの電流をCTクランプで非接触測定し、ESPHome経由でHome Assistantに取り込むための電力センサーモジュール基板を、EasyEDA ProとClaude Codeを連携させて設計するプロジェクト。詳細は [docs/spec.md](docs/spec.md) を参照。

## EasyEDA Pro接続方法

EasyEDA Pro上での回路図・PCB操作は、`easyeda-api-skill`(公式Skill)経由で行う。詳細は [docs/easyeda-connection.md](docs/easyeda-connection.md) にまとめてあるが、要点は以下の通り。

- 方式: Claude Codeの「Skills」機能(SKILL.md)+ Node.jsローカルブリッジ + WebSocket。MCPではなく、SKILL.mdの指示に従いbashからcurlでローカルAPIを叩く
- Skill本体: `.claude/skills/easyeda-api-skill`(GitHub `easyeda/easyeda-api-skill` からclone済み)
- 関連拡張機能: `eext-run-api-gateway`(EasyEDA Pro側の受信拡張)

### ブリッジサーバーの起動

```bash
cd .claude/skills/easyeda-api-skill
npm install
npm run server
```

ポート49620〜49629の空きを自動選択する。VS Codeの実行構成 `easyeda-api-skill: npm run server`([.vscode/launch.json](.vscode/launch.json))からも起動可能。

### EasyEDA Pro側の準備

1. https://ext.lceda.cn/item/oshwhub/run-api-gateway から `run-api-gateway.eext` をダウンロード
2. https://pro.easyeda.com/editor を開く
3. Advanced -> Extensions Manager -> Import
4. Advanced -> API Gateway -> Reconnect
5. Chromeから「デバイス上のアプリへのアクセス許可要求」-> 許可

### 接続確認

```bash
curl http://localhost:49620/health
curl http://localhost:49620/eda-windows
```

### 注意点(座標単位)

- PCB: 1mil単位
- 回路図: 0.01inch(10mil)単位

AIが混同しやすい既知の落とし穴なので、座標を扱う際は単位を必ず確認すること。

## ESPHome / 校正

- [firmware/esphome/esphome-sample.yaml](firmware/esphome/esphome-sample.yaml) — ADS1115 + CTクランプ3chのESPHomeサンプル設定(ドラフト)
- [docs/calibration.md](docs/calibration.md) — `calibrate_linear` による校正の考え方と、クランプメーターを使った実測手順

個体ごとに校正が必要な運用のため(docs/spec.md 4.8参照)、ESPHome設定を触る際は校正値がハードコードされている前提を忘れないこと。

## 図の作成方法

説明用に図(ブロック図、フローチャートなど)を作成する場合は、Draw.io(diagrams.net)で開いて編集できるSVG形式で書くこと。

- SVGファイルとして保存し、Markdownから `![説明](図.svg)` の形で埋め込む
- Draw.ioで開くには、SVG内にDraw.io編集用のデータ(`content` 属性内のmxfile/mxGraphModel XML、または `<mxfile>` をコメントとして埋め込む形式)が必要。単なる見た目だけのSVGではなくDraw.ioが再編集できる形式にする
- 拡張機能(VS Code用Draw.io拡張など)からもそのまま開いて修正できるようにする

## 開発環境

VS Code Dev Containers(`.devcontainer/`)を使用。コンテナはポート`49620`(EasyEDA API Gateway用)を自動フォワードする。
