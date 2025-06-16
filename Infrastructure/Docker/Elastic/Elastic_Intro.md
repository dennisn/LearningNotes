# Elastic Introduction

## Overview
  - Elastic Stack (ELK stack): collections of open source software used to collect, store and analyze data
    + ElasticSearch: core database for the stack
    + Kibana: UI for elasticSearch
    + Logstash: extra data from any source, then transform/normalise it into a common format
    + Beats: lightweight agents, reside at specific endpoints, to ship the data into our stack using common format: elastic common schema
    + Endgame: endpoint security
  - Elasticsearch: based on Apache Lucene (for scaleable full-text search)
    + Documents are grouped into indexes (often by datasource).
    + Each document contains name-value pairs as field

## Ingest data into Elasticsearch
  - Kibana components:
    + Discover: list of documents ingested
    + Visualize: building graph from documents
    + Dashboard: grouping graphs together
    + Maps: Visualize geo-location documents
    + Canvas: pretty up dashboard for presentation
    + Machine learning: advance statistical algorithms
  - Kibana additional interfaces:
    + Observability: view of data for real-time monitor
    + Security: group security data (e.g. network, host, etc.)
    + Management: monitor existing infrastructure, setting & indexes
  - Data pipeline
    + create index (via Kibana, auto-matic when upload documents, or via API endpoint)
    + push data via API endpoint, or upload data to index --> Elastic search will recognise the format and parsed then index the documents
    + Kibana includes various pre-defined beats for pulling data from sources into Elasticsearch
    + index-pattern: create through management interface, to group related indixes together, also specify which field will be used as the timestamp

## Datatypes & document mappings in Elasticsearch
  - Search function for: existence, value matching pattern or specific value, etc.
  - Mapping of the data type: allow more semantic operation on the field (for example: IP address is not simply a string/number, and allow for subnet search)
    + Mapping must be done before ingest data into index (once data are ingested, mapping is fixed)
    + To change mapping of an index, you can only create a different index to replace the current one
  - Index template: define settings & mappings for any index from a data source
    + also define how new index is "rolled-over", either by time or size
  - Elastic Common Schema: common name & type for often seen data field --> ease of usage 

## Using analyzers in Elasticsearch
  - Analyzer: algorithm to parse the ingest data into text fields to store in index
    + There are many built-in analyzers, from simple to complex
  - Custom analyzer must be created with a custom name in the settings of the index that use it