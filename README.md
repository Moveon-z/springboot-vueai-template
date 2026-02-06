# SpringBoot + Vue 项目 AI 自动化开发框架

> 让 AI 自动分析需求、开发代码、自动测试

---

## 核心理念

**放入需求 → AI 自动开发 → 最终确认 → 通过或回滚**

---

## 目录结构

```
project/
├── ai/                           # AI 规范目录
│   ├── config.md                  # AI 核心配置
│   ├── context.md                 # 项目上下文
│   ├── modes/                     # 工作模式
│   │   ├── auto.md               # 自动模式
│   │   └── interactive.md         # 交互模式
│   └── prompts/                   # 提示词
│       ├── auto.md               # 自动开发提示词
│       └── interactive.md         # 交互开发提示词
│
├── requirements/                 # 需求目录
│   ├── inbox/                     # 放入需求（Word/Excel）
│   ├── analyzing/                # 分析中
│   ├── developing/               # 开发中
│   └── completed/                # 已完成
│
├── logs/                         # AI 行为日志
│   └── 2024-01-15/
│       └── task-001/
│           ├── changes.md        # 修改了什么
│           └── summary.md        # 最终总结
│
├── tests/                        # 测试目录
│   └── [task-id]/
│       ├── test-plan.md          # 测试计划
│       └── results.md            # 测试结果
│
└── backups/                      # 备份目录
    ├── database/                 # 数据库备份
    │   └── pre-task-001.sql
    └── code/                     # 代码快照
        └── pre-task-001/
```

---

## 快速开始

### 方式1：自动模式（快速）

```bash
# 1. 将需求文档放入 requirements/inbox/
cp ~/Downloads/需求.docx requirements/inbox/

# 2. 复制提示词给 AI
复制 ai/prompts/auto.md 的内容

# 3. AI 自动执行
- 分析需求
- 设计方案
- 创建备份
- 开发代码
- 自动测试
- 生成日志

# 4. 查看结果
cat logs/2024-01-15/task-001/summary.md

# 5. 确认
# 通过 → mv requirements/inbox/需求.docx requirements/completed/
# 回滚 → 恢复备份（见下方回滚命令）
```

### 方式2：交互模式（可控）

```bash
# 1. 将需求文档放入 requirements/inbox/
cp ~/Downloads/需求.docx requirements/inbox/

# 2. 复制提示词给 AI
复制 ai/prompts/interactive.md 的内容

# 3. AI 分步执行
- 提出疑问 → 你回答
- 设计方案 → 你选择
- 实施计划 → 你确认
- 开发代码 → 你确认每步
- 最终结果 → 你确认

# 4. 查看结果
cat logs/2024-01-15/task-001/summary.md
```

---

## 回滚方法

### 如果不满意 AI 的修改

```bash
# 1. 回滚数据库
mysql < backups/database/pre-task-001.sql

# 2. 回滚代码
rm -rf src/
cp -r backups/code/pre-task-001/src/ ./

# 3. 重启服务
./restart.sh

# 或使用 Maven
mvn clean install
mvn spring-boot:run
```

---

## 工作模式对比

| 特性 | 自动模式 | 交互模式 |
|-----|---------|---------|
| 速度 | 快 ⚡ | 较慢 |
| 交互 | 少 | 多 |
| 控制 | AI 自主决策 | 人工确认 |
| 适用场景 | 明确需求、重复任务 | 复杂需求、新技术 |

---

## 核心特性

### 1. 自动备份
每次开发前自动创建：
- 数据库快照（`.sql`）
- 代码快照（完整源码）

### 2. 行为日志
记录 AI 的所有行为：
- 修改了哪些文件
- 每个文件做了什么修改
- 最终总结和决策理由

### 3. 质量保证
自动检查：
- 分层架构是否正确
- SQL 注入防护
- 参数验证
- 异常处理
- 单元测试

### 4. 双模式切换
- **自动模式**：快速交付
- **交互模式**：可控开发

---

## 技术栈

### 后端
- SpringBoot + MyBatis-Plus + MySQL

### 前端
- Vue 3 + Element Plus

### 代码规范
- 分层架构：Controller → Service → Mapper
- 必须字段：id, create_time, update_time, is_deleted
- 可选字段：create_by, update_by

---

## 文件说明

### ai/config.md
AI 的核心行为规则

### ai/context.md
项目上下文（让 AI 了解项目）

### ai/modes/
- `auto.md` - 自动模式说明
- `interactive.md` - 交互模式说明

### ai/prompts/
- `auto.md` - 自动模式提示词
- `interactive.md` - 交互模式提示词

### requirements/
需求文档流转目录

### logs/
AI 行为日志

### backups/
数据库和代码备份

---

## 常见问题

### Q: 支持哪些需求文档格式？
A: Word (.docx) 和 Excel (.xlsx)，AI 可以直接读取

### Q: 如何选择工作模式？
A:
- 简单明确的需求 → 自动模式
- 复杂或不清楚的需求 → 交互模式

### Q: 备份会占用很多空间吗？
A: 可以定期清理旧备份，只保留最近 N 个

### Q: 如何查看 AI 的行为日志？
A: 查看 `logs/[日期]/[任务ID]/` 目录

---

## 最佳实践

1. **第一次使用**：使用交互模式，让 AI 充分了解项目
2. **简单功能**：使用自动模式，快速交付
3. **复杂功能**：使用交互模式，逐步确认
4. **每次任务**：查看日志，了解 AI 做了什么
5. **定期清理**：删除旧的备份和日志

---

## 进阶技巧

### 1. 自定义提示词
编辑 `.ai/prompts/` 中的文件，添加你的特殊要求

### 2. 添加代码检查
在 `.ai/config.md` 中添加代码检查规则

### 3. 多人协作
将 `requirements/` 设置为共享目录

### 4. 持续集成
将自动模式集成到 CI/CD 流程

---

**最后更新**：2024-XX-XX
**当前版本**：v1.0
