# Postgresql 批量更新,按照输入的值一次性操作

应用的场景:

比如当前你有一组dataTable数据呈现在前端,需要对某几行的排序进行调整,那么就有两种方案:

1. 对每一个sort排序值进行一次api提交,每改一次则提交一次
    
2. 拖拽好你期望的dataTable row的排序,改好了以后,点击"确认"一次性提交多行;
    

其中方案2,在pg中的实现方案有个操作就甚是简单

```plaintext

WITH sort_settings_table(crs_sn,crs_sort) AS (
                VALUES ('SN01',100),('SN02',110),('SN03',120)
             )
             UPDATE course_t AS ct
             SET sort = sst.crs_sort
             FROM sort_settings_table AS sst
             WHERE ct.crs_sn = sst.crs_sn
             RETURNING ct.crs_sn;   
```

如此这般操作 即可一次性更新多则数据,以List 方式进行提交;

而且这样的Sql更优雅美观;