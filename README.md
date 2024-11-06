# EFK-Monitoring

CRUD (Create, Read, Update, Delete) operations for Elasticsearch:

## Monitoring in Kubernetes with the EFK Stack

The EFK stack, comprising Elasticsearch, Fluent Bit, and Kibana, is a popular logging and monitoring solution for efficiently collecting, analyzing, and visualizing logs from applications and infrastructure.

### Components of the EFK Stack

- **Elasticsearch**: Store and search logs efficiently.
- **Fluent Bit**: Collect and forward logs from various sources.
- **Kibana**: Visualize and explore logs to gain valuable insights.

## Elasticsearch CRUD Operations

### Create Operation

#### Using PUT to Create an Index

To create an index in Elasticsearch, use the `PUT` method with the appropriate JSON data.

```bash
CLUSTER_IP=$(kubectl get svc elasticsearch -n efk -o jsonpath='{.spec.clusterIP}')
curl -X PUT "http://$CLUSTER_IP:9200/products" -H 'Content-Type: application/json' -d '{
  "settings": {
    "number_of_shards": 1,
    "number_of_replicas": 1
  },
  "mappings": {
    "properties": {
      "product_id": { "type": "integer" },
      "name": { "type": "text" },
      "description": { "type": "text" },
      "price": { "type": "float" },
      "category": { "type": "text" },
      "brand": { "type": "text" }
    }
  }
}'
```

- **URL**: `http://$CLUSTER_IP:9200/products` specifies the endpoint for creating the `products` index.
- **HTTP Method**: `PUT` is used to create or update an index.
- **HTTP Header**: `Content-Type: application/json` indicates that the request body contains JSON data.
- **JSON Data**: The settings and mappings for the index are provided in the request body.

#### Using PUT or POST to Create a Document

You can create a document using either the `PUT` or `POST` method.

##### Using PUT with a Specified ID

```bash
curl -X PUT "http://$CLUSTER_IP:9200/products/_doc/1" -H 'Content-Type: application/json' -d '{
  "product_id": 67890,
  "name": "Cozy Winter Sweater",
  "description": "Soft and stylish sweater for cold days",
  "price": 59.99,
  "category": "Apparel",
  "brand": "Trendy Threads"
}'
```

- **URL**: `http://$CLUSTER_IP:9200/products/_doc/1` specifies the index, type, and ID of the document.
- **HTTP Method**: `PUT` is used to create or update a document with a specified ID.

##### Using POST with Auto-Generated ID

```bash
curl -X POST "http://$CLUSTER_IP:9200/products/_doc" -H 'Content-Type: application/json' -d '{
  "product_id": 67891,
  "name": "Cozy Winter Sweater 2",
  "description": "Soft and stylish sweater for cold days",
  "price": 59.99,
  "category": "Apparel",
  "brand": "Trendy Threads"
}'
```

- **URL**: `http://$CLUSTER_IP:9200/products/_doc` specifies the index and type, and Elasticsearch will generate an ID.
- **HTTP Method**: `POST` is used to create a document with an auto-generated ID.

### Read Operation

#### Retrieve a Document by ID

To retrieve a document by its ID, use the `GET` method.

```bash
kubectl get svc elasticsearch -n efk -o jsonpath='{.spec.clusterIP}' | xargs -I {} curl -X GET "http://{}:9200/products/_doc/1?pretty"
```

- **URL**: `http://$CLUSTER_IP:9200/products/_doc/1` specifies the index, type, and ID of the document.
- **HTTP Method**: `GET` is used to retrieve the document.
- **Parameter**: The `?pretty` parameter formats the JSON response for better readability.

#### Retrieve Only the Source Fields

If you only need the source fields of the document, use the `_source` endpoint:

```bash
kubectl get svc elasticsearch -n efk -o jsonpath='{.spec.clusterIP}' | xargs -I {} curl -X GET "http://{}:9200/products/_source/1"
```

- **URL**: `http://$CLUSTER_IP:9200/products/_source/1` specifies the index, type, and ID of the document and retrieves only the source fields.

#### Verify Document Existence

To verify if a document exists, use the `HEAD` method:

```bash
kubectl get svc elasticsearch -n efk -o jsonpath='{.spec.clusterIP}' | xargs -I {} curl -X HEAD "http://{}:9200/products/_doc/1"
```

- **URL**: `http://$CLUSTER_IP:9200/products/_doc/1` specifies the index, type, and ID of the document.
- **HTTP Method**: `HEAD` returns a status code indicating whether the document exists.

### Search Operation

#### Search for Documents by Name

To search for documents with a specific term in the name field, use the `_search` endpoint with a query.

```bash
kubectl get svc elasticsearch -n efk -o jsonpath='{.spec.clusterIP}' | xargs -I {} curl -X GET "http://{}:9200/products/_search" -H 'Content-Type: application/json' -d '{
  "query": {
    "match": {
      "name": "sweater"
    }
  }
}'
```

- **URL**: `http://$CLUSTER_IP:9200/products/_search` specifies the index and the search endpoint.
- **HTTP Method**: `GET` is used to perform the search.
- **HTTP Header**: `Content-Type: application/json` indicates that the request body contains JSON data.
- **JSON Data**: The query specifies a match query on the `name` field with the term `sweater`.

### Update Operation

#### Update the Price of a Product

To update an existing document, you can use the `PUT` method or the `_update` endpoint.

##### Using PUT to Update a Document

```bash
kubectl get svc elasticsearch -n efk -o jsonpath='{.spec.clusterIP}' | xargs -I {} curl -X PUT "http://{}:9200/products/_doc/1" -H 'Content-Type: application/json' -d '{
  "price": 129.99
}'
```

- **URL**: `http://$CLUSTER_IP:9200/products/_doc/1` specifies the index, type, and ID of the document.
- **HTTP Method**: `PUT` is used to update the document.
- **JSON Data**: The updated field (`price`) is provided in the request body.

##### Using the _update Endpoint

```bash
kubectl get svc elasticsearch -n efk -o jsonpath='{.spec.clusterIP}' | xargs -I {} curl -X POST "http://{}:9200/products/_update/1" -H 'Content-Type: application/json' -d '{
  "doc": {
    "price": 129.99
  }
}'
```

- **URL**: `http://$CLUSTER_IP:9200/products/_update/1` specifies the index, type, and ID of the document to update.
- **HTTP Method**: `POST` is used to update the document.
- **JSON Data**: The update script or document changes are provided in the request body.

### Delete Operation

#### Delete a Document

To delete a document, use the `DELETE` method.

```bash
kubectl get svc elasticsearch -n efk -o jsonpath='{.spec.clusterIP}' | xargs -I {} curl -X DELETE "http://{}:9200/products/_doc/1"
```

- **URL**: `http://$CLUSTER_IP:9200/products/_doc/1` specifies the index, type, and ID of the document to delete.
- **HTTP Method**: `DELETE` is used to remove the document.

The operation of deleting documents and indexes is indeed included in the documentation, but here is a more detailed and explicit section to ensure clarity:

### Delete Operation

#### Delete a Document by ID
To delete a document by its ID, use the `DELETE` method.

```bash
kubectl get svc elasticsearch -n efk -o jsonpath='{.spec.clusterIP}' | xargs -I {} curl -X DELETE "http://{}:9200/products/_doc/1"
```

- **URL**: `http://$CLUSTER_IP:9200/products/_doc/1` specifies the index, type, and ID of the document to delete.
- **HTTP Method**: `DELETE` is used to remove the document.

#### Delete the Index
To delete an entire index, use the `DELETE` method on the index endpoint.

```bash
kubectl get svc elasticsearch -n efk -o jsonpath='{.spec.clusterIP}' | xargs -I {} curl -X DELETE "http://{}:9200/products"
```

- **URL**: `http://$CLUSTER_IP:9200/products` specifies the index to delete.
- **HTTP Method**: `DELETE` is used to remove the index.

### Example Workflow for Deleting Documents and Indexes

Here is how we can include these operations in our workflow:

```bash
CLUSTER_IP=$(kubectl get svc elasticsearch -n efk -o jsonpath='{.spec.clusterIP}')

# Create the index
curl -X PUT "http://$CLUSTER_IP:9200/products" -H 'Content-Type: application/json' -d '{
    "settings": {
        "number_of_shards": 1,
        "number_of_replicas": 1
    },
    "mappings": {
        "properties": {
            "product_id": {
                "type": "integer"
            },
            "name": {
                "type": "text"
            },
            "description": {
                "type": "text"
            },
            "price": {
                "type": "float"
            },
            "category": {
                "type": "text"
            },
            "brand": {
                "type": "text"
            }
        }
    }
}'

# Add a document
curl -X PUT "http://$CLUSTER_IP:9200/products/_doc/1" -H 'Content-Type: application/json' -d '{
    "product_id": 67890,
    "name": "Cozy Winter Sweater",
    "description": "Soft and stylish sweater for cold days",
    "price": 59.99,
    "category": "Apparel",
    "brand": "Trendy Threads"
}'

# Retrieve a document by ID
curl -X GET "http://$CLUSTER_IP:9200/products/_doc/1?pretty"

# Search for documents by name
curl -X GET "http://$CLUSTER_IP:9200/products/_search" -H 'Content-Type: application/json' -d '{
    "query": {
        "match": {
            "name": "sweater"
        }
    }
}'

# Update the price of a product
curl -X PUT "http://$CLUSTER_IP:9200/products/_doc/1" -H 'Content-Type: application/json' -d '{
    "price": 129.99
}'

# Verify the update
curl -X GET "http://$CLUSTER_IP:9200/products/_doc/1"

# Delete a document by ID
kubectl get svc elasticsearch -n efk -o jsonpath='{.spec.clusterIP}' | xargs -I {} curl -X DELETE "http://{}:9200/products/_doc/1"

# Delete the index
kubectl get svc elasticsearch -n efk -o jsonpath='{.spec.clusterIP}' | xargs -I {} curl -X DELETE "http://{}:9200/products"
```

### Additional Information

- **Deleting Documents**: When deleting a document, ensure that the document exists before attempting to delete it to avoid errors.
- **Deleting Indexes**: Deleting an index will remove all documents and settings associated with that index. Use this operation with caution.

#### Retrieving All Documents

If we need to retrieve all documents from an index, we can use the Scroll API or the Search API with pagination.

##### Using the Scroll API

The Scroll API allows us to retrieve large numbers of documents efficiently by creating a snapshot of the index.

```bash
kubectl get svc elasticsearch -n efk -o jsonpath='{.spec.clusterIP}' | xargs -I {} curl -X GET "http://{}:9200/products/_search?scroll=1m&size=1000" -H 'Content-Type: application/json' -d '{
  "query": {
    "match_all": {}
  }
}'
```

Then, use the scroll ID returned in the response to fetch the next batch of documents:

```bash
kubectl get svc elasticsearch -n efk -o jsonpath='{.spec.clusterIP}' | xargs -I {} curl -X GET "http://{}:9200/_search/scroll" -H 'Content-Type: application/json' -d '{
  "scroll": "1m",
  "scroll_id": "<scroll_id_from_previous_response>"
}'
```

Repeat this process until no more documents are returned.

##### Using Search After

Another approach is to use the `search_after` parameter with a sort clause.

```bash
kubectl get svc elasticsearch -n efk -o jsonpath='{.spec.clusterIP}' | xargs -I {} curl -X GET "http://{}:9200/products/_search" -H 'Content-Type: application/json' -d '{
  "size": 1000,
  "sort": { "_id": "asc" },
  "query": { "match_all": {} }
}'
```

Then, use the sort value of the last hit as the `search_after` parameter for the next request:

```bash
kubectl get svc elasticsearch -n efk -o jsonpath='{.spec.clusterIP}' | xargs -I {} curl -X GET "http://{}:9200/products/_search" -H 'Content-Type: application/json' -d '{
  "size": 1000,
  "sort": { "_id": "asc" },
  "search_after": [536738],
  "query": { "match_all": {} }
}'
```

Repeat this process until all documents are retrieved.

### Example Workflow

Here is a complete example workflow that includes creating an index, adding documents, retrieving documents, searching documents, updating documents, and deleting documents:

```bash
CLUSTER_IP=$(kubectl get svc elasticsearch -n efk -o jsonpath='{.spec.clusterIP}')

# Create the index
curl -X PUT "http://$CLUSTER_IP:9200/products" -H 'Content-Type: application/json' -d '{
  "settings": {
    "number_of_shards": 1,
    "number_of_replicas": 1
  },
  "mappings": {
    "properties": {
      "product_id": { "type": "integer" },
      "name": { "type": "text" },
      "description": { "type": "text" },
      "price": { "type": "float" },
      "category": { "type": "text" },
      "brand": { "type": "text" }
    }
  }
}'

# Add a document
curl -X PUT "http://$CLUSTER_IP:9200/products/_doc/1" -H 'Content-Type: application/json' -d '{
  "product_id": 67890,
  "name": "Cozy Winter Sweater",
  "description": "Soft and stylish sweater for cold days",
  "price": 59.99,
  "category": "Apparel",
  "brand": "Trendy Threads"
}'

# Add another document
curl -X PUT "http://$CLUSTER_IP:9200/products/_doc/2" -H 'Content-Type: application/json' -d '{
  "product_id": 67891,
  "name": "Cozy Winter Sweater 2",
  "description": "Soft and stylish sweater for cold days",
  "price": 69.99,
  "category": "Apparel",
  "brand": "Trendy Threads"
}'

# Retrieve a document by ID
curl -X GET "http://$CLUSTER_IP:9200/products/_doc/1?pretty"

# Search for documents by name
curl -X GET "http://$CLUSTER_IP:9200/products/_search" -H 'Content-Type: application/json' -d '{
  "query": {
    "match": {
      "name": "sweater"
    }
  }
}'

# Update the price of a product
curl -X PUT "http://$CLUSTER_IP:9200/products/_doc/1" -H 'Content-Type: application/json' -d '{
  "price": 129.99
}'

# Verify the update
curl -X GET "http://$CLUSTER_IP:9200/products/_doc/1"

# Delete a document
curl -X DELETE "http://$CLUSTER_IP:9200/products/_doc/1"

# Verify the deletion
curl -X HEAD "http://$CLUSTER_IP:9200/products/_doc/1"
```

### Additional Information

#### Retrieving All Documents

If you need to retrieve all documents from an index, you can use the Scroll API or the Search API with pagination.

##### Using the Scroll API

The Scroll API allows you to retrieve large numbers of documents efficiently by creating a snapshot of the index.

```bash
kubectl get svc elasticsearch -n efk -o jsonpath='{.spec.clusterIP}' | xargs -I {} curl -X GET "http://{}:9200/products/_search?scroll=1m&size=1000" -H 'Content-Type: application/json' -d '{
  "query": {
    "match_all": {}
  }
}'
```

Then, use the scroll ID returned in the response to fetch the next batch of documents:

```bash
kubectl get svc elasticsearch -n efk -o jsonpath='{.spec.clusterIP}' | xargs -I {} curl -X GET "http://{}:9200/_search/scroll" -H 'Content-Type: application/json' -d '{
  "scroll": "1m",
  "scroll_id": "<scroll_id_from_previous_response>"
}'
```

Repeat this process until no more documents are returned.

##### Using Search After

Another approach is to use the `search_after` parameter with a sort clause.

```bash
kubectl get svc elasticsearch -n efk -o jsonpath='{.spec.clusterIP}' | xargs -I {} curl -X GET "http://{}:9200/products/_search" -H 'Content-Type: application/json' -d '{
  "size": 1000,
  "sort": { "_id": "asc" },
  "query": { "match_all": {} }
}'
```

Then, use the sort value of the last hit as the `search_after` parameter for the next request:

```bash
kubectl get svc elasticsearch -n efk -o jsonpath='{.spec.clusterIP}' | xargs -I {} curl -X GET "http://{}:9200/products/_search" -H 'Content-Type: application/json' -d '{
  "size": 1000,
  "sort": { "_id": "asc" },
  "search_after": [536738],
  "query": { "match_all": {} }
}'
```

Repeat this process until all documents are retrieved.

### Troubleshooting Common Issues

Here are some common issues you might encounter and how to troubleshoot them:

#### Index Not Found

If you receive an error indicating that the index does not exist, ensure that the index has been created successfully.

```bash
curl -X GET "http://$CLUSTER_IP:9200/_cat/indices"
```

This command lists all existing indices. If your index is not listed, you need to create it.

#### Document Not Found

If a document is not found, verify that the document ID is correct and that the document exists in the index.

```bash
curl -X GET "http://$CLUSTER_IP:9200/products/_doc/1"
```

If the document does not exist, we may need to add it or check the ID.

#### Search Query Issues

If our search queries are not returning the expected results, check the query syntax and ensure that the fields and terms are correct.

```bash
curl -X GET "http://$CLUSTER_IP:9200/products/_search" -H 'Content-Type: application/json' -d '{
  "query": {
    "match": {
      "name": "sweater"
    }
  }
}'
```

Review the Elasticsearch documentation for more details on query syntax and troubleshooting.

### Conclusion

By following this guide, you have learned how to perform CRUD operations in Elasticsearch within a Kubernetes environment using the EFK stack. This includes creating indices, adding and retrieving documents, searching for documents, updating documents, and deleting documents. These skills are essential for efficiently managing and analyzing logs in your applications and infrastructure.

### Further Reading

For more advanced topics and detailed documentation, refer to the official Elasticsearch documentation:

- [Elasticsearch Documentation]
- [Elasticsearch API Reference]

These resources provide comprehensive information on Elasticsearch features, configuration, and troubleshooting.

: https://www.elastic.co/guide/en/elasticsearch/reference/current/index.html
: https://www.elastic.co/guide/en/elasticsearch/reference/current/rest-apis.html
: https://www.elastic.co/guide/en/elasticsearch/reference/current/search-request-body.html



To understand the differences between `PUT` and `POST` methods in the context of Elasticsearch, and how the update operation varies when using these methods, here is a detailed explanation:

### Create Operation: PUT vs POST

#### Using PUT to Create a Document
- **URL**: `http://$CLUSTER_IP:9200/products/_doc/1`
- **HTTP Method**: `PUT`
- **Behavior**: If the document with the specified ID (`1` in this case) does not exist, a new document is created. If the document already exists, it is updated.
- **Idempotency**: Idempotent, meaning that making the same request multiple times will have the same effect as making it once.
- **Example**:
  ```bash
  curl -X PUT "http://$CLUSTER_IP:9200/products/_doc/1" -H 'Content-Type: application/json' -d '{
      "product_id": 67890,
      "name": "Cozy Winter Sweater",
      "description": "Soft and stylish sweater for cold days",
      "price": 59.99,
      "category": "Apparel",
      "brand": "Trendy Threads"
  }'
  ```

#### Using POST to Create a Document
- **URL**: `http://$CLUSTER_IP:9200/products/_doc`
- **HTTP Method**: `POST`
- **Behavior**: Elasticsearch generates a unique ID for the new document. Each request creates a new document with a different ID.
- **Idempotency**: Not idempotent, as each request can create a new document with a different auto-generated ID.
- **Example**:
  ```bash
  curl -X POST "http://$CLUSTER_IP:9200/products/_doc" -H 'Content-Type: application/json' -d '{
      "product_id": 67891,
      "name": "Cozy Winter Sweater 2",
      "description": "Soft and stylish sweater for cold days",
      "price": 59.99,
      "category": "Apparel",
      "brand": "Trendy Threads"
  }'
  ```

### Update Operation: PUT vs POST

#### Using PUT to Update a Document
- **URL**: `http://$CLUSTER_IP:9200/products/_doc/1`
- **HTTP Method**: `PUT`
- **Behavior**: Replaces the entire document with the new data. If you only provide a subset of the fields, the missing fields will be set to null.
- **Example**:
  ```bash
  kubectl get svc elasticsearch -n efk -o jsonpath='{.spec.clusterIP}' | xargs -I {} curl -X PUT "http://{}:9200/products/_doc/1" -H 'Content-Type: application/json' -d '{
      "price": 129.99
  }'
  ```
  This will update the `price` field but will set all other fields to null if they are not provided.

#### Using the _update Endpoint with POST
- **URL**: `http://$CLUSTER_IP:9200/products/_update/1`
- **HTTP Method**: `POST`
- **Behavior**: Allows partial updates by specifying only the fields that need to be updated. This method does not replace the entire document.
- **Example**:
  ```bash
  kubectl get svc elasticsearch -n efk -o jsonpath='{.spec.clusterIP}' | xargs -I {} curl -X POST "http://{}:9200/products/_update/1" -H 'Content-Type: application/json' -d '{
      "doc": {
          "price": 129.99
      }
  }'
  ```
  This will update only the `price` field without affecting the other fields.

### Key Differences

- **PUT**:
  - Requires a specified ID in the URL.
  - Replaces the entire document if used for updates.
  - Idempotent when creating or updating documents.
  - Can be used for both creating and updating documents.

- **POST**:
  - Does not require a specified ID in the URL when creating documents; instead, Elasticsearch generates an ID.
  - Not idempotent when creating documents because each request generates a new ID.
  - When used with the `_update` endpoint, allows partial updates without replacing the entire document.

### Example Workflow

Here is an example workflow that demonstrates both `PUT` and `POST` methods for creating and updating documents:

```bash
CLUSTER_IP=$(kubectl get svc elasticsearch -n efk -o jsonpath='{.spec.clusterIP}')

# Create the index
curl -X PUT "http://$CLUSTER_IP:9200/products" -H 'Content-Type: application/json' -d '{
    "settings": {
        "number_of_shards": 1,
        "number_of_replicas": 1
    },
    "mappings": {
        "properties": {
            "product_id": {
                "type": "integer"
            },
            "name": {
                "type": "text"
            },
            "description": {
                "type": "text"
            },
            "price": {
                "type": "float"
            },
            "category": {
                "type": "text"
            },
            "brand": {
                "type": "text"
            }
        }
    }
}'

# Create a document using PUT with specified ID
curl -X PUT "http://$CLUSTER_IP:9200/products/_doc/1" -H 'Content-Type: application/json' -d '{
    "product_id": 67890,
    "name": "Cozy Winter Sweater",
    "description": "Soft and stylish sweater for cold days",
    "price": 59.99,
    "category": "Apparel",
    "brand": "Trendy Threads"
}'

# Update the document using PUT (replaces entire document)
kubectl get svc elasticsearch -n efk -o jsonpath='{.spec.clusterIP}' | xargs -I {} curl -X PUT "http://{}:9200/products/_doc/1" -H 'Content-Type: application/json' -d '{
    "price": 129.99
}'

# Update the document using POST with _update endpoint (partial update)
kubectl get svc elasticsearch -n efk -o jsonpath='{.spec.clusterIP}' | xargs -I {} curl -X POST "http://{}:9200/products/_update/1" -H 'Content-Type: application/json' -d '{
    "doc": {
        "price": 129.99
    }
}'

# Verify the update
kubectl get svc elasticsearch -n efk -o jsonpath='{.spec.clusterIP}' | xargs -I {} curl -X GET "http://{}:9200/products/_doc/1"
```

This workflow demonstrates how to create a document using `PUT`, update it using both `PUT` and the `_update` endpoint with `POST`, and verify the updates.


When updating a document in Elasticsearch, it's crucial to understand the differences between using `PUT` and `POST` methods, especially when it comes to partial updates.

### Using PUT to Update a Document

- **Behavior**: When you use `PUT` to update a document, you are essentially replacing the entire document with the new data. If you only provide a subset of the fields, the missing fields will be set to `null`.
- **Example**:
  ```bash
  kubectl get svc elasticsearch -n efk -o jsonpath='{.spec.clusterIP}' | xargs -I {} curl -X PUT "http://{}:9200/products/_doc/1" -H 'Content-Type: application/json' -d '{
      "price": 129.99
  }'
  ```
  In this example, if the original document had other fields like `name`, `description`, `category`, and `brand`, they would all be lost because only the `price` field is provided in the update.

### Using POST with the _update Endpoint

- **Behavior**: To perform a partial update without losing the existing data, you should use the `_update` endpoint with the `POST` method.
- **Example**:
  ```bash
  kubectl get svc elasticsearch -n efk -o jsonpath='{.spec.clusterIP}' | xargs -I {} curl -X POST "http://{}:9200/products/_update/1" -H 'Content-Type: application/json' -d '{
      "doc": {
          "price": 129.99
      }
  }'
  ```
  This method allows you to update only the specified fields (`price` in this case) without affecting the other fields of the document.

### Key Points

- **PUT**:
  - Replaces the entire document. If you only provide a subset of fields, the missing fields will be set to `null`.
  - Not suitable for partial updates if you want to preserve existing data.

- **POST with _update**:
  - Allows partial updates by specifying only the fields that need to be updated.
  - Preserves the existing data and only updates the specified fields.

### Correct Approach

If you want to update just the `price` field of a document without losing the other fields, you should use the `_update` endpoint with the `POST` method:

```bash
kubectl get svc elasticsearch -n efk -o jsonpath='{.spec.clusterIP}' | xargs -I {} curl -X POST "http://{}:9200/products/_update/1" -H 'Content-Type: application/json' -d '{
    "doc": {
        "price": 129.99
    }
}'
```

This way, you ensure that only the `price` field is updated, and the rest of the document remains intact.


How you can use Kibana's Dev Tools and Index Management for Elasticsearch operations:

### Kibana Dev Tools

**Dev Tools** in Kibana is a powerful interface that allows you to interact with your Elasticsearch cluster using the same API endpoints you would use with `curl` or other HTTP clients, but through a more user-friendly interface.

#### Key Features of Dev Tools

- **Console**: This tab allows you to execute Elasticsearch API requests directly. You can write and execute queries, create indexes, add documents, search, update, and delete documents.
  - Example: To create an index, you can use the Console tab and execute a `PUT` request.
    ```json
    PUT /products
    {
      "settings": {
        "number_of_shards": 1,
        "number_of_replicas": 1
      },
      "mappings": {
        "properties": {
          "product_id": {
            "type": "integer"
          },
          "name": {
            "type": "text"
          },
          "description": {
            "type": "text"
          },
          "price": {
            "type": "float"
          },
          "category": {
            "type": "text"
          },
          "brand": {
            "type": "text"
          }
        }
      }
    }
    ```

- **Search**: This tab provides a simpler way to construct and execute search queries without writing raw JSON.
  - Example: To search for documents with "sweater" in the name field, you can use the Search tab.
    ```json
    GET /products/_search
    {
      "query": {
        "match": {
          "name": "sweater"
        }
      }
    }
    ```

- **Index Management**: While Dev Tools does not directly manage indexes, you can use the Console tab to execute index management commands.
  - Example: To delete an index, you can use the Console tab.
    ```json
    DELETE /products
    ```

### Kibana Index Management

**Index Management** in Kibana is a dedicated section for managing your Elasticsearch indexes. It provides a graphical interface to create, delete, and manage indexes.

#### Key Features of Index Management

- **Create Index**: You can create a new index by specifying the index name, settings, and mappings.
  - Example: Go to **Stack Management** > **Index Management**, click on **Create index**, and fill in the necessary details.

- **Delete Index**: You can delete an existing index.
  - Example: Go to **Stack Management** > **Index Management**, find the index you want to delete, and click on the delete button.

- **Index Settings**: You can view and edit the settings of an existing index.
  - Example: Go to **Stack Management** > **Index Management**, find the index, and click on the settings icon to view or edit its settings.

### Comparison and Usage

#### When to Use Dev Tools

- **Complex Queries**: Use Dev Tools for complex queries or when you need to execute raw Elasticsearch API requests.
- **Direct API Access**: Use Dev Tools when you need direct access to the Elasticsearch API for operations like creating documents, updating documents, or executing custom queries.
- **Debugging**: Dev Tools is useful for debugging and testing Elasticsearch queries and operations.

#### When to Use Index Management

- **Index Creation and Deletion**: Use Index Management for creating and deleting indexes through a graphical interface.
- **Index Settings**: Use Index Management to view and edit index settings in a more user-friendly way.
- **Index Monitoring**: Use Index Management to monitor the health and status of our indexes.

### Example Workflow Using Kibana

Here is an example of how we can use both Dev Tools and Index Management in Kibana:

#### Create an Index Using Index Management

1. Go to **Stack Management** > **Index Management**.
2. Click on **Create index**.
3. Enter the index name, settings, and mappings.

#### Add a Document Using Dev Tools

1. Go to **Dev Tools** > **Console**.
2. Execute the following command:
   ```json
   PUT /products/_doc/1
   {
     "product_id": 67890,
     "name": "Cozy Winter Sweater",
     "description": "Soft and stylish sweater for cold days",
     "price": 59.99,
     "category": "Apparel",
     "brand": "Trendy Threads"
   }
   ```

#### Search for Documents Using Dev Tools

1. Go to **Dev Tools** > **Console**.
2. Execute the following command:
   ```json
   GET /products/_search
   {
     "query": {
       "match": {
         "name": "sweater"
       }
     }
   }
   ```

#### Delete an Index Using Index Management

1. Go to **Stack Management** > **Index Management**.
2. Find the index you want to delete.
3. Click on the delete button.

By using both Dev Tools and Index Management in Kibana, we can leverage the strengths of each tool to manage and interact with our Elasticsearch data efficiently.

- [Next] (https://github.com/BairiRohithReddy/EFK-Monitoring/blob/main/endpoints_elasticsearch.md)
