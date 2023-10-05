---
title: "PostgreSQL通过表备注获取表信息,list table info by description"
datePublished: Thu Oct 05 2023 05:02:53 GMT+0000 (Coordinated Universal Time)
cuid: clncpon1w000d09jj9e4a2bu8
slug: postgresqllist-table-info-by-description
tags: postgresql, maintain

---

```sql
SELECT * FROM pg_catalog.pg_statio_all_tables AS st
         where relid in (
            select desct.objoid from pg_catalog.pg_description as desct where description ~ ('NF|RC|DT') and objsubid = 0
);
    
```