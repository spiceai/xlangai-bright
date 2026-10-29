# BRIGHT benchmarks
[BRIGHT](https://huggingface.co/datasets/xlangai/BRIGHT) is a text retrieval benchmark designed for reasoning beyond mere keyword overlap. Text search models alone will not score highly. 

In this spice app, full-text-search (FTS) is combined with a variety of rerank models to improve scores. Search pipelines are defined in `spicepod.yaml` using custom user-defined SQL functions. For example
```yaml
functions:
  - name: luna_rerank
    from: sql
    kind: table
    description: BM25 top 100 reranked by gpt-6-luna as an LLM judge, listwise.
    signature:
      args: [{ name: q, type: utf8 }]
      returns:
        - { name: id, type: utf8 }
        - { name: score, type: float64 }
    body: |
      SELECT id, rerank_score as _score
      FROM rerank(
        text_search(docs, q, content, limit => 100),
        model => 'luna',
        document => 'content'
      )
```

Can be invoked in SQL 
```sql
select id, _score from luna_rerank("Do animals exhibit handedness (paw-ness?) preference?") limit 5
```
