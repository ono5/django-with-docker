# Django With Django Cookbooks 

#todobackend-maseter

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
Docker で環境構築する際に重要なことは、Docker image に開発環境ようのモジュールやテストを含めないこと。

以下の環境に分ける。

### Test Stage
テスト環境。ソースをコンパイルし、アプリケーションのアーキテクチャの構築やテストを実行する

### Release stage
テスト環境からテスト済みのアプリケーションアーキテクチャをコピーする　　
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

* [multi-stage build](https://docs.docker.com/develop/develop-images/multistage-build/)



