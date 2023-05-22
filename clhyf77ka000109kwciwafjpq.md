---
title: "postgresql日期计算 date calculation"
datePublished: Mon May 22 2023 05:42:06 GMT+0000 (Coordinated Universal Time)
cuid: clhyf77ka000109kwciwafjpq
slug: postgresql-date-calculation
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1684734407342/05daf4ef-1d01-4a8c-96e1-54675254de4c.png
tags: postgresql, date-calculation

---

在写pg func sql函数的时候,避免不了日期/时间的计算;

这里就有一个刚刚写好的sql 脚本给演示or讲解下

```sql
select (to_date((aa||''),'YYYYMMDD')+100) into var_thatExpDate from ( select (unnest(ARRAY[(to_char(now()::date,'YYYYMMDD'))::integer,(to_char(var_ulepExpDate,'YYYYMMDD'))::integer])) aa
                    ) bb order by aa desc limit 1 ;    
```

来简单地讲解一下这个脚本的组成部分:

从最内层说起

```sql
ARRAY[(to_char(now()::date,'YYYYMMDD'))::integer,(to_char(var_ulepExpDate,'YYYYMMDD'))::integer]
```

其中 to\_char(now()::date,'YYYYMMDD')::integer

即获取今天的时间,转换成日期后,将其格式化为 YYYYMMDD的字符串,并转化为整型数值;

其中 (to\_char(var\_ulepExpDate,'YYYYMMDD'))::integer 是将指定变量转化成日期格式的字符串并转换成整型数值

最后 外层用ARRAY\[ \] 包裹起来,就形成了一个pg 数组,再套一个 unnest() 函数,即实现了数组到列的转化;

转化成了列型,即可实现max min order by asc desc 等sql的常规排序操作了

### 再看外层

```sql
select (to_date((aa||''),'YYYYMMDD')+100)
```

这里是将取到的那个值aa 原本 是integer型的转为字符串型,再从字符串型转为日期数值型即中式的年月日,后面的+100 就是天数了哒

### 最后总结

整个函数的意思就是对会员的续费操作,即: 今天日期,和 数据库内存在的到期日,两个日期进行比较,选取更晚的那个日期,用来追加天数,赋值给指定变量