# Django With Django Cookbooks 

## todobackend-maseter

## Curl

```bash
curl localhost:8000
{"todos":"http://localhost:8000/todos"}


# Create New Item
curl -X POST -H "Content-Type: application/json" localhost:8000/todos -d '{"title": "Wash the car" , "order": 2}'
{"url":"http://localhost:8000/todos/4","title":"Wash the car","completed":false,"order":2}


curl -s localhost:8000/todos | jq
[
  {
    "url": "http://localhost:8000/todos/1",
    "title": "Walk the dog",
    "completed": false,
    "order": 1
  },
]

curl -X PATCH -H "Content-Type: appllication/json" localhost:8000/todos/2 -d '{"completed": true}'
{"detail":"Unsupported media type \"appllication/json\" in request."}
```

## Docker
Docker で環境構築する際に重要なことは、Docker image に開発環境用のモジュールやテストを含めないこと。

以下の環境に分ける。

### Test Stage
テスト環境。ソースをコンパイルし、アプリケーションのアーキテクチャの構築やテストを実行する

### Release stage
テスト環境からテスト済みのアプリケーションアーキテクチャをコピーする。  
最小構成になるようにする。　

## テストステージの構築

Dockerfile を todobackend-master 配下に作成する。

```bash
tree -L 2
.
├── Dockerfile
├── README.md
└── src
    ├── coverage.xml
    ├── db.sqlite3
    ├── manage.py
    ├── requirements.txt
    ├── requirements_test.txt
    ├── todo
    ├── todobackend
    └── unittests.xml
```

Dockerfile の FROM 要素に AS キーワードを付与するとマルチステージング環境にできる。

* [multi-stage build](https://docs.docker.com/develop/develop-images/multistage-build/)

## テスト環境の実行
```bash
# --target タグは、Dockerfile の As キーワードに指定した名前を紐付ける
docker build --target test -t todobackend-test .
```

# アプリケーションユーザーの作成
デフォルトでは、Docker コンテナは root 権限で実行される。

しかし、本番環境では、root ユーザーで実行するよりも独自にグループとユーザーを作成する方が望ましい、　

# 本番環境の実行

```bash
docker build -t todobackend-release .

# uWSGI をインストールしている
docker run -it --rm -p 8000:8000 todobackend-release uwsgi \
    --http=0.0.0.0:8000 --module=todobackend.wsgi --master
```

# 動作確認

```bash
 curl -s localhost:8000/todos | jq
[
  {
    "url": "http://localhost:8000/todos/1",
    "title": "Walk the dog",
    "completed": false,
    "order": 1
  },
  {
```

# Web Server Gateway Interface (WSGI)
WSGI is a standard interface used by Python applications to interact with web server.

Every Django application includes a WSGI module for communicating with a web server, which can be accessed via <application-name>.wsgi

# Static ファイルの配信
Django の開発サーバーでは、static コンテンツを自動生成するが、本番環境で WSGI のような拡張 Web Server を用いた実行では、
自分で作成する必要がある。

また、collectstatic コマンドを実行する必要もある。

collectstatic は、STATIC_ROOT 設定に定義されたロケーションに静的なファイルを集めるためのコマンド。

```bash
# app user で、public フォルダを作成してから実行すること
docker-compose down -v
docker-compose build
docker-compose migrate
docker-compose run app python3 manage.py collectstatic --no-input
docker-compose up app adm
```

# マイグレーション

```bash
docker-compose run release python3 manage.py migrate
```

