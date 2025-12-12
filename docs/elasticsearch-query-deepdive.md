---
title: Elasticsearch Query Deep Dive
summary: Detailed breakdown (Vietnamese + English) of bool, match vs term, function_score, and nested queries with JSON and usage guidance.
---

<!-- markdownlint-disable MD022 MD024 MD025 MD031 MD032 MD033 MD037 MD009 MD012 -->

# Elasticsearch Query Deep Dive (Match/Term, Function Score, Nested)

## 0. Bool + `must`/`filter`/`should`/`must_not`

### Giải thích / Explanation
- **Tiếng Việt**: `bool` là khung kết hợp nhiều điều kiện. `must` bắt buộc và ảnh hưởng điểm; `filter` lọc nhưng không ảnh hưởng score; `should` là điều kiện phụ trợ để tăng score; `must_not` loại trừ tài liệu không mong muốn.
- **English**: `bool` combines multiple clauses. `must` is required and influences scoring, `filter` restricts without scoring, `should` boosts relevance when matched, and `must_not` excludes documents.

### JSON structure / Example
```json
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "description": "monitoring"
          }
        }
      ],
      "filter": [
        {
          "term": {
            "status.keyword": "active"
          }
        }
      ],
      "should": [
        {
          "match": {
            "tags": "performance"
          }
        }
      ],
      "must_not": [
        {
          "term": {
            "category.keyword": "deprecated"
          }
        }
      ]
    }
  }
}
```

### Khi nào dùng / When to use
- Dùng `bool` để mô tả nhiều điều kiện với vai trò khác nhau (lọc, boost, exclude).
- `filter` phù hợp cho range/term vì có thể cache tốt hơn.
- `should` có thể yêu cầu `minimum_should_match` nếu bạn cần ít nhất một clause phụ trợ.

## 1. Match vs Term / `match` vs `term`

### Giải thích / Explanation
- **Tiếng Việt**: `match` là full-text (phân tích từ), `term` là so khớp chính xác (không phân tích).
- **English**: `match` performs analyzed searches, while `term` expects exact values.

### JSON
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
- `match` cho text, `term` cho keyword/ID/enum hoặc số.
- `term` hữu dụng khi bạn cần đảm bảo không bị ảnh hưởng bởi case/tokenization.

## 2. Function Score

### Giải thích / Explanation
- **Tiếng Việt**: `function_score` kết hợp query với các hàm để boost score theo field, khoảng cách thời gian, hoặc random.
- **English**: `function_score` wraps a base query and applies functions (field_value_factor, decay).

### JSON ví dụ
```json
{
  "query": {
    "function_score": {
      "query": {
        "match": {
          "title": "monitoring"
        }
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
- Ưu tiên tài liệu mới hơn, có độ phổ biến cao, hoặc cần xếp hạng đặc biệt.
- Dễ kết hợp nhiều hàm mà không phải thay đổi phần `query`.

## 3. Nested Query

### Giải thích / Explanation
- **Tiếng Việt**: Dùng `nested` khi field là array of objects; các điều kiện phải nằm trong cùng một object.
- **English**: Ensures conditions stay within the same nested object entry.

### Sample mapping
```json
"comments": {
  "type": "nested",
  "properties": {
    "author": { "type": "keyword" },
    "score": { "type": "integer" }
  }
}
```

### JSON example
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

### Notes
- Dùng `nested` agg + `inner_hits` khi cần phân tích từng entry.
- Đảm bảo field đã map `nested` để tránh kết quả sai lệch.

## Tổng hợp / Summary

- `match` vs `term`: full-text vs exact.
- `function_score`: điều chỉnh score (boost, decay).
- `nested`: đảm bảo điều kiện trong cùng object lồng.
- Có thể kết hợp bool/filter/agg tùy tình huống.

