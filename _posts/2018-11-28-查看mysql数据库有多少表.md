查看mysql数据库有多少表
--------------


SELECT COUNT(*) TABLES,  
table_schema FROM information_schema.TABLES 
WHERE table_schema = ‘tableName’ GROUP BY table_schema;

tableName 这个是你的数据库表名




