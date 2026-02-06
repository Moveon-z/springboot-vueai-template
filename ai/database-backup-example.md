# 数据库备份示例

> AI 如何只备份受影响的数据

---

## 场景：更新用户信息

### 步骤1：AI 查询受影响的数据

```java
// AI 在更新前先查询
User user = userMapper.selectById(1L);

// 生成反向SQL
String rollbackSQL = String.format(
    "UPDATE t_user SET " +
    "username = '%s', " +
    "email = '%s', " +
    "status = %d " +
    "WHERE id = %d;",
    user.getUsername(),
    user.getEmail(),
    user.getStatus(),
    user.getId()
);

// 保存到备份文件
saveToFile("backups/database/pre-task-001.sql", rollbackSQL);
```

**生成的备份SQL**：
```sql
-- 备份：如何撤销本次操作
UPDATE t_user SET
  username = 'oldname',
  email = 'old@example.com',
  status = 1
WHERE id = 1;
```

---

## 场景：删除用户

### 步骤1：AI 查询要删除的数据

```java
// AI 在删除前先查询
List<User> users = userMapper.selectBatchIds(Arrays.asList(1L, 2L, 3L));

// 生成反向SQL（恢复数据）
StringBuilder rollbackSQL = new StringBuilder();
for (User user : users) {
    rollbackSQL.append(String.format(
        "INSERT INTO t_user (id, username, email, password, status, create_time, update_time, is_deleted) " +
        "VALUES (%d, '%s', '%s', '%s', %d, '%s', '%s', %d);\n",
        user.getId(),
        user.getUsername(),
        user.getEmail(),
        user.getPassword(),
        user.getStatus(),
        user.getCreateTime(),
        user.getUpdateTime(),
        user.getIsDeleted()
    ));
}

// 保存到备份文件
saveToFile("backups/database/pre-task-001.sql", rollbackSQL.toString());
```

**生成的备份SQL**：
```sql
-- 备份：如何撤销本次操作
INSERT INTO t_user (id, username, email, password, status, create_time, update_time, is_deleted)
VALUES (1, 'user1', 'user1@example.com', '$2a$...', 1, '2024-01-15 10:00:00', '2024-01-15 10:00:00', 0);
INSERT INTO t_user (id, username, email, password, status, create_time, update_time, is_deleted)
VALUES (2, 'user2', 'user2@example.com', '$2a$...', 1, '2024-01-15 10:00:00', '2024-01-15 10:00:00', 0);
INSERT INTO t_user (id, username, email, password, status, create_time, update_time, is_deleted)
VALUES (3, 'user3', 'user3@example.com', '$2a$...', 1, '2024-01-15 10:00:00', '2024-01-15 10:00:00', 0);
```

---

## 场景：批量更新

### 步骤1：AI 查询受影响的数据

```java
// AI 在更新前先查询
LambdaQueryWrapper<User> wrapper = new LambdaQueryWrapper<>();
wrapper.eq(User::getStatus, 0);  // 只查状态为0的用户
List<User> users = userMapper.selectList(wrapper);

// 生成反向SQL
StringBuilder rollbackSQL = new StringBuilder();
for (User user : users) {
    rollbackSQL.append(String.format(
        "UPDATE t_user SET status = 0 WHERE id = %d;\n",
        user.getId()
    ));
}

// 保存到备份文件
saveToFile("backups/database/pre-task-001.sql", rollbackSQL.toString());
```

**生成的备份SQL**：
```sql
-- 备份：如何撤销本次操作
UPDATE t_user SET status = 0 WHERE id = 1;
UPDATE t_user SET status = 0 WHERE id = 5;
UPDATE t_user SET status = 0 WHERE id = 8;
```

---

## AI 实现模板

```java
@Component
public class BackupService {

    @Autowired
    private UserMapper userMapper;

    /**
     * 生成反向SQL
     */
    public String createRollbackSQL(String operation, Object data) {
        StringBuilder sql = new StringBuilder();

        sql.append("-- 备份：如何撤销本次操作\n");

        if ("UPDATE".equals(operation)) {
            User user = (User) data;
            sql.append(String.format(
                "UPDATE t_user SET " +
                "username = '%s', " +
                "email = '%s', " +
                "status = %d " +
                "WHERE id = %d;",
                user.getOldUsername(),  // AI 需要在查询时保存旧值
                user.getOldEmail(),
                user.getOldStatus(),
                user.getId()
            ));
        }

        else if ("DELETE".equals(operation)) {
            List<User> users = (List<User>) data;
            for (User user : users) {
                sql.append(String.format(
                    "INSERT INTO t_user (id, username, email, ...) " +
                    "VALUES (%d, '%s', '%s', ...);\n",
                    user.getId(),
                    user.getUsername(),
                    user.getEmail()
                ));
            }
        }

        return sql.toString();
    }
}
```

---

## 提示词示例

告诉 AI 如何备份：

```
## 开发用户更新功能

请按照以下步骤开发：

### 步骤1：查询受影响的数据
在更新前先查询用户数据，保存旧值

### 步骤2：生成反向SQL
将以下内容保存到 backups/database/pre-task-001.sql：
```sql
-- 备份：如何撤销本次操作
UPDATE t_user SET
  username = '旧值',
  email = '旧值',
  status = 旧状态
WHERE id = 1;
```

### 步骤3：执行更新
执行实际的更新操作

### 步骤4：记录到日志
在 logs/[date]/[task-id]/changes.md 中记录
```

---

## 优势总结

| 方式 | 备份内容 | 备份大小 | 恢复精度 |
|-----|---------|---------|---------|
| **全表备份** | 整个表 | 10-100MB | 低（影响所有数据） |
| **只备份行** | 受影响的行 | 1-10KB | 高（只影响相关数据） |
| **反向SQL** | 只记录旧值 | 几百字节 | 最高（精确到字段） |

---

## 推荐方案

**日常使用**：**反向SQL**（最精确）

**批量操作**：**只备份受影响的行**（平衡）

**极端情况**：**mysqldump where子句**（最保险）
