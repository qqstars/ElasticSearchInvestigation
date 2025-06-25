# ElesticSearchInvestigation

# Install
  - Run command with ***PowerShell***:
  ```
  docker run -d --name elasticsearch -p 9200:9200 -e "discovery.type=single-node" -v "${PWD}/elasticsearch_http.yml:/usr/share/elasticsearch/config/elasticsearch.yml" elasticsearch:8.13.4
  ```
  - OR, alternately, using docker-compose.yml, by running command from the root folder:
  ```
  docker compose up -d
  ```

# Basic test
  - Send request to set the data: POST http://localhost:9200/products/_doc/1 
  ```
  JSON:
  {
    "name": "Angular Hoodie",
    "price": 49.99,
    "tags": ["angular", "clothing"]
  }
  ```
  - Send request to get the data: GET http://localhost:9200/products/_search?q=angular
  - More queries can be found: https://www.elastic.co/docs/explore-analyze/query-filter/languages/querydsl

# Sample 1 - Using Query DSL:
  - Add multiple Angular related product information:
  - Send request to set the data: 
  ```
  POST http://localhost:9200/products/_doc/1
  JSON:
  {
    "name": "Angular Hoodie",
    "price": 49.99,
    "tags": ["angular", "clothing"]
  }

  POST http://localhost:9200/products/_doc/2
  JSON:
  {
    "name": "Angular Beginner",
    "price": 32.99,
    "tags": ["angular", "beginner"]
  }

  POST http://localhost:9200/products/_doc/3
  JSON:
  {
    "name": "Angular and More Frameworks",
    "price": 78.99,
    "tags": ["beginner"]
  }
  ```
  - Get all Angular related data:
  ```
  GET http://localhost:9200/products/_search?q=angular
  ```
  - Get with combined condition, with name include "Angular", and price greater than 40, tags include exactly the same as "angular":
  ```
  GET http://localhost:9200/products/_search
  JSON
  {
    "query": {
      "bool": {
        "must": [
          { "match": { "name": "angular" }}
        ],
      "filter": [
          { "term":  { "tags": "angular" }},
          { "range": { "price": { "gte": 40 }}}
        ]
      }
    }
  }
  ```

# Sample 2 - Using ESQL:
  - Use the same data as Sample 1.
  - Query with ESQL, check doc: https://www.elastic.co/docs/api/doc/elasticsearch/group/endpoint-esql and https://www.elastic.co/docs/reference/query-languages/esql/functions-operators/operators
  ```
  POST http://localhost:9200/_query?format=txt
  JSON:
  {
    "query": "FROM products | WHERE name LIKE \"Angular*\" AND tags != ? AND price >= ?",
    "params": [
      { "type": "keyword", "value": "angular" },
      { "type": "double", "value": 40 }
    ]
  }
  ```

# Sample 3 - Use Kibana:
  - Open the Kibana that configured with installation, with port 5601: http://localhost:5601/
  - From Analytics -> Discover
  - Click top-left blue button, and "Create a data view"
  - Set the "Index pattern" as the index name, say, "Products" here, so we can set as "Products*"
  - Select from the blue button, with "Products"
  - All data will be shown by default.
  - We can filter with KQL:
  ```
  name: Angular AND tags: angular AND price >= 40
  ```