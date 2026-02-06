# AI 配置文件

> 本文件定义 AI 的核心行为规则

## 项目信息

**项目名称**：[填写项目名称]
**技术栈**：SpringBoot + MyBatis-Plus + MySQL + Vue 3
**包路径**：com.moveon.project

---

## 核心规则

### 写代码前必须读
1. `ai/context.md` - 项目上下文
2. `requirements/processing/` - 当前正在处理的需求
3. `backups/` - 修改前必须备份

### 工作模式

**自动模式**（ai/modes/auto.md）：
- AI 自主分析、开发、验证
- 减少交互，快速交付
- 适合：明确需求、重复性任务

**交互模式**（ai/modes/interactive.md）：
- AI 主动询问确认
- 明确需求、技术选型
- 适合：复杂需求、新技术

### 强制规则

#### 分层架构（严禁违反）
```
Controller → Service → Mapper → Database
```

#### 备份策略

**原则**：只备份受影响的数据，不产生无用的commit记录

**数据库备份**（只备份受影响的数据）：

**方式1：生成反向SQL**（推荐）
```sql
-- UPDATE 操作
-- 备份：生成如何撤销的SQL
UPDATE t_user SET
  username = '旧值',
  email = '旧值',
  status = 旧状态
WHERE id = 1;

-- DELETE 操作
-- 备份：生成如何恢复的SQL
INSERT INTO t_user (id, username, email, status, ...)
VALUES (1, '旧值1', '旧值2', 1, ...);

-- INSERT 操作
-- 备份：生成如何撤销的SQL
DELETE FROM t_user WHERE id = 1;
```

**方式2：只备份受影响的行**
```bash
# 只备份将要修改的行
mysqldump project \
  --where="id IN (1,2,3)" \
  --no-create-info \
  --no-create-db \
  t_user > backups/database/pre-task-001.sql
```

**方式3：记录数据快照**
```sql
-- 查询并保存受影响的数据到临时文件
SELECT * FROM t_user WHERE id IN (1,2,3)
INTO OUTFILE '/tmp/backup-001.csv'
FIELDS TERMINATED BY ','
ENCLOSED BY '"'
LINES TERMINATED BY '\n';
```

**代码备份**（二选一）：

**方式1：Git Stash**（推荐）
```bash
# 任务开始前
git stash push -m "before task-001"

# 不满意回滚
git stash pop

# 满意删除
git stash drop
```

**方式2：Patch**
```bash
# 任务开始前
git diff HEAD > backups/code/pre-task-001.diff

# 不满意回滚
git apply --reverse backups/code/pre-task-001.diff

# 满意删除
rm backups/code/pre-task-001.diff
```

**优势**：
- ✅ 不产生commit记录，Git历史保持干净
- ✅ 只备份受影响的数据行，不是全表
- ✅ 备份文件极小（可能只有几KB）
- ✅ 恢复精确，不影响其他数据
- ✅ 速度快

#### 日志记录
**必须记录**：
1. 修改了哪些文件（带路径）
2. 每个文件修改了什么内容
3. 最终总结（做了什么、为什么这么做）

---

## 代码规范

### Entity
```java
@Data
@TableName("t_xxx")
public class Xxx {
    @TableId(type = IdType.AUTO)
    private Long id;

    @TableField("create_time")
    private LocalDateTime createTime;

    @TableField("update_time")
    private LocalDateTime updateTime;

    @TableField("create_by")
    private Long createBy;  // 可选

    @TableField("update_by")
    private Long updateBy;  // 可选

    @TableLogic
    @TableField("is_deleted")
    private Integer isDeleted;
}
```

### 必须字段
```sql
id BIGINT AUTO_INCREMENT PRIMARY KEY,
create_time DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
update_time DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE,
is_deleted TINYINT NOT NULL DEFAULT 0,
create_by BIGINT COMMENT '创建人ID',          -- 可选
update_by BIGINT COMMENT '更新人ID'          -- 可选
```

### 统一响应
```java
Result.success(data)
Result.error(ErrorCode.XXX)
```

---

## 工作流程

### 标准流程
```
1. 读取需求（requirements/inbox/）
2. 分析需求 → 生成技术方案
3. 创建备份（backups/）
4. 开发实现
5. 自动测试
6. 生成日志（logs/）
7. 等待人工确认
```

### 失败回滚
```bash
# 回滚数据库（只恢复相关表）
mysql < backups/database/pre-task-001.sql

# 回滚代码
# 方式1：使用 Stash 恢复
git stash pop

# 方式2：使用 Patch 恢复
git apply --reverse backups/code/pre-task-001.diff
```

---

## 质量标准

### 每个 API 必须
- [ ] 参数验证（@Validated）
- [ ] 异常处理
- [ ] 事务控制（@Transactional）
- [ ] 单元测试
- [ ] 日志记录

### 安全检查
- [ ] SQL 注入防护
- [ ] XSS 防护
- [ ] 权限验证
- [ ] 密码加密（BCrypt）

---

**最后更新**：2024-XX-XX
