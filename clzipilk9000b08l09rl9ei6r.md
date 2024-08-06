---
title: "Postgresql 各表大小size 排序列出"
seoTitle: "postgres pgsql table size"
seoDescription: "postgres pgsql table size query"
datePublished: Tue Aug 06 2024 17:41:17 GMT+0000 (Coordinated Universal Time)
cuid: clzipilk9000b08l09rl9ei6r
slug: postgresql-size
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1722965980780/923b2f35-addd-4916-b036-9ce69c8d4305.png
tags: postgresql, table-size

---

就用这段sql即可,可以直接在内嵌查询的 table\_name 做筛选操作

```sql
SELECT
   table_name,
   pg_size_pretty(table_size) AS table_size,
   pg_size_pretty(indexes_size) AS indexes_size,
   pg_size_pretty(total_size) AS total_size
FROM (
        SELECT
           table_name,
           pg_table_size(table_name) AS table_size,
           pg_indexes_size(table_name) AS indexes_size,
           pg_total_relation_size(table_name) AS total_size
        FROM (
                SELECT ('' || table_schema || '.' || table_name || '') AS table_name
                FROM information_schema.tables
             ) AS all_tables
        where table_name like 'public.%'
        ORDER BY total_size DESC
     ) AS pretty_sizes
```