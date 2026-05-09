⭐ If this helps you, please star this repo!

## 📁 Repository Structure

```
elasticsearch-cheatsheet/
│
├── README.md                      ← This file — master index
├── elasticsearch-commands.md      ← Core production commands reference
│
└── til/                           ← Today I Learned — daily deep dives
    ├── 2026-04-22.md              ← Write Path Internals
    ├── 2026-04-23.md              ← Sizing & Capacity Planning
    ├── 2026-04-24.md              ← ES|QL Complete Reference
    ├── 2026-04-25.md              ← Ingest Processors
    ├── 2026-04-26.md              ← ELK on Docker
    ├── 2026-04-27.md              ← Templates + Data Streams + ILM
    ├── 2026-04-28.md              ← Snapshot & Restore
    ├── 2026-04-29.md              ← Security & RBAC
    ├── 2026-04-30.md              ← CCS & CCR
    ├── 2026-05-01.md              ← Aggregations Deep Dive
    ├── 2026-05-02.md              ← Troubleshooting Guide
    └── 2026-05-03.md              ← Search Internals & BM25
```

---

## 📚 Core Reference

### [elasticsearch-commands.md](./elasticsearch-commands.md)
Production-grade command reference covering cluster health, index operations, shard management, ILM, data streams, ingest pipelines, search queries, ES|QL, snapshot & restore, and cross cluster search.

---

## 📅 Today I Learned (TIL) - Index (In Progress)

| Date | Topic | Key Areas Covered |
|---|---|---|
| [Apr 22](./til/2026-04-22.md) | **Elasticsearch Write Path Internals** | Refresh vs Flush vs Translog, memory-to-disk flow, tuning tips |
| [Apr 23](./til/2026-04-23.md) | **Sizing & Capacity Planning** | Memory:data ratios, hot-warm-cold sizing formulas, worked examples, AutoOps |
| [Apr 24](./til/2026-04-24.md) | **ES\|QL — Complete Functions & Operators** | All 13 function categories, 150+ functions, commands, real observability examples |
| [Apr 25](./til/2026-04-25.md) | **Ingest Processors — Complete Reference** | All 10 processor categories, pipeline skeleton, Nginx example, testing with _simulate |
| [Apr 26](./til/2026-04-26.md) | **ELK Stack 9.x on Docker** | Single node, 3-node cluster, Filebeat, Elastic Agent, Fleet, APM, production checklist |
| [Apr 27](./til/2026-04-27.md) | **Index Templates + Component Templates + Data Streams + ILM** | Full production setup, naming conventions, priority system, day-2 operations |
| [Apr 28](./til/2026-04-28.md) | **Snapshot & Restore** | S3/GCS/Azure repos, SLM policies, searchable snapshots, DR runbook, cost comparison |
| [Apr 29](./til/2026-04-29.md) | **Security & RBAC** | TLS, roles, users, field-level security, document-level security, API keys, audit logging |
| [Apr 30](./til/2026-04-30.md) | **Cross Cluster Search & Replication** | CCS setup, CCR auto-follow, DR failover runbook, geo-distribution patterns |
| [May 01](./til/2026-05-01.md) | **Aggregations Deep Dive** | Metric, bucket, pipeline aggs, composite, significant terms, golden signals dashboard |
| [May 02](./til/2026-05-02.md) | **Troubleshooting Guide** | RED/YELLOW diagnosis, OOM/heap, slow queries, shard issues, rejection errors |
| [May 03](./til/2026-05-03.md) | **Search Internals & BM25** | BM25 algorithm, IDF/TF explained, query vs filter context, explain API, function_score |

---

## 🔗 Key Official Resources

| Resource | Link |
|---|---|
| Elasticsearch Documentation | [📖](https://www.elastic.co/docs/reference/elasticsearch) |
| ES\|QL Functions & Operators | [📖](https://www.elastic.co/docs/reference/query-languages/esql/esql-functions-operators) |
| Ingest Processor Reference | [📖](https://www.elastic.co/docs/reference/enrich-processor) |
| ILM Documentation | [📖](https://www.elastic.co/guide/en/elasticsearch/reference/current/index-lifecycle-management.html) |
| Snapshot & Restore | [📖](https://www.elastic.co/guide/en/elasticsearch/reference/current/snapshot-restore.html) |
| Security Guide | [📖](https://www.elastic.co/guide/en/elasticsearch/reference/current/secure-cluster-tutorial.html) |
| Cross Cluster Search | [📖](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-cross-cluster-search.html) |
| Aggregations Reference | [📖](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-aggregations.html) |
| Tune for Search Speed | [📖](https://www.elastic.co/guide/en/elasticsearch/reference/current/tune-for-search-speed.html) |
| Tune for Indexing Speed | [📖](https://www.elastic.co/guide/en/elasticsearch/reference/current/tune-for-indexing-speed.html) |
| Elasticsearch Sizing PDF | [📖](https://www.elastic.co/pdf/elasticsearch-sizing-and-capacity-planning.pdf) |
| Elastic Discuss Forum | [💬](https://discuss.elastic.co/) |
| Elastic Blog | [📰](https://www.elastic.co/blog) |
| Elastic Search Labs | [🔬](https://www.elastic.co/search-labs) |

---

---

## 💬 Discussions & Feedback

Have a question, suggestion, or want to discuss any topic?

👉 [Start a Discussion](https://github.com/elksearch/elasticsearch-cheatsheet/discussions)

Found an error or want to suggest a new topic?

👉 [Open an Issue](https://github.com/elksearch/elasticsearch-cheatsheet/issues)

*This repository is actively maintained and updated daily.*

---

## ⚠️ Disclaimer

This repository contains notes, examples and references based on my **personal learning 
and hands-on experience** with Elasticsearch and the Elastic Stack.

- Content is provided **as-is** for educational purposes only
- Always refer to the **[official Elastic documentation](https://www.elastic.co/docs)** 
  for production use
- Commands and configurations should be **tested in a non-production environment first**
- The author is not responsible for any issues arising from the use of this content
- Elastic Stack features and APIs may change — verify against your specific version

> *When in doubt, trust the [official docs](https://www.elastic.co/docs) over this repo.*

