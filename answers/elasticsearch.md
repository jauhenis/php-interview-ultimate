# Elasticsearch Concepts

## Main Features
- **Distributed & Real-time**: Data is indexed and searchable almost instantly across a cluster of nodes.
- **RESTful API**: Interacts with the data using standard HTTP methods and JSON.
- **Full-Text Search**: Powered by Apache Lucene, it handles complex text searches (fuzzy matching, highlighting, stemming).
- **Document-Oriented**: Data is stored as JSON documents, making it schema-less (though schemas can be defined).
- **Inverted Index**: Instead of storing strings, it stores an index of terms and which documents contain them, making searching extremely fast.
- **Scalability**: Sharding and replication allow it to handle massive amounts of data and provide high availability.

---

## Analyzers
Analyzers are used during indexing and searching to process text. An analyzer consists of:
1. **Character Filters**: Modify the text before tokenization (e.g., removing HTML tags).
2. **Tokenizer**: Splits the text into tokens (usually words).
3. **Token Filters**: Modify tokens (e.g., lowercasing, removing stop words like "the" or "and", stemming).

### Standard Analyzer
The default analyzer that works well for most languages. It tokenizes on word boundaries and removes punctuation.

### Custom Analyzers
You can define your own analyzers to handle specific cases, like indexing email addresses or product IDs.

---

## Core Concepts
- **Cluster**: a group of nodes that share data, identified by a name.
- **Node**: one Elasticsearch server. Nodes have roles such as master-eligible, data, ingest and coordinating.
- **Index**: a collection of JSON documents with a common mapping, for example `products` or `logs-2026.11.15`.
- **Document**: a JSON object in an index, identified by `_id`.
- **Mapping**: the field names and types of an index and how each field is analyzed.
- **Shard**: an index is split into primary shards, and each one is a separate Lucene index. Shards are distributed among nodes, so the index can grow beyond one server.
- **Replica**: a copy of a primary shard on another node. Replicas protect against node failure and also serve searches.

Mapping types were removed in Elasticsearch 8.0, so the old "index = database, type = table" analogy no longer applies.

## When to Use Elasticsearch
Elasticsearch suits full-text search with relevance ranking, typo tolerance and synonyms, catalog filtering with facet counts, log and metrics analytics (the Elastic Stack) and aggregations over large datasets.

It should not be the primary source of truth:
- It has no multi-document transactions and no joins, apart from the limited `nested` and `join` field types.
- A new document becomes searchable only after a refresh.
- Changing a field type requires a full reindex.
- It has no constraints or foreign keys.

A common setup keeps the data in MySQL or PostgreSQL and treats Elasticsearch as a search index that is synced from the database and can be rebuilt at any time.

## `text` vs `keyword`
- **`text`**: the value is analyzed into tokens, for example lowercased and stemmed. Use it for full-text search on titles and descriptions. It cannot be used efficiently for sorting or aggregations.
- **`keyword`**: the value is stored as one exact token. Use it for filters, sorting and aggregations on statuses, tags, emails and IDs.

Without an explicit mapping, a string is indexed as a `text` field with a `.keyword` sub-field. Defining the mapping explicitly avoids surprises:

```json
{
    "mappings": {
        "properties": {
            "title": { "type": "text", "fields": { "raw": { "type": "keyword" } } },
            "status": { "type": "keyword" },
            "price": { "type": "scaled_float", "scaling_factor": 100 }
        }
    }
}
```

## `match` vs `term`
- **`match`** passes the search string through the field's analyzer and searches for the resulting tokens. A `match` query for "Red Shoes" on `title` finds documents with "red" or "shoes". Add `"operator": "and"` to require both.
- **`term`** searches for the exact value without analysis. Use it on `keyword`, numeric, date and boolean fields.

A `term` query for "Red Shoes" on a `text` field finds nothing, because the index contains the tokens "red" and "shoes", not the original string.

## Query Context, Filter Context and `bool`
- **Query context** calculates how well a document matches and sets its `_score`.
- **Filter context** only checks whether a document matches. It calculates no score, and Elasticsearch caches frequently used filters.

A `bool` query combines clauses:
- `must`: must match and adds to the score.
- `should`: adds to the score when it matches. Without `must` or `filter`, at least one `should` clause must match.
- `filter`: must match, without scoring.
- `must_not`: must not match.

```json
{
    "query": {
        "bool": {
            "must": [{ "match": { "title": "running shoes" } }],
            "filter": [
                { "term": { "status": "active" } },
                { "range": { "price": { "gte": 50, "lte": 150 } } }
            ],
            "must_not": [{ "term": { "brand": "noname" } }]
        }
    }
}
```

## Relevance Scoring
Elasticsearch uses BM25 by default. A document's score depends on:
- **Term frequency**: more occurrences of the term in the field raise the score, with diminishing returns.
- **Inverse document frequency**: a term that is rare across the index weighs more than a common one.
- **Field length**: a match in a short field, such as a title, weighs more than a match in a long body.

Boost fields with `"title^3"` in a `multi_match` query, or adjust scores by popularity, freshness or distance with `function_score`. `"explain": true` shows how a score was calculated. Document frequencies are counted per shard, so scores can look inconsistent in small indices.

## Deep Pagination
- **`from` and `size`**: each shard returns `from + size` documents to the coordinating node, so deep pages are expensive. By default, `from + size` cannot exceed 10,000 (`index.max_result_window`).
- **`search_after` with a point in time (PIT)**: pass the sort values of the last document on the previous page. Every page costs the same, and the PIT keeps a consistent view of the index between requests. You cannot jump to an arbitrary page number.
- **`scroll`**: an older API for exporting large result sets. Elastic now recommends `search_after` with PIT instead.

Raising `max_result_window` far above the default can exhaust heap memory.

## Aggregations
Aggregations compute statistics over the documents a query matches, in the same request as the search:
- **Metric aggregations**: `avg`, `sum`, `min`, `max`, `stats`, `cardinality` (approximate count of unique values) and `percentiles`.
- **Bucket aggregations**: `terms`, `range`, `histogram` and `date_histogram` group documents into buckets.
- **Pipeline aggregations**: work on the output of other aggregations, for example `derivative` or `bucket_sort`.

Aggregations can be nested, for example average price per category per month. Online store facets such as "Brand: Nike (120)" are `terms` aggregations. Aggregate on `keyword`, numeric or date fields.

## Bulk Indexing from PHP
The example uses the official client `elasticsearch/elasticsearch` 8.x, installed with Composer, without a framework. `$products` is an array of rows loaded from the database.

```php
use Elastic\Elasticsearch\ClientBuilder;

$client = ClientBuilder::create()->setHosts(['http://localhost:9200'])->build();

foreach (array_chunk($products, 1000) as $chunk) {
    $body = [];
    foreach ($chunk as $product) {
        $body[] = ['index' => ['_index' => 'products', '_id' => $product['id']]];
        $body[] = ['title' => $product['title'], 'price' => $product['price']];
    }

    $response = $client->bulk(['body' => $body])->asArray();
    if ($response['errors']) {
        // Some operations failed: inspect $response['items'] and retry or log them.
    }
}
```

- Keep each bulk request at a few megabytes and tune the size by measuring.
- `_bulk` returns HTTP 200 even when some operations fail, so always check `errors`.
- For a large initial import, set `refresh_interval` to `-1` and `number_of_replicas` to `0`, then restore both.

## Cluster Health
`GET _cluster/health` returns one of three statuses:
- **Green**: all primary and replica shards are allocated.
- **Yellow**: all primary shards are allocated, but some replicas are not. All data is available, but a node failure could lose some. A single-node cluster with `number_of_replicas: 1` is always yellow, because a replica cannot be on the same node as its primary.
- **Red**: at least one primary shard is not allocated, so some data is unavailable.

`GET _cat/shards?v` and `GET _cluster/allocation/explain` show why a shard is unassigned. Common causes are a failed node, a full disk and allocation rules that cannot be met. When a disk reaches the flood-stage watermark (95% by default), Elasticsearch makes the indices on it read-only.

## Syncing with the Primary Database
- **Dual write**: the application writes to the database and then to Elasticsearch. If the second write fails, the two diverge.
- **Queue**: after the database commit, publish an event such as `ProductUpdated`, and a worker reloads the record and indexes it. The Transactional Outbox pattern guarantees the event is published: it is written to an `outbox` table in the same transaction and published later.
- **Change data capture (CDC)**: a tool such as Debezium reads the database's binlog or WAL and streams changes to Kafka, and a consumer indexes them. It also catches changes made outside the application, but adds infrastructure.
- **Periodic sync**: a scheduled job reindexes rows changed since `updated_at`. It adds delay and misses deleted rows.

In every case, use the database primary key as `_id` so indexing is idempotent, protect against out-of-order updates with external versioning or by reloading the latest state, and keep a full reindex procedure for recovery.

## Changing a Mapping Without Downtime
You can add new fields to a mapping, but you cannot change the type of an existing field. Reindex behind an alias instead:

1. The application queries an alias such as `products`, not a concrete index such as `products_v1`.
2. Create `products_v2` with the new mapping.
3. Copy the data with the `_reindex` API or by reimporting from the database.
4. Apply changes made during the copy, for example by writing to both indices or replaying events.
5. Switch the alias atomically:

```json
POST _aliases
{
    "actions": [
        { "remove": { "index": "products_v1", "alias": "products" } },
        { "add": { "index": "products_v2", "alias": "products" } }
    ]
}
```

6. Delete the old index once the new one is verified. To roll back, switch the alias back.

`"dynamic": "strict"` rejects documents with unknown fields, which prevents wrong guessed types and a mapping with thousands of fields.

## Shards and Replicas
- **Primary shards** are fixed when the index is created. Changing their number requires a reindex or the `_split` and `_shrink` APIs. The default is 1.
- **Replicas** can be changed at any time. Each one adds fault tolerance and search capacity, but costs disk space and indexing work.

Guidelines:
- Aim for shards of roughly 10 to 50 GB.
- Avoid many small shards: every shard uses heap memory, and a search runs on every shard of the index.
- For logs and other time-based data, use data streams with Index Lifecycle Management (ILM). It rolls over to a new index by size or age and moves old indices through the hot, warm, cold and delete phases.
- In production, run at least three master-eligible nodes and at least one replica.

## Refresh, Flush and Translog
Elasticsearch search is near real-time:

1. A new document goes into an in-memory buffer and into the translog, a log on disk that keeps it durable.
2. A **refresh**, every second by default (`refresh_interval`), writes the buffer to a new Lucene segment that search can see. A `GET` by `_id` finds the document even before the refresh.
3. A **flush** commits the segments to disk and clears the translog.
4. In the background, small segments are merged into larger ones, and deleted documents are removed.

- When a user must see their change immediately, add `?refresh=wait_for` to the write request. `?refresh=true` under load creates many small segments.
- For heavy indexing, increase `refresh_interval`, for example to `30s`.
- An update marks the old document as deleted and writes a new one, so frequently updated documents, such as counters, are expensive.

## Autocomplete and Typo Tolerance
- **Fuzzy matching**: `"fuzziness": "AUTO"` in a `match` query allows up to two edits depending on the word length, so "iphnoe" finds "iphone".
- **Edge n-grams**: an index-time analyzer stores word prefixes ("lap", "lapt", "lapto", "laptop"), so prefix search is a simple term lookup. Use a standard analyzer at search time.
- **`search_as_you_type`**: a field type that creates the prefix sub-fields automatically. Query it with a `multi_match` query of type `bool_prefix`.
- **Completion suggester**: a `completion` field kept in memory. It is the fastest option for search box suggestions, but supports only prefix matching.
- **Synonyms and language analyzers**: a synonym filter ("tv" and "television") and a language analyzer such as `russian` find more relevant documents.

Search engines often combine these: `search_as_you_type` for suggestions, `fuzziness` for the main search, and a boost for exact matches so they rank above fuzzy ones.
