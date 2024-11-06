Here is a comprehensive overview of endpoints available in ElasticSearch categorized by the main types of APIs: Cluster APIs, Index APIs, Document APIs, and Search APIs.

## Cluster APIs

These APIs are used to manage and monitor your Elasticsearch cluster.

### Cluster Health API
- **Endpoint**: `GET /_cluster/health`
- **Description**: Provides a quick overview of the cluster health, including basic index metrics and status information.
  ```bash
  curl -XGET "http://localhost:9200/_cluster/health"
  ```

### Cluster Stats API
- **Endpoint**: `GET /_cluster/stats`
- **Description**: Provides comprehensive statistics about the cluster, including nodes, indices, shards, and disk usage.
  ```bash
  curl -XGET "http://localhost:9200/_cluster/stats"
  ```

### Cluster Settings API
- **Endpoint**: `GET /_cluster/settings` and `PUT /_cluster/settings`
- **Description**: Used to view and change cluster-wide settings. Settings can be either transient (dynamic) or persistent (require a restart).
  ```bash
  curl -XGET "http://localhost:9200/_cluster/settings"
  curl -XPUT "http://localhost:9200/_cluster/settings" -d '{ "transient": { "index.number_of_replicas": 2 } }'
  ```

### Cluster Nodes Info API
- **Endpoint**: `GET /_nodes`
- **Description**: Provides information about the nodes in the cluster.
  ```bash
  curl -XGET "http://localhost:9200/_nodes"
  ```

### Cluster Node Stats API
- **Endpoint**: `GET /_nodes/stats`
- **Description**: Provides detailed statistics about the nodes in the cluster.
  ```bash
  curl -XGET "http://localhost:9200/_nodes/stats"
  ```

## Index APIs

These APIs are used to manage individual indices within the Elasticsearch cluster.

### Create Index API
- **Endpoint**: `PUT /<index_name>`
- **Description**: Creates a new index with specified settings and mappings.
  ```bash
  curl -XPUT "http://localhost:9200/my_index" -d '{ "settings": { "index.number_of_shards": 1, "index.number_of_replicas": 1 }, "mappings": { "properties": { "field1": { "type": "text" }, "field2": { "type": "keyword" } } } }'
  ```

### Delete Index API
- **Endpoint**: `DELETE /<index_name>`
- **Description**: Deletes an existing index.
  ```bash
  curl -XDELETE "http://localhost:9200/my_index"
  ```

### Index Settings API
- **Endpoint**: `GET /<index_name>/_settings` and `PUT /<index_name>/_settings`
- **Description**: Used to view and change settings of an index.
  ```bash
  curl -XGET "http://localhost:9200/my_index/_settings"
  curl -XPUT "http://localhost:9200/my_index/_settings" -d '{ "index.number_of_replicas": 2 }'
  ```

### Index Mappings API
- **Endpoint**: `GET /<index_name>/_mappings` and `PUT /<index_name>/_mappings`
- **Description**: Used to view and change the mappings of an index.
  ```bash
  curl -XGET "http://localhost:9200/my_index/_mappings"
  curl -XPUT "http://localhost:9200/my_index/_mappings" -d '{ "properties": { "field1": { "type": "text" }, "field2": { "type": "keyword" } } }'
  ```

### Index Stats API
- **Endpoint**: `GET /<index_name>/_stats`
- **Description**: Provides detailed statistics about an index, including search stats, indexing stats, and store size.
  ```bash
  curl -XGET "http://localhost:9200/my_index/_stats"
  ```

## Document APIs

These APIs are used to manage individual documents within an index.

### Index a Document API
- **Endpoint**: `POST /<index_name>/_doc` and `PUT /<index_name>/_doc/<id>`
  - **POST**: Used to create a new document without specifying an ID; Elasticsearch generates a unique ID.
    ```bash
    curl -XPOST "http://localhost:9200/my_index/_doc" -d '{ "field1": "text value", "field2": "keyword value" }'
    ```
  - **PUT**: Used to create a new document with a specific ID or to replace an existing document.
    ```bash
    curl -XPUT "http://localhost:9200/my_index/_doc/1" -d '{ "field1": "text value", "field2": "keyword value" }'
    ```

### Get a Document API
- **Endpoint**: `GET /<index_name>/_doc/<id>`
- **Description**: Retrieves a document by its ID.
  ```bash
  curl -XGET "http://localhost:9200/my_index/_doc/1"
  ```

### Update a Document API
- **Endpoint**: `POST /<index_name>/_update/<id>`
- **Description**: Updates a document partially using a script or by providing the updated fields.
  ```bash
  curl -XPOST "http://localhost:9200/my_index/_update/1" -d '{ "doc": { "field1": "new text value" } }'
  ```

### Delete a Document API
- **Endpoint**: `DELETE /<index_name>/_doc/<id>`
- **Description**: Deletes a document by its ID.
  ```bash
  curl -XDELETE "http://localhost:9200/my_index/_doc/1"
  ```

### Create Document with _create Endpoint API
- **Endpoint**: `PUT /<index_name>/_create/<id>`
- **Description**: Creates a new document with a specific ID, but fails if the document already exists.
  ```bash
  curl -XPUT "http://localhost:9200/my_index/_create/1" -d '{ "field1": "text value", "field2": "keyword value" }'
  ```

## Search APIs

These APIs are used to execute search queries on an Elasticsearch cluster.

### Search API
- **Endpoint**: `GET /<index_name>/_search` and `POST /<index_name>/_search`
- **Description**: Executes a search query and returns search hits.
  ```bash
  curl -XGET "http://localhost:9200/my_index/_search" -d '{ "query": { "match": { "field1": "some value" } } }'
  ```

### Multi-Search API
- **Endpoint**: `GET /_msearch` and `POST /_msearch`
- **Description**: Executes multiple search requests within a single API call.
  ```bash
  curl -XPOST "http://localhost:9200/_msearch" -d '{ "index": "my_index" }' -d '{ "query": { "match": { "field1": "some value" } } }'
  ```

### Count API
- **Endpoint**: `GET /<index_name>/_count` and `POST /<index_name>/_count`
- **Description**: Returns the number of documents matching a query.
  ```bash
  curl -XGET "http://localhost:9200/my_index/_count" -d '{ "query": { "match": { "field1": "some value" } } }'
  ```

### Update by Query API
- **Endpoint**: `POST /<index_name>/_update_by_query`
- **Description**: Updates all documents that match a query.
  ```bash
  curl -XPOST "http://localhost:9200/my_index/_update_by_query" -d '{ "query": { "match": { "field1": "some value" } }, "script": { "source": "ctx._source.field2 = \"new value\"" } }'
  ```

### Delete by Query API
- **Endpoint**: `POST /<index_name>/_delete_by_query`
- **Description**: Deletes all documents that match a query.
  ```bash
  curl -XPOST "http://localhost:9200/my_index/_delete_by_query" -d '{ "query": { "match": { "field1": "some value" } } }'
  ```

## Cat APIs

These APIs provide a more human-readable output for various cluster and index metrics.

### List Indices API
- **Endpoint**: `GET /_cat/indices`
- **Description**: Displays a list of all indices, their health, number of documents, size, etc.
  ```bash
  curl -XGET "http://localhost:9200/_cat/indices"
  ```

### Node Info API
- **Endpoint**: `GET /_cat/nodes`
- **Description**: Provides information about the nodes in the cluster.
  ```bash
  curl -XGET "http://localhost:9200/_cat/nodes"
  ```

### Shard Allocation API
- **Endpoint**: `GET /_cat/shards`
- **Description**: Displays information about shard allocation.
  ```bash
  curl -XGET "http://localhost:9200/_cat/shards"
  ```

## Additional Endpoints and Features

### Aggregations
- **Endpoint**: Various search endpoints support aggregations.
- **Description**: Used to perform aggregations on search results, such as terms, date histograms, etc.
  ```bash
  curl -XPOST "http://localhost:9200/my_index/_search" -d '{ "query": { "query_string": { "query": "gas*" } }, "aggs": { "tags": { "terms": { "field": "news.tags" } } } }'
  ```

### Suggestions
- **Endpoint**: Various search endpoints support suggestions.
- **Description**: Used to implement features like "Did you mean?" suggestions.
  ```bash
  curl -XPOST "http://localhost:9200/my_index/_search" -d '{ "suggest": { "title-suggestions": { "text": "gs pric rollrcoater", "term": { "size": 3, "field": "title" } } } }'
  ```

By understanding and utilizing these endpoints, you can effectively manage your Elasticsearch cluster, perform CRUD operations, and execute complex search queries.

Citations:
  1. https://opster.com/guides/elasticsearch/search-apis/elasticsearch-endpoints/
  2. https://www.dotcms.com/docs/latest/elasticsearch-rest-api
  3. https://dev.to/elastic/performing-crud-operations-with-elasticsearch-kibana-50ka
  4. https://python.plainenglish.io/beginners-guide-to-crud-with-elasticsearch-python-62ed07477a65?gi=97d8f25224d9
  5. https://logz.io/blog/elasticsearch-api/
  6. https://www.elastic.co/guide/en/elasticsearch/reference/current/rest-apis.html
   
- [Prev](https://github.com/BairiRohithReddy/EFK-Monitoring/blob/main/README.md)
- [Next](https://github.com/BairiRohithReddy/EFK-Monitoring/blob/main/put%20vs%20post.md)
  
  8. https://www.elastic.co/guide/en/cloud/current/ec-work-with-apis.html
  9. https://www.digitalocean.com/community/tutorials/how-to-interact-with-data-in-elasticsearch-using-crud-operations
