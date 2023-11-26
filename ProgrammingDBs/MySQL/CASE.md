# CASE 表達式

```sql
SELECT 
    CASE 
        WHEN (SELECT birthdate FROM students WHERE name = 'John') LIKE '1990%' 
            THEN student_id 
        ELSE name 
    END 
FROM students;
```

蠻特別的方式

如果生日1990開頭則回傳 `student_id` 否則回傳 `name`
