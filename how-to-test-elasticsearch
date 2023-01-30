# How to try Elasticsearch with fuzzy search

1. Start Elasticsearch:

```sh
docker run -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" elasticsearch:7.10.1
```

```sh
sed -i -e 's/^mirrorlist/#mirrorlist/g' -e 's/^#baseurl=http:\/\/mirror/baseurl=http:\/\/vault/g' /etc/yum.repos.d/CentOS-*repo
```

2. Create Index:

```sh
curl -X PUT http://localhost:9200/my_index -H 'Content-Type: application/json' -d '
{
    "mappings": {
        "properties": {
            "text": {
                "type": "text"
            }
        }
    }
}'
```

3. Add documents

```sh
curl -XPOST http://localhost:9200/my_index/_doc/1 -H 'Content-Type: application/json' -d '
{
    "text": "This is a sample text document"
}'

curl -XPOST http://localhost:9200/my_index/_doc/2 -H 'Content-Type: application/json' -d '
{
    "text": "Another sample text document"
}'

```

4. Get document by fuzzy search

```sh
curl -XGET http://localhost:9200/my_index/_search -H 'Content-Type: application/json' -d '
{
    "query": {
        "fuzzy": {
            "text": {
                "value": "sanple",
                "fuzziness": "AUTO"
            }
        }
    }
}' | jq
```
```json
{
  "took": 6,
  "timed_out": false,
  "_shards": {
    "total": 1,
    "successful": 1,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": {
      "value": 2,
      "relation": "eq"
    },
    "max_score": 0.16547336,
    "hits": [
      {
        "_index": "my_index",
        "_type": "_doc",
        "_id": "2",
        "_score": 0.16547336,
        "_source": {
          "text": "Another sample text document"
        }
      },
      {
        "_index": "my_index",
        "_type": "_doc",
        "_id": "1",
        "_score": 0.14044379,
        "_source": {
          "text": "This is a sample text document"
        }
      }
    ]
  }
}
```

## Note

### Ref

- https://tech-blog.rakus.co.jp/entry/20191002/elasticsearch

### Commands

```sh
curl -XPOST http://localhost:9200/analyzer_handson/_doc -H 'Content-Type: application/json' -d '
{
  "text": "コウテイペンギンはペンギンであり鳥の一種である"
}'
curl -XPOST http://localhost:9200/analyzer_handson/_doc -H 'Content-Type: application/json' -d '
{
  "text": "ペンギンを見て和む"
}'
curl -XPOST http://localhost:9200/analyzer_handson/_doc -H 'Content-Type: application/json' -d '
{
  "text": "ペンギンとコウテイペンギンはどちらも鳥の一種だ"
}'
curl -XPOST http://localhost:9200/analyzer_handson/_doc -H 'Content-Type: application/json' -d '
{
  "text": "コウテイペンギンは鳥の一種だ"
}'
curl -XPOST http://localhost:9200/analyzer_handson/_doc -H 'Content-Type: application/json' -d '
{
  "text": "ペンギンは可愛い"
}'

curl -X POST "localhost:9200/analyzer_handson/_analyze?pretty" -H 'Content-Type: application/json' -d'
{
  "analyzer": "my_kuromoji_analyzer",
  "text": "コウテイペンギンは体格のいいものは１３０㌢あるという。僕は度々、コンピューターでそれを見て和んでいる"
}
' | jq

curl -X GET 'http://localhost:9200/analyzer_handson/?pretty'

curl -XGET http://localhost:9200/analyzer_handson/_search -H 'Content-Type: application/json' -d '
{
    "query": {
        "match": {
            "text": "ぺんぎん かわい"
        }
    }
}' | jq
```
