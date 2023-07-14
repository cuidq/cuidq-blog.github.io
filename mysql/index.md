# Mysql

**命令**


#### **SQL** 查询
1. 父查子  
   ```SQL
    SELECT u2.* FROM (
       SELECT
       @ids AS p_ids,
        (SELECT @ids := GROUP_CONCAT(id) FROM ec_app_category where status= 1 and is_del = 0
            and FIND_IN_SET(pid, @ids)) AS c_ids,
            @l := @l+1 AS LEVEL
         FROM ec_app_category, (SELECT @ids := 0, @l := 0 ) b #此处为需要传递的父类id.
         WHERE @ids IS NOT NULL
        ) u1
       JOIN ec_app_category u2
        ON FIND_IN_SET(u2.id, u1.p_ids) and u2.status= 1 and u2.is_del = 0
        AND u2.id != 0 and u2.pid = 0;
    ```
    {{< admonition type=tip title="This is a tip" open=false >}}
     需要包含自己, 则删掉 !=
    {{< /admonition >}}

1. 子查父
    ```SQL
     SELECT t2.id, t2.cate_name as name
        FROM
        (
        SELECT @r AS p_ids,
        (SELECT @r := pid FROM ec_activity_category WHERE id = p_ids) AS pid
        FROM
        (SELECT @r:= 22) vars, ec_activity_category AS h
        WHERE @r != 0
        ) t1
        JOIN ec_activity_category t2
        ON t1.p_ids = t2.id
        order by t2.id;
    ```

