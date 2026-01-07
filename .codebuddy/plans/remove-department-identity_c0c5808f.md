---
name: remove-department-identity
overview: 从项目中移除"部门"(department)和"身份"(identity)字段，包括类型定义、默认数据、表格列配置、表单输入、国际化文本、界面展示等相关内容。
todos:
  - id: search-references
    content: 使用 [subagent:code-explorer] 搜索项目中所有 department 和 identity 相关引用
    status: completed
  - id: remove-type-definitions
    content: 移除类型定义文件中的 department 和 identity 字段
    status: completed
    dependencies:
      - search-references
  - id: clean-default-data
    content: 清理默认数据和模拟数据中的相关字段值
    status: completed
    dependencies:
      - remove-type-definitions
  - id: remove-table-columns
    content: 删除表格列配置中的部门和身份列定义
    status: completed
    dependencies:
      - remove-type-definitions
  - id: remove-form-fields
    content: 移除表单组件中的部门和身份输入项
    status: completed
    dependencies:
      - remove-type-definitions
  - id: clean-i18n
    content: 删除国际化文件中的相关翻译文本
    status: completed
    dependencies:
      - search-references
  - id: clean-import-logic
    content: 清理导入功能中与这两个字段相关的处理逻辑
    status: completed
    dependencies:
      - remove-type-definitions
---

## Product Overview

从现有项目中完整移除"部门"(department)和"身份"(identity)两个字段，确保代码库中不再包含这两个字段的任何引用和相关功能。

## Core Features

- 移除类型定义中的 department 和 identity 字段
- 清理默认数据和模拟数据中的相关字段
- 删除表格列配置中的部门和身份列
- 移除表单中的部门和身份输入项
- 清理国际化文件中的相关翻译文本
- 移除导入功能中与这两个字段相关的处理逻辑

## 技术方案

### 修改范围分析

基于现有项目结构，需要修改以下几类文件：

1. **类型定义文件** - 移除接口/类型中的字段定义
2. **数据文件** - 清理默认数据和模拟数据
3. **表格配置** - 删除列定义
4. **表单组件** - 移除输入控件
5. **国际化文件** - 删除翻译键值
6. **导入逻辑** - 清理字段映射和处理

### 实现策略

#### 安全删除原则

- 先搜索所有引用点，确保不遗漏
- 按依赖顺序删除：先删除使用方，再删除定义方
- 保持代码格式一致性

### 核心修改文件清单

```
project-root/
├── src/
│   ├── types/
│   │   └── *.ts              # 移除 department/identity 字段定义
│   ├── data/
│   │   └── *.ts              # 清理默认数据
│   ├── components/
│   │   ├── table/
│   │   │   └── columns.ts    # 删除列配置
│   │   └── form/
│   │       └── *.tsx         # 移除表单字段
│   ├── locales/
│   │   ├── zh-CN.json        # 删除中文翻译
│   │   └── en-US.json        # 删除英文翻译
│   └── utils/
│       └── import.ts         # 清理导入逻辑
```

### 搜索关键词

需要在代码库中搜索以下关键词确保完整清理：

- `department`
- `identity`
- `部门`
- `身份`

## Agent Extensions

### SubAgent

- **code-explorer**
- Purpose: 全面搜索项目中所有包含 department 和 identity 字段的文件
- Expected outcome: 获取完整的文件列表和具体位置，确保不遗漏任何引用点