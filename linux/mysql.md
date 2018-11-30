# mysql小记

set names utf8;

INSERT INTO table (primarykey, field1, field2, ...)
SELECT key, value1, value2, ...
FROM dual
WHERE not exists (select * from table where primarykey = id);