#### Mysql生成数据库详细设计说明书的表格语句

```sql
SELECT
	y.ORDINAL_POSITION AS 序号,
	y.COLUMN_NAME AS 字段名称,
	y.COLUMN_COMMENT AS 字段描述,
	y.DATA_TYPE AS 字段类型,
	y.CHARACTER_MAXIMUM_LENGTH AS 字段长度,
	y.IS_NULLABLE AS 允许空,
	y.COLUMN_DEFAULT AS 默认值
FROM
	information_schema. COLUMNS y
WHERE
	table_schema = 'resource_portal'
AND table_name = 'rpt_user';
```

