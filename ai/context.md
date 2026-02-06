# 项目上下文

> 本文件让 AI 了解项目基本信息

---

## 项目信息

**项目名称**：[填写项目名称]
**项目描述**：[一句话描述项目]
**当前版本**：v1.0

---

## 技术栈

### 后端
- **框架**：SpringBoot 2.7.x / 3.x
- **ORM**：MyBatis-Plus 3.5+
- **数据库**：MySQL 8.0+
- **其他**：JWT, Lombok

### 前端
- **框架**：Vue 3
- **UI 库**：Element Plus
- **HTTP**：Axios
- **状态**：Pinia

---

## 项目结构

```
src/main/java/com/example/
├── controller/              # 控制器层
├── service/                 # 业务层
│   └── impl/
├── mapper/                  # 数据层
├── entity/                  # 实体类
├── dto/                     # 请求参数
├── vo/                      # 返回数据
└── common/                  # 公共模块
    ├── result/              # 统一响应
    ├── exception/           # 异常处理
    └── enums/               # 枚举
```

---

## 分层架构

```
Controller → Service → Mapper → Database
```

**禁止**：
- ❌ Controller 直接调用 Mapper
- ❌ Controller 中写业务逻辑
- ❌ 跨层调用

---

## 必须字段

每个表必须包含：
```sql
id BIGINT AUTO_INCREMENT PRIMARY KEY,
create_time DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
update_time DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE,
is_deleted TINYINT NOT NULL DEFAULT 0,
create_by BIGINT COMMENT '创建人ID',          -- 可选
update_by BIGINT COMMENT '更新人ID'          -- 可选
```

---

## 统一规范

### 响应格式
```java
Result.success(data)
Result.error(ErrorCode.USER_NOT_FOUND)
```

### 异常处理
```java
throw new BusinessException(ErrorCode.USER_NOT_FOUND);
```

---

## 数据库命名

- **表名**：`t_模块_功能`
- **字段名**：小写 + 下划线
- **索引**：`idx_字段名`
- **唯一键**：`uk_字段名`

---

## 当前功能

### 已完成
- [ ] [功能1]
- [ ] [功能2]

### 开发中
- [ ] [功能3]

### 计划中
- [ ] [功能4]

---

## 快速导航

- 需求文档：`requirements/`
- AI 配置：`.ai/config.md`
- 工作模式：`.ai/modes/`
- 日志：`logs/`
- 备份：`backups/`

---

**最后更新**：2026-02-06
