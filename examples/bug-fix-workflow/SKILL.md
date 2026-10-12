---
name: bug-fix-workflow
description: >-
  按 5 步闸门修复 bug:先确认复现与落点,再先写失败测试、最小修复、删临时测试、跑 lint。
  用户报告 bug、CI 失败、回归失败时使用。
---

# Bug Fix Workflow

> 示例:默认只生成 Cursor。主栈按扫描结果只写一行,这里用 uni-app 演示。不要把 12 栈表抄进产物。

## 运行时执行协议(强制,先于任何写操作)

每轮第一行:`当前闸门: N/5 <共识|架构预检|TDD 红绿重构|跑完即删|Lint>`。
停步条文在 `.cursor/rules/bug-fix-workflow.mdc`,每轮已注入,此处不重复。未过当前闸门禁止写文件。

## 何时使用

- 功能报错、CI 失败、线上故障,需要先复现再改

## 落点

- **Cursor 技能**:`<project>/.cursor/skills/bug-fix-workflow/`
- **Cursor 常驻闸门**:`<project>/.cursor/rules/bug-fix-workflow.mdc`(`alwaysApply: true`)

## 本栈

- **主栈**:uni-app
- **测试**:`local-tests/`(镜像 `pages/` `api/`) / `npm run test:unit`
- **Lint**:`npm run lint`
- **类型检查**:`tsc --noEmit`
- **落点**:页面 `pages/<页>.vue`;接口 `api/<模块>.js`;请求封装 `utils/request.js`
- **跨端**:H5 通过后还要 `npm run dev:mp-weixin`

## 第 1 步要确认的点

一次只问一个,写进共识四行。

- 复现步骤、期望、实际
- 影响的页面或接口
- 不改什么

## 业务分支梳理与改动影响确认

> 每次实现功能改造或者新功能,需要梳理原业务分支,并列出改动影响,询问确认后再往下执行(每次仅问一个问题),最终达成共识才执行。

## 业务落点变动触发提示词补充

> 如果业务逻辑或工程结构代码发生变动导致代码落点变化,提示词缺失或者需要补充,也需要询问用户是否补充调整。

## 反模式

- ❌ 未声明当前闸门,或用户未确认就改代码
- ❌ 没复现、没失败测试就改
- ❌ 留下本次 `local-tests/`,或 lint 没过就结束
- ❌ 只改了 H5,没编目标小程序
