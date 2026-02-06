# 自动模式

> AI 自主分析、开发、验证，减少交互

---

## 模式特点

⚡ **快速交付**：减少询问，快速完成
🤖 **自主决策**：AI 自主选择技术方案
✅ **自动测试**：AI 自动编写并运行测试
📋 **最终确认**：完成后等待人工确认

---

## 工作流程

```
1. 读取需求文档
   ↓
2. 分析需求，自主设计方案
   ↓
3. 创建备份（backups/）
   ↓
4. 开发实现
   ↓
5. 自动测试
   ↓
6. 生成日志（logs/）
   ↓
7. 展示结果，等待确认
   ↓
8. 通过或回滚
```

---

## 提示词模板

### 完整流程提示词

```
## 任务：自动开发

请阅读 requirements/inbox/ 中的需求文档，然后：

## 要求

### 1. 分析需求
- 理解业务目标
- 识别数据实体
- 明确验收标准

### 2. 设计方案
- 自主选择最合适的技术方案
- 数据库设计（表结构、字段、索引）
- API 设计（接口、参数、响应）

### 3. 开发前准备
- 创建数据库备份：backups/database/pre-[timestamp].sql
- 创建代码备份：backups/code/pre-[timestamp]/
- 创建日志目录：logs/[date]/[task-id]/

### 4. 开发实现
- Entity → Mapper → Service → Controller
- DTO 和 VO
- 参数验证
- 异常处理
- 事务控制

### 5. 自动测试
- 编写单元测试
- 运行测试
- 记录测试结果

### 6. 生成日志
在 logs/[date]/[task-id]/ 下创建：
- `changes.md` - 修改了哪些文件
- `summary.md` - 最终总结

### 7. 展示结果
等待我的最终确认：
- 通过 → 移动需求到 requirements/completed/
- 回滚 → 恢复备份，重新开发

## 重要

- 遇到不确定的地方，使用最常用的方案
- 不需要中途向我确认
- 但在最终总结中要说明你的决策理由
- 如果遇到无法解决的问题，记录在 summary.md 中

请开始执行。
```

---

## 日志模板

### changes.md

```markdown
# 修改记录

## 任务信息
- 任务ID：task-001
- 需求文档：requirements/inbox/user-module.docx
- 开始时间：2024-01-15 10:00
- 完成时间：2024-01-15 11:30

## 文件修改清单

### 新增文件
- `entity/User.java`
- `mapper/UserMapper.java`
- `service/IUserService.java`
- `service/impl/UserServiceImpl.java`
- `controller/UserController.java`
- `dto/UserCreateDTO.java`
- `dto/UserUpdateDTO.java`
- `vo/UserVO.java`

### 修改文件
- `pom.xml` - 添加依赖

### 删除文件
- 无

## 具体修改内容

### entity/User.java
- 创建用户实体类
- 使用 @TableName("t_user")
- 包含必须字段：id, create_time, update_time, is_deleted
- 包含可选字段：create_by, update_by

### mapper/UserMapper.java
- 继承 BaseMapper<User>
- 提供自动 CRUD

[继续列出其他文件的修改...]

## 备份信息
- 数据库备份：backups/database/pre-task-001.sql
- 代码备份：backups/code/pre-task-001/
```

### summary.md

```markdown
# 完成总结

## 任务概述
实现了用户注册、登录、CRUD 等基础功能

## 技术方案
- **认证方式**：JWT Token（7天有效期）
- **密码加密**：BCrypt
- **参数验证**：javax.validation
- **异常处理**：统一异常处理

## 决策理由
1. **为什么选择 JWT 而不是 Session**？
   - 无状态，适合前后端分离
   - 不需要服务端存储，减轻服务器压力

2. **为什么不需要邮箱验证**？
   - MVP 阶段保持简单
   - v2.0 可以加入

3. **为什么使用 MyBatis-Plus**？
   - 项目技术栈要求
   - 大幅简化 CRUD 开发

## 测试结果
- ✅ 单元测试通过率：100%
- ✅ 接口测试通过
- ✅ 无 SQL 注入风险
- ✅ 无 XSS 风险

## 遗留问题
无

## 建议
- 可以考虑添加 Redis 缓存用户信息
- 可以考虑添加 Swagger API 文档
- v2.0 添加邮箱验证功能
```

---

## 快速回滚

如果不满意，执行：

```bash
# 回滚数据库
mysql < backups/database/pre-task-001.sql

# 回滚代码
rm -rf src/
cp -r backups/code/pre-task-001/src/ ./

# 重启服务
./restart.sh
```

---

**最后更新**：2026-02-06
