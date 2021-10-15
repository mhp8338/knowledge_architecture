# MySQL管理事务处理

本文主要介绍如何利用`COMMIT`和`ROLLBACK`语句管理事务处理

# 事务处理

### 事务处理是什么？

- 维护数据库的完整性
- 成批处理SQL语句，要么全部执行，要么都不执行

### 事务处理有什么作用？

利用事务处理可以保证一组操作不会中途停止，它们或者作为整体执行，或者干脆都不执行。

如果发生错误，则回退至已知的安全状态

### 几个重要术语

| 术语                 |                                                  |
| :------------------- | :----------------------------------------------: |
| 事务（transaction）  |                  指一组sql语句                   |
| 回退（rollback）     |               指撤销sql语句的过程                |
| 提交（commit）       |          指将未存储的数据结果写入sql表           |
| 保留点（save point） | 事务处理过程中设置的临时占位符，可以对其发布回退 |

# 控制事务处理

- 使用rollback回退事务

```mysql
# 事务开始
START TRANSACTION;
DELETE FROM ordertotals;
SELECT * FROM ordertotals;
ROLLBACK;

SELECT * FROM ordertotals;
```

`rollback`只能在事务中使用，`start transaction`

- 使用commit提交事务

```mysql
# 26.2.2 使用COMMIT
START TRANSACTION;
DELETE FROM orderitems WHERE order_num=20010;
DELETE FROM orders WHERE order_num=20010;
COMMIT ;
```

- 使用保留点savepoint

```mysql
SAVEPOINT delete1;
-- 回退
ROLLBACK delete1;
```

- 更改默认提交行为

默认的mysql行为是自动提交更改的，所做更改立即生效，但可以设置不自动提交更改

```mysql
SET autocommit=0;
```

直到`autocommit`为1时，才可提交更改