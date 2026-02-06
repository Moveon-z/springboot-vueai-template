# 日志模板

> 复制此模板到 `logs/[日期]/[任务ID]/` 目录

---

## 文件1：changes.md（修改记录）

```markdown
# 修改记录

## 任务信息
- **任务ID**：task-[id]
- **需求文档**：requirements/inbox/[文件名]
- **工作模式**：自动/交互
- **开始时间**：2026-02-06 XX:XX
- **完成时间**：2026-02-06 XX:XX

## 文件修改清单

### 新增文件
1. `entity/User.java` - 用户实体类
2. `mapper/UserMapper.java` - 用户Mapper
3. `service/IUserService.java` - 用户Service接口
4. `service/impl/UserServiceImpl.java` - 用户Service实现
5. `controller/UserController.java` - 用户Controller
6. `dto/UserCreateDTO.java` - 创建用户DTO
7. `dto/UserUpdateDTO.java` - 更新用户DTO
8. `vo/UserVO.java` - 用户VO

### 修改文件
1. `pom.xml` - 添加 mybatis-plus 依赖
2. `application.yml` - 配置数据库连接

### 删除文件
无

## 具体修改内容

### entity/User.java
**修改内容**：
- 创建用户实体类
- 使用 @TableName("t_user")
- 使用 @TableId(type = IdType.AUTO)
- 使用 @TableLogic 逻辑删除

**代码片段**：
```java
@Data
@TableName("t_user")
public class User {
    @TableId(type = IdType.AUTO)
    private Long id;

    @TableField("username")
    private String username;

    @TableField("create_time")
    private LocalDateTime createTime;

    @TableLogic
    @TableField("is_deleted")
    private Integer isDeleted;
}
```

### mapper/UserMapper.java
**修改内容**：
- 继承 BaseMapper<User>
- 自动获得 CRUD 方法

**代码片段**：
```java
@Mapper
public interface UserMapper extends BaseMapper<User> {
}
```

### service/impl/UserServiceImpl.java
**修改内容**：
- 继承 ServiceImpl<UserMapper, User>
- 实现 IUserService
- 实现 createUser 方法

**关键代码**：
```java
@Service
@RequiredArgsConstructor
public class UserServiceImpl
    extends ServiceImpl<UserMapper, User>
    implements IUserService {

    @Override
    @Transactional(rollbackFor = Exception.class)
    public UserVO createUser(UserCreateDTO dto) {
        // 检查用户名
        if (lambdaQuery().eq(User::getUsername, dto.getUsername()).exists()) {
            throw new BusinessException(ErrorCode.USER_ALREADY_EXISTS);
        }

        // 构建实体
        User user = BeanUtil.copyProperties(dto, User.class);
        user.setPassword(BCrypt.hashpw(dto.getPassword()));

        // 保存
        save(user);

        return BeanUtil.copyProperties(user, UserVO.class);
    }
}
```

[继续列出其他文件的修改...]

## 数据库变更

### 新增表
- `t_user` - 用户表

### 表结构
```sql
CREATE TABLE t_user (
    id BIGINT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(50) NOT NULL,
    password VARCHAR(255) NOT NULL,
    create_time DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
    update_time DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE,
    is_deleted TINYINT NOT NULL DEFAULT 0,
    UNIQUE KEY uk_username (username)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
```

## 依赖变更
### 新增依赖
- mybatis-plus-boot-starter: 3.5.5
- lombok: 1.18.30
- jwt: 0.12.3

## 备份信息
- **数据库备份**：backups/database/pre-task-[id].sql
- **代码备份**：backups/code/pre-task-[id]/
```

---

## 文件2：summary.md（完成总结）

```markdown
# 完成总结

## 任务概述
实现了用户注册、登录、信息管理等功能

## 技术方案
- **认证方式**：JWT Token（7天有效期）
- **密码加密**：BCrypt（salt rounds = 12）
- **参数验证**：javax.validation
- **ORM**：MyBatis-Plus

## 实现功能
1. ✅ 用户注册
   - 用户名唯一性验证
   - 密码强度验证
   - BCrypt 加密

2. ✅ 用户登录
   - 用户名密码验证
   - JWT Token 生成

3. ✅ 用户管理
   - 创建用户
   - 更新用户
   - 删除用户（逻辑删除）
   - 查询用户列表（分页）

## 决策理由

### 为什么选择 JWT 而不是 Session？
- 无状态，适合前后端分离
- 不需要服务端存储，减轻服务器压力
- 移动端友好

### 为什么使用 MyBatis-Plus？
- 项目技术栈要求
- 大幅简化 CRUD 开发
- 自动生成 CRUD 方法
- Lambda 表达式查询

### 为什么不需要邮箱验证？
- MVP 阶段保持简单
- v2.0 可以加入

### 为什么 create_by 和 update_by 是可选的？
- 某些场景不需要审计（如系统自动创建的数据）
- 根据实际需求决定是否添加

## 测试结果

### 单元测试
- ✅ UserServiceTest: 100% 通过
- ✅ UserControllerTest: 100% 通过
- ✅ 测试覆盖率：85%

### 自动化测试
- ✅ 注册接口测试通过
- ✅ 登录接口测试通过
- ✅ CRUD 接口测试通过

### 安全检查
- ✅ 无 SQL 注入风险（使用 MyBatis-Plus）
- ✅ 密码已加密（BCrypt）
- ✅ 参数验证完整
- ⚠️ 建议添加 API 限流（v2.0）

## 遗留问题
无

## 性能指标
- API 响应时间：平均 50ms
- 数据库查询：平均 10ms
- 并发支持：500+ 用户

## 后续建议
1. 可以考虑添加 Redis 缓存用户信息
2. 可以考虑添加 Swagger API 文档
3. v2.0 添加邮箱验证功能
4. v2.0 添加 API 限流

## 参考资料
- 需求文档：requirements/inbox/user-module.docx
- 数据库设计：docs/design/database.md
- API 设计：docs/design/api.md
```

---

## 使用说明

### 创建日志目录
```bash
mkdir -p logs/2024-01-15/task-001
cd logs/2024-01-15/task-001
```

### 创建日志文件
1. 复制 `changes.md` 模板
2. 复制 `summary.md` 模板
3. 填写实际内容
