# 项目 AI 配置

> Claude Code 自动读取的配置文件

---

## AI 行为规则

**必须遵守的配置**：
- 阅读 `ai/config.md` - AI 核心规则
- 阅读 `ai/context.md` - 项目上下文
- 使用 `ai/prompts/` 中的提示词模板

---

## 写代码前必须读

1. **ai/context.md** - 项目上下文
   - 项目信息、技术栈、项目结构、当前功能

2. **ai/config.md** - AI 配置和规则
   - 分层架构规则
   - 代码规范
   - 备份策略
   - 质量标准

---

## 快速开始

### 自动模式（快速开发）
```
1. 放入需求文档到 requirements/inbox/
2. 复制 ai/prompts/auto.md 的内容给 AI
3. AI 自动分析、开发、测试
4. 查看 logs/ 中的结果
5. 确认或回滚
```

### 交互模式（可控开发）
```
1. 放入需求文档到 requirements/inbox/
2. 复制 ai/prompts/interactive.md 的内容给 AI
3. AI 分步确认，逐步开发
4. 查看日志，确认最终结果
5. 通过或回滚
```

---

## 核心规则

### 分层架构（严禁违反）
```
Controller → Service → Mapper → Database
```

**禁止**：
- ❌ Controller 直接调用 Mapper
- ❌ Controller 中写业务逻辑
- ❌ 跨层调用

### 备份策略（严格执行）
- **数据库**：只备份受影响的数据（生成反向SQL）
- **代码**：使用 Git Stash 或 Patch
- **日志**：记录所有修改和最终总结

### 代码规范
- 必须字段：id, create_time, update_time, is_deleted
- 可选字段：create_by, update_by
- 统一响应：Result.success(data)
- 异常处理：BusinessException

---

## 质量检查

每个功能完成后必须：
- [ ] 符合分层架构
- [ ] 参数验证完整
- [ ] 异常处理完整
- [ ] 单元测试通过
- [ ] 更新日志（logs/）

---

## 快速导航

- **配置规则**：ai/config.md
- **项目上下文**：ai/context.md
- **自动模式**：ai/prompts/auto.md
- **交互模式**：ai/prompts/interactive.md
- **使用指南**：使用指南.md

---

## 注意事项

- ❌ 不要跳过阅读 ai/config.md 和 ai/context.md
- ❌ 不要违反分层架构
- ❌ 不要省略备份步骤
- ✅ 每个任务完成后查看日志
- ✅ 满意后再移动需求到 requirements/completed/

---

**最后更新**：2024-XX-XX
