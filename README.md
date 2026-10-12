# generate-front-business-skills

**可复用的前端/原生/桌面工程 skill 生成器。**

它本身不解决任何业务问题,它的产出是另一个 skill 目录。

## 原始目标(本生成器自身的定位)

> 本生成器产出的所有 skill,**专用于协助用户使用 AI 工具编写业务代码**。
> 这是 generate-front-business-skills 的核心定位,**不需要在每次生成时重新设计**。

四类产出:

| 类别 | 典型例子 |
|---|---|
| **代码编写规范** | 命名约定、目录结构、风格统一、注释规则、错误处理规范 |
| **工程流程固化** | 架构预检、TDD 红绿、Code Review、PR 提交流程、bug 修复流程 |
| **最佳实践沉淀** | 业务分支梳理、改动影响确认、技术栈适配、跨端平台分支 |
| **AI 工具落地** | 触发命令、扫描提示、错误提醒、与编辑器(Claude Code / Cursor / Codex)集成 |

> 跑 `/generate-front-business-skills` 时,Q1 默认沿用上述目标,只问"沿用 / 自定义",
> 选"沿用"即直接进入 Q2,不再追问 Q1。

---

## 怎么用 — 触发指令

| 触发方式 | 用户输入 | 备注 |
|---|---|---|
| **斜杠命令**(推荐) | `/generate-front-business-skills` | 最快、最确定 |
| **明确意图** | "帮我生成一个 skill"、"按规范生成技能"、"做一个技能"、"把这个流程固化成 skill" | Claude Code 按 frontmatter `description` 自动匹配触发 |
| **描述目标** | "为新功能做架构预检"、"为提 PR 写规范"、"为联调做接口对齐"、"为 bug 修复做流程" | 描述流程意图即可,生成器会按多轮问答固化为 skill |

按多轮问答(每次仅问一个问题)逐项回答,得到最终复述后回 "OK / 落盘 / 按此执行",
生成器就会生成一个完整的 skill。

## 默认值(用户可改)

> ⚠ **本生成器的产物默认值已固化**,跑 `/generate-front-business-skills` 时大部分题目选"沿用"即跳过。

| 项 | 默认值 |
|---|---|
| **slug** | **`business-coding-flow`**(业务编码流程) |
| **输出位置** | **项目级工程目录**(默认触发) |
| **目标编辑器** | **仅 Cursor**(可改成追加 Claude Code / Codex / 其他) |
| **默认产物落点** | `<project>/.cursor/skills/business-coding-flow/` + `<project>/.cursor/rules/business-coding-flow.mdc` |
| 5 步闸门 / 子目录 / 业务规则 | 全部默认启用(详见 SKILL.md §3.1 默认值速查) |

**最简路径**:Q1 沿用 → Q2 沿用 → Q3 填一句话 description → Q4~Q10 全部沿用 → Q11 确认 → 落盘。
**用户可改**:任何默认值都可在对应题目选"自定义"覆盖。

---

## 它强制做什么

1. **先扫工程** —— 用指纹矩阵识别主栈 + **TDD / Lint / 类型检查 环境检测**,记入业务代码量、既有 skills / 规则
2. **一次一问** —— 每次仅问一个问题,得到确认才记录
3. **5 步闸门** —— 停步全文只放 `.cursor/rules/<slug>.mdc`(`alwaysApply: true`)。`SKILL.md` 只留指针和本栈命令,宜 ≤ 80 行。顺序仍是共识 → 架构预检 → TDD 红绿重构 → 跑完即删 → Lint
4. **TDD 环境提醒** —— 工程未具备 TDD 环境时,**告知 + 给安装命令 + 询问**(A 装/B 降级/C 延后),绝不擅自安装或擅自降级
5. **Lint 环境提醒** —— 工程未配置 Lint 工具时,同样三步(告知 / 给安装命令 / 询问)
6. **类型检查提醒** —— TypeScript 工程缺 typescript / tsconfig 时,同样三步
7. **业务分支梳理规则** —— 检测到工程有大量既有业务时,自动注入:
   > 每次实现功能改造或者新功能,需要梳理原业务分支,并列出改动影响,
   > 询问确认后再往下执行(每次仅问一个问题),最终达成共识才执行。
8. **业务落点变动触发提示词补充** —— **总是注入**(与上一条并列):
   > 如果业务逻辑或工程结构代码发生变动导致代码落点变化,提示词缺失或者需要补充,
   > 也需要询问用户是否补充调整。
9. **跑完即删** —— 自检通过后,删除测试脚本和中间产物
10. **多技术栈适配** —— 按主栈分支生成流程命令、目录约定、代码落点规则

## 支持的技术栈(12 栈)

| 主栈 | 指纹 | 默认扫描目录 |
|---|---|---|
| **uni-app** | `manifest.json` + `pages.json` + `App.vue` + `main.{js,ts}` | `pages/` `components/` `static/` `uni_modules/` |
| **Vue 2/3** | `package.json` 依赖 `vue` + `src/main.{js,ts}` + `src/App.vue` | `src/views/` `src/components/` `src/composables/` `src/store/` |
| **React** | `package.json` 依赖 `react` + `src/main.{ts,tsx}` + `src/App.{tsx,jsx}` | `src/pages/` `src/components/` `src/hooks/` `src/store/` |
| **React Native** | `react-native` + `metro.config.js` + `ios/` + `android/` | `src/screens/` `src/navigation/` `src/services/` |
| **Taro** | `@tarojs/taro` 依赖 + `project.config.json` + `babel.config.js` + `app.config.{ts,js}` | `src/pages/` `src/components/` `src/store/` `src/utils/` |
| **Android 原生** | `build.gradle` + `settings.gradle` + `AndroidManifest.xml` | `app/src/main/java/<pkg>/{ui,data,domain,di,util,base}/` |
| **iOS 原生** | `*.xcodeproj` + `Podfile` / `Package.swift` | `Sources/{App,Features,Common,Domain,Networking,Utils}/` |
| **Flutter** | `pubspec.yaml` + `lib/main.dart` + `android/` + `ios/` | `lib/{models,services,providers,screens,widgets,utils}/` |
| **Electron**(桌面) | `electron` 依赖 + `electron-builder.yml` 或 `forge.config.*` | `electron/main/ preload/` + `src/renderer/` |
| **Tauri**(桌面) | `src-tauri/Cargo.toml` + `src-tauri/tauri.conf.json` + `src-tauri/src/main.rs` | `src/`(前端)+ `src-tauri/src/{commands,domain,api}/`(Rust) |
| **原生微信小程序** | `app.json` + `app.{js,ts}` + `project.config.json` | `pages/` `components/` `utils/` |
| **原生鸿蒙** | `AppScope/app.json5` + `entry/src/main/ets/EntryAbility.ets` | `entry/src/main/ets/{pages,components,model,viewmodel,utils}/` |

跨端栈(uni-app / Taro / RN / Flutter / 鸿蒙)额外问**目标平台**(H5 / 各小程序 / Android / iOS),并按平台分支生成工作流动作。
桌面栈(Electron / Tauri)额外问**目标桌面平台**(Windows / macOS / Linux),并按平台分支打包验证。

## 支持的目标编辑器 / 工具

> ⚠ **核心约束**:被生成的 skill **必须默认触发**——只要用户用 AI 工具执行编码就自动生效,
> **不需要额外触发条件**(不要求 `/<slug>` 命令)。
> **Q10 默认仅 Cursor,可改。** 不改就不写 Claude Code,也不写 sync 脚本。

| 编辑器 | 用户级路径 | 项目级路径(默认触发) | `/<slug>` 命令 | **默认触发机制** | 状态 |
|---|---|---|---|---|---|
| **Claude Code** | `~/.claude/skills/<slug>/` | `<project>/.claude/skills/<slug>/` | `/<slug>`(补强) | **项目级 skills 自动扫描 + `description` 意图匹配** | ✅ 已实测 |
| **Cursor** | `~/.cursor/skills/<slug>/` | `<project>/.cursor/skills/<slug>/` **并且** `<project>/.cursor/rules/<slug>.mdc` | `/<slug>`(补强) | **`.cursor/rules/<slug>.mdc`(`alwaysApply: true`)每轮注入闸门** | ✅ 已实测 |
| **Codex** | TODO(用户告知) | TODO(用户告知) | TODO | TODO(以该工具文档为准) | ⚠ 占位 |
| **Aider / Continue / Cline / Zed** | 见 [references/editor-adapters.md](references/editor-adapters.md) | 同左 | 各异 | 部分支持默认触发(详见 adapters) | 简表支持 |

### 多处复用与文件统一性(目标编辑器选 ≥2 个时)

| 策略 | 一句话 | 适用场景 |
|---|---|---|
| **A 一源多端部署**(推荐) | 主源 + `scripts/sync.ps1` / `sync.sh` 复制 | 想保持源清晰,跨机器同步方便 |
| **B 软链接 / junction** | 主源放一处,各目录 `mklink /J` / `ln -s` | 同机器多 IDE 同时用 |
| **C 每端独立副本** | 各编辑器一份,各自维护 | 编辑器之间差异大,确实需要分支 |
| **D 主源 + 每端 wrapper** | 主源完整,各端只放极简 wrapper 链回 | 想避免重复又不想用软链接 |

策略 A/D 时,生成的 skill 会带 `scripts/sync.ps1` 与 `scripts/sync.sh`,并在 SKILL.md 顶部列出每个编辑器的具体落点。

**默认只写 Cursor 的项目级目录。** 用户追加了第二个编辑器,才做多处复用。

---

## 目录结构

```
generate-front-business-skills/
├── SKILL.md                              # 主入口(触发指令见 frontmatter description + 顶部"## 触发指令")
├── README.md                             # 本文件
├── references/
│   ├── interview-questions.md            # 多轮问答的提问模板(含 Q-Editor / Q-Editor-Path / Q-Editor-Sync)
│   ├── workflow-gates.md                 # 5 步闸门细则 + 运行时执行协议 + Cursor 常驻规则
│   ├── skill-template.md                 # SKILL.md 骨架(已含技术栈字段 + 3 个环境占位 + 目标编辑器字段)
│   ├── tech-stack-adapters.md            # 技术栈指纹与适配库(关键,12 栈 + 3 类环境)
│   └── editor-adapters.md                # 编辑器 / AI 工具适配库(关键,Claude Code/Cursor 已实测)
└── examples/
    └── bug-fix-workflow/
        ├── SKILL.md                      # 瘦身后的产物示例(仅 Cursor + 单栈)
        └── cursor-rule.mdc               # Cursor alwaysApply 闸门规则示例
```

---

## 安装

### 用户级(推荐,直接触发)

把整个目录放到 Claude Code 用户级 skills 目录即可,**无需重启**:

```bash
# Windows PowerShell
Copy-Item -Recurse -Force `
  "C:\Users\37313\Desktop\generate-front-business-skills" `
  "C:\Users\37313\.claude\skills\generate-front-business-skills"
```

或用 `cp -r`(Git Bash / WSL):

```bash
cp -r "C:/Users/37313/Desktop/generate-front-business-skills" \
      "C:/Users/37313/.claude/skills/generate-front-business-skills"
```

安装后,Claude Code 立刻能识别 `/generate-front-business-skills` 命令。

### 项目级

- `<your-project>/.claude/skills/generate-front-business-skills/`(项目内,优先)
- `<your-project>/.cursor/skills/generate-front-business-skills/`(Cursor 兼容)

---

## 不做什么

- 不解决任何具体业务问题(它只生成 skill)
- 不覆盖已存在的同名 skill(若发现冲突,会先告知用户)
- 不在没有用户确认的情况下写文件
- 不擅自安装测试框架 / Lint 工具 / TypeScript(必须先问)
