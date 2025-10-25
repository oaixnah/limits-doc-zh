---
hide:
  - navigation
description: 学习如何使用parse()和parse_many()函数解析速率限制字符串，包括标准格式如"10 per hour"、"100/day"等，支持多种时间单位和组合方式。
---

# 语法格式

## 速率限制字符串表示法

与其手动构造 `RateLimitItem` 的实例，您可以使用以下 `解析函数`。

- `parse()`
- `parse_many()`

这些函数接受遵循以下格式的字符串指定的速率限制：

```
[count] [per|/] [n (optional)] [second|minute|hour|day|month|year]
```

您可以通过使用您选择的分隔符来组合速率限制。

## 示例

* `10 per hour`
* `10/hour`
* `10/hour;100/day;2000 per year`
* `100/day, 500/7days`
* 