## Elasticsearch CRUD Operations and Cluster Management

### Introduction

Elasticsearch is a powerful search and analytics engine designed to store, search, and analyze large volumes of data quickly and in near real-time. This document outlines the CRUD (Create, Read, Update, Delete) operations in Elasticsearch, the differences between `POST` and `PUT` methods, and how to manage cluster and index settings.

## CRUD Operations

Elasticsearch uses standard HTTP methods to perform CRUD operations on data.

### Create

- **HTTP PUT**: Used to create a new document with a specific ID. If a document with that ID already exists, it will be overwritten.
  ```bash
  curl -XPUT "http://localhost:9200/index/type/id" -d '{ "field": "value" }'
  ```
  Example:
  ```bash
  curl -XPUT "http://localhost:9200/playground/equipment/1" -d '{ "type": "slide", "quantity": 2 }'
  ```

- **HTTP POST**: Used to create a new document without specifying an ID. Elasticsearch will generate a unique ID for the document.
  ```bash
  curl -XPOST "http://localhost:9200/index/type" -d '{ "field": "value" }'
  ```
  Example:
  ```bash
  curl -XPOST "http://localhost:9200/playground/equipment" -d '{ "type": "swing", "quantity": 3 }'
  ```

### Read

- **HTTP GET**: Used to retrieve documents.
  ```bash
  curl -XGET "http://localhost:9200/index/type/id"
  ```
  Example:
  ```bash
  curl -XGET "http://localhost:9200/playground/equipment/1"
  ```

### Update

- **HTTP PUT**: Used to update an existing document by providing the full document with changes.
  ```bash
  curl -XPUT "http://localhost:9200/index/type/id" -d '{ "field": "new_value" }'
  ```
  Example:
  ```bash
  curl -XPUT "http://localhost:9200/playground/equipment/1" -d '{ "type": "slide", "quantity": 4 }'
  ```

- **HTTP POST (Update)**: Used with the `_update` endpoint to update a document partially, similar to a PATCH operation.
  ```bash
  curl -XPOST "http://localhost:9200/index/type/id/_update" -d '{ "doc": { "field": "new_value" } }'
  ```
  Example:
  ```bash
  curl -XPOST "http://localhost:9200/playground/equipment/1/_update" -d '{ "doc": { "quantity": 5 } }'
  ```

### Delete

- **HTTP DELETE**: Used to delete a document by its ID.
  ```bash
  curl -XDELETE "http://localhost:9200/index/type/id"
  ```
  Example:
  ```bash
  curl -XDELETE "http://localhost:9200/playground/equipment/1"
  ```

## Difference between POST and PUT Operations

### PUT

- **Purpose**: Primarily used for creating new documents or replacing existing documents.
- **ID Specification**: You must specify the document ID.
- **Behavior**: If a document with the specified ID already exists, it will be overwritten.
- **Example**:
  ```bash
  curl -XPUT "http://localhost:9200/playground/equipment/1" -d '{ "type": "slide", "quantity": 2 }'
  ```

### POST

- **Purpose**: Used to create new documents without specifying an ID, or to update parts of a document.
- **ID Generation**: If no ID is specified, Elasticsearch generates a unique ID for the document.
- **Behavior**: Can be used to update parts of a document using the `_update` endpoint.
- **Examples**:
  - Create without ID:
    ```bash
    curl -XPOST "http://localhost:9200/playground/equipment" -d '{ "type": "swing", "quantity": 3 }'
    ```
  - Update partially:
    ```bash
    curl -XPOST "http://localhost:9200/playground/equipment/1/_update" -d '{ "doc": { "quantity": 5 } }'
    ```

## Cluster Information

### Cluster Health

- **Endpoint**: `GET /_cluster/health`
- **Description**: Provides an overview of the cluster status (green, yellow, red) and information on nodes, shards, relocation, etc.
  ```bash
  curl -XGET "http://localhost:9200/_cluster/health"
  ```

### Cluster Stats

- **Endpoint**: `GET /_cluster/stats`
- **Description**: Provides detailed metrics on nodes, indices, shards, disk usage, etc.
  ```bash
  curl -XGET "http://localhost:9200/_cluster/stats"
  ```

### Cluster Settings

- **Endpoint**: `GET /_cluster/settings`
- **Description**: Helps view current cluster-wide configuration settings.
  ```bash
  curl -XGET "http://localhost:9200/_cluster/settings"
  ```

## Index Information

### List Indices

- **Endpoint**: `GET /_cat/indices`
- **Description**: Displays a list of all indices, their health, number of documents, size, etc.
  ```bash
  curl -XGET "http://localhost:9200/_cat/indices"
  ```

### Index Settings

- **Endpoint**: `GET /<index_name>/_settings`
- **Description**: Used for viewing mappings, analysis settings, and other configurations for a specific index.
  ```bash
  curl -XGET "http://localhost:9200/playground/_settings"
  ```

### Index Stats

- **Endpoint**: `GET /<index_name>/_stats`
- **Description**: Provides detailed stats about an index (search stats, indexing stats, store size, etc.).
  ```bash
  curl -XGET "http://localhost:9200/playground/_stats"
  ```

## Cluster Settings Management

### Types of Cluster Settings

- **Transient Settings**: These settings can be changed dynamically using the Cluster Update Settings API. Examples include the number of replicas for an index or routing allocation rules.
  ```bash
  curl -XPUT "http://localhost:9200/_cluster/settings" -d '{ "transient": { "index.number_of_replicas": 2 } }'
  ```

- **Persistent Settings**: These settings require a cluster restart to take effect. Examples include cluster name, data path location, and certain network settings.
  ```bash
  # Note: Persistent settings are typically set in the elasticsearch.yml file and require a restart.
  ```

### Updating Cluster Settings

- **Considerations**:
  - Not all settings are dynamic. Many cluster-wide settings are persistent and cannot be updated using typical REST verbs.
  - Use `GET /_cluster/settings` to see current settings.
  - Be aware that changing persistent settings requires a full cluster restart for the change to take effect.
  - Cluster settings update resembles the Update concept of CRUD, but you don't directly create or delete cluster-wide settings using typical REST verbs. Instead, you modify existing settings or provide cluster-wide configurations at the time of cluster creation.

## Additional Considerations

### Dynamic vs Persistent Settings

- **Dynamic Settings**: Can be updated without restarting the cluster.
- **Persistent Settings**: Require a cluster restart to take effect.

### Best Practices

- **Backup Data**: Regularly back up your data to ensure it is safe in case of failures or data loss.
- **Monitor Cluster Health**: Regularly check the cluster health and stats to ensure optimal performance.
- **Optimize Index Settings**: Optimize index settings such as shard count, replica count, and mapping to improve search and indexing performance.

### Common Errors and Troubleshooting

- **Document Not Found**: Ensure the document ID is correct and the document exists in the index.
- **Index Not Found**: Ensure the index name is correct and the index exists in the cluster.
- **Cluster Not Available**: Check the cluster health and ensure all nodes are available.

By following these guidelines and understanding the differences between `POST` and `PUT` operations, you can effectively manage your Elasticsearch cluster and perform CRUD operations efficiently.
