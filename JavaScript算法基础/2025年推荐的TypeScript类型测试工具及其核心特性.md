# 2025 年推荐的 TypeScript 类型测试工具及其核心特性

## 一、专业类型测试工具

### tsd

- **功能**: 静态测试 `.d.ts` 类型定义文件，支持 `.test-d.ts` 测试文件编写。
- **断言方法**: 提供 `expectType<T>` / `expectError<T>` 等断言方法。
- **项目地址**: [tsd 项目地址](https://gitcode.com/gh_mirrors/ts/tsd)

### TSTyche

- **功能**: 轻量级类型测试框架，支持 `describe()` / `test()` 测试组织。
- **断言方法**: 提供 `.toBe()` / `.toBeAssignableTo()` 等类型断言。
- **项目地址**: [TSTyche 项目地址](https://gitcode.com/gh_mirrors/ts/tstyche)

### Typroof

- **功能**: 结合代码分析与可视化测试，类 Jest/Vitest 的测试报告输出。
- **特点**: 支持工具类型即时验证。

## 二、扩展测试方案

### 单元测试集成

- 使用 `ts-mocha` 在 Mocha 中测试全局类型。
- 通过 `Jest` + `@types/jest` 验证运行时类型。

### 静态分析工具

- 使用 `TSLint` 检查类型定义规范。
- 配合 `ESLint` 和 `@typescript-eslint` 插件进行静态分析。

## 三、选型建议

| 需求场景         | 推荐工具 | 优势                      |
| ---------------- | -------- | ------------------------- |
| 类型定义文件测试 | tsd      | 专注 `.d.ts` 静态验证     |
| 复杂工具类型测试 | TSTyche  | 丰富的断言 API            |
| 开发体验优先     | Typroof  | 可视化反馈 + 传统测试语法 |
| 现有测试框架集成 | ts-mocha | 兼容 Mocha 生态           |

所有工具均支持 TypeScript 5.0+ 版本，建议结合项目规模选择：

- **中小型项目**: 可用 TSTyche 快速上手。
- **大型库开发**: 推荐 tsd 进行严格类型验证。
