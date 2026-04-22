
## 📑 Table of Contents

1. [Cluster Health & Info](#1-cluster-health--info)
2. [Index Operations](#2-index-operations)
3. [Shard Management](#3-shard-management)
4. [Index Lifecycle Management (ILM)](#4-index-lifecycle-management-ilm)
5. [Data Streams](#5-data-streams)
6. [Ingest Pipelines](#6-ingest-pipelines)
7. [Search & Queries](#7-search--queries)
8. [ES|QL](#8-esql)
9. [Snapshot & Restore](#9-snapshot--restore)
10. [Cross Cluster Search (CCS)](#10-cross-cluster-search-ccs)

---

## 1. Cluster Health & Info

```bash
# Cluster health
GET _cluster/health
GET _cluster/health?level=indices

# Cluster settings
GET _cluster/settings
GET _cluster/settings?include_defaults=true

# Node info & stats & shards
GET _nodes
GET _nodes/stats
GET _nodes/stats/indices,os,jvm
GET /_cat/shards/*?v&h=index,shard,prirep,state,store,ip,node
GET /_cat/nodes?v=true&h=name,heap.current,heap.max,ram.current,ram.max,uptime

# Pending tasks
GET _cluster/pending_tasks

# Allocation explain (why is a shard unassigned?)
GET _cluster/allocation/explain
```

---

## 2. Index Operations

```bash
# List all indices
GET _cat/indices?v&s=index
GET _cat/indices?v&h=index,health,status,pri,rep,docs.count,store.size

# Create index with settings
PUT my-index
{
  "settings": {
    "number_of_shards": 1,
    "number_of_replicas": 1
  }
}

# Index mapping
GET my-index/_mapping
PUT my-index/_mapping
{
  "properties": {
    "timestamp": { "type": "date" },
    "message":   { "type": "text" },
    "level":     { "type": "keyword" }
  }
}

# Delete index
DELETE my-index

# Close / Open index
POST my-index/_close
POST my-index/_open

# Reindex
POST _reindex
{
  "source": { "index": "old-index" },
  "dest":   { "index": "new-index" }
}

# Index aliases
POST _aliases
{
  "actions": [
    { "add": { "index": "my-index-v2", "alias": "my-index" } },
    { "remove": { "index": "my-index-v1", "alias": "my-index" } }
  ]
}
```

---

## 3. Shard Management

```bash
# List shards
GET _cat/shards?v
GET _cat/shards/my-index?v&s=shard

# Unassigned shards
GET _cat/shards?v&h=index,shard,prirep,state,node&s=state

# Manually move a shard
POST _cluster/reroute
{
  "commands": [
    {
      "move": {
        "index": "my-index",
        "shard": 0,
        "from_node": "node-1",
        "to_node":   "node-2"
      }
    }
  ]
}

# Force allocate unassigned shard (last resort)
POST _cluster/reroute
{
  "commands": [
    {
      "allocate_stale_primary": {
        "index":          "my-index",
        "shard":          0,
        "node":           "node-1",
        "accept_data_loss": true
      }
    }
  ]
}

# Shard allocation settings
PUT _cluster/settings
{
  "persistent": {
    "cluster.routing.allocation.enable": "all"
  }
}
```

---

## 4. Index Lifecycle Management (ILM)

```bash
# List all ILM policies
GET _ilm/policy

# Create ILM policy (hot-warm-cold-delete)
PUT _ilm/policy/my-logs-policy
{
  "policy": {
    "phases": {
      "hot": {
        "min_age": "0ms",
        "actions": {
          "rollover": {
            "max_primary_shard_size": "50gb",
            "max_age": "30d"
          },
          "set_priority": { "priority": 100 }
        }
      },
      "warm": {
        "min_age": "30d",
        "actions": {
          "shrink":   { "number_of_shards": 1 },
          "forcemerge": { "max_num_segments": 1 },
          "set_priority": { "priority": 50 }
        }
      },
      "cold": {
        "min_age": "60d",
        "actions": {
          "freeze": {},
          "set_priority": { "priority": 0 }
        }
      },
      "delete": {
        "min_age": "90d",
        "actions": {
          "delete": {}
        }
      }
    }
  }
}

# Check ILM status of an index
GET my-index/_ilm/explain

# Retry failed ILM step
POST my-index/_ilm/retry

# Move index to next ILM step manually
POST _ilm/move/my-index
{
  "current_step": {
    "phase":  "hot",
    "action": "rollover",
    "name":   "check-rollover-ready"
  },
  "next_step": {
    "phase":  "warm",
    "action": "shrink",
    "name":   "shrink"
  }
}
```

---

## 5. Data Streams

```bash
# List data streams
GET _data_stream
GET _data_stream/my-logs-*

# Create index template for data stream
PUT _index_template/my-logs-template
{
  "index_patterns": ["my-logs-*"],
  "data_stream": {},
  "template": {
    "settings": {
      "number_of_shards":   1,
      "number_of_replicas": 1,
      "index.lifecycle.name": "my-logs-policy"
    },
    "mappings": {
      "properties": {
        "@timestamp": { "type": "date" },
        "message":    { "type": "text" },
        "level":      { "type": "keyword" }
      }
    }
  }
}

# Manually rollover a data stream
POST my-logs-stream/_rollover

# Delete a data stream
DELETE _data_stream/my-logs-stream
```

---

## 6. Ingest Pipelines

```bash
# List all pipelines
GET _ingest/pipeline

# Create pipeline
PUT _ingest/pipeline/my-pipeline
{
  "description": "Parse and enrich logs",
  "processors": [
    {
      "grok": {
        "field": "message",
        "patterns": ["%{TIMESTAMP_ISO8601:timestamp} %{LOGLEVEL:level} %{GREEDYDATA:msg}"]
      }
    },
    {
      "date": {
        "field":  "timestamp",
        "formats": ["ISO8601"]
      }
    },
    {
      "remove": {
        "field": "timestamp"
      }
    },
    {
      "set": {
        "field":  "environment",
        "value":  "production"
      }
    }
  ]
}

# Test pipeline without indexing
POST _ingest/pipeline/my-pipeline/_simulate
{
  "docs": [
    {
      "_source": {
        "message": "2024-01-15T10:30:00Z ERROR Something went wrong"
      }
    }
  ]
}

# Delete pipeline
DELETE _ingest/pipeline/my-pipeline
```

---

## 7. Search & Queries

```bash
# Basic match query
GET my-index/_search
{
  "query": {
    "match": {
      "message": "error timeout"
    }
  }
}

# Bool query (must + filter + must_not)
GET my-index/_search
{
  "query": {
    "bool": {
      "must":   [ { "match":  { "message": "error" } } ],
      "filter": [ { "term":   { "level": "ERROR" } },
                  { "range":  { "@timestamp": { "gte": "now-1d/d" } } } ],
      "must_not": [ { "term": { "env": "staging" } } ]
    }
  }
}

# Aggregations — count by level
GET my-index/_search
{
  "size": 0,
  "aggs": {
    "by_level": {
      "terms": { "field": "level", "size": 10 }
    }
  }
}

# Explain scoring for a document
GET my-index/_explain/doc-id
{
  "query": { "match": { "message": "error" } }
}
```

---

## 8. ES|QL

```bash
# Basic ES|QL query
POST _query
{
  "query": "FROM my-logs-* | WHERE level == \"ERROR\" | LIMIT 10"
}

# Aggregate with ES|QL
POST _query
{
  "query": """
    FROM my-logs-*
    | WHERE @timestamp >= NOW() - 1 DAY
    | STATS count = COUNT(*) BY level
    | SORT count DESC
  """
}

# ES|QL with EVAL for computed fields
POST _query
{
  "query": """
    FROM my-logs-*
    | EVAL hour = DATE_TRUNC(1 hours, @timestamp)
    | STATS error_count = COUNT(*) BY hour
    | SORT hour ASC
  """
}
```

---

## 9. Snapshot & Restore

```bash
# Register repository (S3)
PUT _snapshot/my-s3-repo
{
  "type": "s3",
  "settings": {
    "bucket":   "my-es-backups",
    "region":   "ap-south-1",
    "base_path": "elasticsearch"
  }
}

# Take snapshot
PUT _snapshot/my-s3-repo/snapshot-2024-01-15
{
  "indices": "my-logs-*",
  "ignore_unavailable": true,
  "include_global_state": false
}

# List snapshots
GET _snapshot/my-s3-repo/_all

# Restore snapshot
POST _snapshot/my-s3-repo/snapshot-2024-01-15/_restore
{
  "indices": "my-logs-2024.01.15",
  "rename_pattern": "(.+)",
  "rename_replacement": "restored-$1"
}

# Delete snapshot
DELETE _snapshot/my-s3-repo/snapshot-2024-01-15
```

---

## 10. Cross Cluster Search (CCS)

```bash
# Configure remote cluster
PUT _cluster/settings
{
  "persistent": {
    "cluster.remote.my-remote-cluster.seeds": [
      "remote-node-1:9300"
    ]
  }
}

# Search across clusters
GET my-remote-cluster:my-logs-*,my-logs-*/_search
{
  "query": {
    "match": { "level": "ERROR" }
  }
}

# Check remote cluster info
GET _remote/info
```

---

## ⚡ Quick Tips

| Tip | Command |
|---|---|
| Check hot threads | `GET _nodes/hot_threads` |
| Disk watermarks | `GET _cluster/settings?include_defaults=true&filter_path=**.watermark` |
| Recovery status | `GET _cat/recovery?v&active_only=true` |
| Field data usage | `GET _nodes/stats/indices/fielddata` |
| Clear field data cache | `POST _cache/clear?fielddata=true` |
| Flush index | `POST my-index/_flush` |
| Force merge | `POST my-index/_forcemerge?max_num_segments=1` |

---
