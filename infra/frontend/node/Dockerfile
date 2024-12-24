# Node.js 23 (Alpineベース) イメージを使用
FROM node:23-alpine

# コンテナ内部の作業ディレクトリ
WORKDIR /usr/src/frontend

# 依存関係のインストール
# パッケージをコピーしてインストールする場合:
COPY frontend/package*.json ./

RUN npm ci

# ソースコードをコピー
# (コンテナ内でもビルドしたい場合はこちら)
COPY frontend/ .

# 3000番ポートをNext.js開発サーバーで利用
EXPOSE 3000

# Docker Compose で `command: npm run dev` を指定する場合はCMDは省略可
# 本番ビルドなどを行う場合は別途調整してください
CMD ["npm", "run", "dev"]
