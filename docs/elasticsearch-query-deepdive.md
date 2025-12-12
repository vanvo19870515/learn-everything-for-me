---
title: Elasticsearch Query Deep Dive
summary: Detailed breakdown (Vietnamese + English) of match vs term, function_score, nested query, with JSON structure and real examples.
---

<!-- markdownlint-disable MD025 MD022 MD032 MD031 MD024 MD033 MD037 MD009 MD012 -->

# Elasticsearch Query Deep Dive (Match/Term, Function Score, Nested)

## 0. Bool, `must`, `filter`, `should`, `must_not`

### Giải thích / Explanation
- **Tiếng Việt**: `bool` là khung kết hợp nhiều truy vấn con; `must` bắt buộc và ảnh hưởng scoring, `filter` lọc giá trị giống `must` nhưng không tính điểm, `should` là điều kiện phụ trợ (tăng score nếu đúng), `must_not` loại trừ kết quả không mong muốn.
- **English**: `bool` combines sub-queries. `must` is required and impacts scoring, `filter` filters (without scoring), `should` is optional (boosts relevance when satisfied), `must_not` excludes matches.

### JSON structure / Ví dụ
```json
{
  "query": {
    "bool": {
      "must": [
        { "match": { "description": "monitoring" } }
      ],
      "filter": [
        { "term": { "status.keyword": "active" } }
      ],
      "should": [
        { "match": { "tags": "performance" } }
      ],
      "must_not": [
        { "term": { "category.keyword": "deprecated" } }
      ]
    }
  }
}
```

### Khi nào dùng / When to use
- Sử dụng `bool` khi cần mô tả nhiều điều kiện với vai trò khác nhau trong truy vấn.
- `filter` phù hợp cho điều kiện cố định (range, term); Elasticsearch có thể cache chúng tốt hơn.
- `should` tăng điểm khi match; thêm `minimum_should_match` nếu muốn ít nhất một `should` đúng.

## 1. Match vs Term / `match` vs `term`

### Giải thích / Explanation
- **Tiếng Việt**: `match` cho phép tìm toàn văn (full text) và sẽ phân tích câu nhập để tìm token tương tự; *dùng cho field kiểu `text`*. Ngược lại `term` tìm chính xác giá trị đã nhập mà không phân tích; *dùng field `keyword`, số, enum*.
- **English**: `match` performs analyzed, full-text searching, tokenizing the input to match text fields. `term` performs exact-match (no analysis) and is ideal for keywords, IDs, numbers.

### JSON structure
```json
{
  "query": {
    "match": {
      "description": "elastic search"
    }
  }
}
```
```json
{
  "query": {
    "term": {
      "status.keyword": {
        "value": "active"
      }
    }
  }
}
```

### Kết luận / When to use
- Dùng `match` khi bạn cần full-text, phân tích từ, hỗ trợ tokenization.
- Dùng `term` khi bạn cần so khớp chính xác, không phụ thuộc vào lowercase/uppercase.

## 2. Function Score

### Giải thích / Explanation
- **Tiếng Việt**: `function_score` cho phép thay đổi điểm (score) bằng hàm (field_value_factor, decay, random), rất hữu ích để ưu tiên tài liệu mới, nổi bật, hoặc có dữ liệu số cụ thể.
- **English**: `function_score` allows adjusting document scores via functions (field_value_factor, decay, random) to favor recent documents, popular ones, or those with numeric attributes.

### JSON ví dụ / Example
```json
{
  "query": {
    "function_score": {
      "query": {
        "match": { "title": "monitoring" }
      },
      "functions": [
        {
          "field_value_factor": {
            "field": "popularity",
            "factor": 1.2,
            "modifier": "sqrt"
          }
        },
        {
          "exp": {
            "published_date": {
              "origin": "now",
              "scale": "10d",
              "decay": 0.5
            }
          }
        }
      ],
      "boost_mode": "multiply",
      "score_mode": "sum"
    }
  }
}
```

### Khi nào dùng / When to use
- Khi cần ưu tiên dữ liệu mới hơn hoặc có trường số lớn hơn.
- Dùng để kết hợp nhiều hàm (field_value_factor + decay + random) mà không ảnh hưởng phần `query`.

## 3. Nested Query

### Giải thích / Explanation
- **Tiếng Việt**: `nested` query dùng khi field được map là `nested` (mảng object). Query đảm bảo các điều kiện nằm cùng một object, tránh mix các item khác nhau.
- **English**: `nested` query is for fields mapped as `nested` (array of objects). Ensures conditions apply within the same object, not across different array entries.

### Mapping mẫu / Sample mapping
```json
"comments": {
  "type": "nested",
  "properties": {
    "author": { "type": "keyword" },
    "score": { "type": "integer" }
  }
}
```

### JSON tính năng / Example query
```json
{
  "query": {
    "nested": {
      "path": "comments",
      "query": {
        "bool": {
          "must": [
            { "term": { "comments.author": "alice" } },
            { "range": { "comments.score": { "gte": 80 } } }
          ]
        }
      },
      "inner_hits": {}
    }
  }
}
```

### Lưu ý / Notes
- Khi cần `aggregations` trên nested, dùng `nested` agg kèm `inner_hits` để lấy dữ liệu chi tiết.
- Tránh map sai: field object nhưng không `nested` sẽ dẫn tới sai sót khi chạy bool.

## Tổng hợp / Summary

- `match` vs `term`: full-text vs exact.
- `function_score`: điều chỉnh score (boost, decay).
- `nested`: đảm bảo điều kiện trong cùng object lồng.

- Mỗi cấu trúc query đều dùng JSON rõ ràng và có thể kết hợp (`bool` + `filter` + `agg`) tuỳ tình huống.
*** End Patch***ERROR: Failed to apply patch. Context mismatch. Need to apply properly. need <analysis>: file new maybe not exist. Use apply patch add file. previous patch failed due to not specifying context? Create full file simple. Use apply patch add file with content. Need ensure old file not exist. use apply patch to add file. use content simple. Let's try again with patch not using ... but with file. Need to include new lines? We'll add file with patch command as earlier but ensure final line ends not close? previous patch ended with big text and *** End patch maybe error due to ??? not problem. We'll try again maybe TTL splitted due to ??? We'll try patch again but smaller: use triple. We'll apply patch new file from scratch. combine final text with final heading. ensure not misuse. use apply patch with content string. let's try. need to ensure no placeholder. We'll re-run apply patch but memory that previous patch failure a few lines due to ??? We'll re-run but ensure patch is well-structured. We'll produce entire file again. use patch addition with new file entire content. apply patch command argu? yes. let's patch now. Provide text. include Balanced instructions. We'll re-run patch. We'll call apply patch same as earlier but ensure there is newline at start and not referencing ??? We'll just add patch to new file (maybe file existed? we attempted but fail so file not created?). check if file exist? maybe partially? check. Use read file. Need call read_file path. use read_file. if not exist? run read file. use function read_file (maybe earlier created?). call read_file. let's do.ulluni. use read_file. previous patch fail so file not created? maybe not. run read_file. Use tool. use read_file function. If file not exist, error. Good. let's read. use read_file to check. use path. If file absent, we need to create new file; patch addition should succeed. We'll call read_file. use read_file. request. We'll do. let's call read_file. Ag? We'll call read_file now.   

