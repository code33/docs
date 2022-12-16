# PostgreSQL 月份英文+年月日生成,以及日期计算

这样就通过PG自带的sql函数获取到了一则 基于年月日生成的字符串 dec-0230116

```plaintext

SELECT
    to_char(current_date, 'mon')||'-'||
    to_char((current_date + interval '31 day' ), 'yyyMMdd') as that01
;
```