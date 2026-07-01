# 電力センサーモジュール基板設計

分電盤の各ブレーカーの電流をCTクランプで非接触測定し、ESPHome経由でHome Assistantに取り込むための基板を、EasyEDA ProとClaude Codeを連携させて設計するプロジェクト。

## ドキュメント

- [docs/spec.md](docs/spec.md) — 基板設計仕様書(ドラフト)。構成部品、回路構成の要点、確定仕様、保留事項をまとめたもの
- [docs/easyeda-connection.md](docs/easyeda-connection.md) — EasyEDA Proとの接続方式まとめ(AIエージェント連携用)
- [firmware/esphome/esphome-sample.yaml](firmware/esphome/esphome-sample.yaml) — ESPHomeサンプル設定(ADS1115 + CTクランプ3ch)
- [docs/calibration.md](docs/calibration.md) — 校正(キャリブレーション)の考え方と手順

## 構成概要

| 項目 | 内容 |
|---|---|
| マイコン | Seeed Studio XIAO ESP32C6 |
| ADコンバータ | ADS1115(16bit, 4ch) |
| センサー | CTクランプ SCT-013-000(YHDC)× 3 |
| ファームウェア | ESPHome |
| 基板 | 2層基板、JLCPCB発注想定 |

詳細は [docs/spec.md](docs/spec.md) を参照。

## 開発環境

VS Code Dev Containers(`.devcontainer/`)で構築。コンテナ起動時に以下が利用可能になる:

- Node.js(EasyEDA連携ブリッジサーバー用)
- Claude Code拡張機能
- ポート `49620` を自動フォワード(EasyEDA API Gateway用)

### 起動方法

1. VS Codeで本リポジトリを開き、"Reopen in Container" を選択
2. `.claude/skills/easyeda-api-skill` でブリッジサーバーを起動

   ```bash
   cd .claude/skills/easyeda-api-skill
   npm install
   npm run server
   ```

   もしくは VS Code の実行構成 `easyeda-api-skill: npm run server`([.vscode/launch.json](.vscode/launch.json))を使用

3. EasyEDA Pro デスクトップクライアント側に `run-api-gateway` 拡張機能を導入し、API Gatewayを接続
4. 接続確認

   ```bash
   curl http://localhost:49620/health
   curl http://localhost:49620/eda-windows
   ```

セットアップの詳細手順は [docs/easyeda-connection.md](docs/easyeda-connection.md) を参照。

## 現在のステータス

仕様(docs/spec.md)は確定済み。次のアクションはEasyEDA上での回路図作成(部品配置・結線)、続いてPCBレイアウト(2層、JLCPCB標準)。

## ライセンス

[MIT License](LICENSE)
