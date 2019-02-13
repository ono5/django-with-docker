# Test stage

# AS キーワードで、multi-stage build 環境を構築する
# 同一 Dockerfile 内で複数の FROM を提議できるようになる
FROM alpine AS test
LABEL application=todobackend

# Install basic utilities
# apk -> Alpine Linux の package manager
# キャッシュさせないように --no-cache を指定
# Git は Docker にバージョンタグをつけるために付与
RUN apk add --no-cache bash git

# Install build dependencies
# Python C の拡張モジュールを追加
# mariadb-dev は、MYSQL client を構築する
RUN apk add --no-cache gcc python3-dev libffi-dev musl-dev linux-headers mariadb-dev

# 事前コンパイルおよび事前フォーマットを行うため wheel を導入
RUN pip3 install wheel

# Copy requirements
COPY /src/requirements* /build/
WORKDIR /build

# Build and install requirements
# --no-cahce-dir は、イメージの肥大化を防ぐ
# --no-input でユーザー確認を無効にする
RUN pip3 wheel -r requirements_test.txt --no-cache-dir --no-input

# コンテナに事前ビルドした wheel を導入する
# --no-index は、インターネットからパッケージをダウンロードしないようにする(wheelを使うため)
# -f は、インストール先を /build に作成した wheel に指定
RUN pip3 install -r requirements_test.txt -f /build --no-index --no-cache-dir
