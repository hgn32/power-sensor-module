# EasyEDA Pro 接続方式まとめ(AIエージェント連携用)

## 公式Skill方式(easyeda-api-skill)

### 概要
- 提供元: GitHub org `easyeda`(著者表記 "JLCEDA" = EasyEDA/JLCPCB運営元)
- リポジトリ: https://github.com/easyeda/easyeda-api-skill
- 方式: Claude Codeの「Skills」機能(SKILL.md)+ Node.jsローカルブリッジ + WebSocket
- プロトコル: MCPではない。SKILL.mdの指示に従い、Claude CodeがbashからcurlでローカルAPIを叩く

### 必要環境
- Node.js 18+
- EasyEDA Pro デスクトップクライアント(拡張機能対応)

### セットアップ手順

#### 1. Skillを配置(グローバル or プロジェクト単位)
```bash
git clone https://github.com/easyeda/easyeda-api-skill.git .claude/skills/easyeda-api-skill
cd .claude/skills/easyeda-api-skill
npm install
```

#### 2. ブリッジサーバー起動(ポート49620-49629の空きを自動選択)
```bash
npm run server
```
VSCodeから起動する場合は `.vscode/launch.json` の "easyeda-api-skill: npm run server" 構成(実体は `node scripts/bridge-server.mjs`)を使ってもよい。

#### 3. EasyEDA Pro側に拡張機能を導入
- https://ext.lceda.cn/item/oshwhub/run-api-gateway から `run-api-gateway.eext` をダウンロード
- https://pro.easyeda.com/editor を開く
- `Advanced` -> `Extensions Manager` -> `Import`
- `Advanced` -> `API Gateway` -> `Reconnect`
- Chromeから「デバイス上のアプリへのアクセス許可」要求が出たら許可

#### 4. 接続確認
```bash
curl http://localhost:49620/health
curl http://localhost:49620/eda-windows
```

### 特徴・注意点
- Claude Codeが自動でSKILL.mdを読み込み、指示なしでEasyEDA操作を試みる
- 座標単位に注意: PCBは1mil、回路図は0.01inch(10mil)単位。AIが混同しやすい既知の落とし穴
- 関連拡張: `eext-run-api-gateway`(EasyEDA側の受信拡張)

### 関連ファイル(このリポジトリ内)
| 内容 | パス |
| --- | --- |
| Skill本体 | `.claude/skills/easyeda-api-skill/` |
| ブリッジサーバー実装 | `.claude/skills/easyeda-api-skill/scripts/bridge-server.mjs` |
| VSCode起動設定 | `.vscode/launch.json` |
