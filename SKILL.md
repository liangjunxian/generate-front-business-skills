---
name: generate-front-business-skills
description: >-
  通过多轮一对一问答,为当前前端/原生/桌面工程(支持 uni-app、Vue、React、Android 原生、
  React Native、Flutter、Taro、Electron、Tauri、原生小程序、原生鸿蒙等脚手架结构)生成
  一个完整、可复用、强制 5 步闸门(共识 → 架构预检 → TDD 红绿重构 → 跑完即删 → Lint)
  的 Cursor skill。扫描阶段会主动检测 TDD / Lint / 类型检查环境,任一缺失时
  告知用户并给出安装命令,经用户确认后才继续;命中大量既有业务时自动注入"业务分支
  梳理与改动影响确认"规则;并始终注入"业务落点变动触发提示词补充"规则。默认产物名
  `business-coding-flow`,默认只落到项目级 `.cursor/skills/` 与 `.cursor/rules/`
  (常驻闸门),用户可改成同时包含 Claude Code / Codex / 其他。在用户说
  "帮我写一个 skill"、"按规范生成技能"、"为这个项目生成一个 XXX 工作流"、或希望把
  某条流程固化为 skill 时触发。
---

# Generate Front Business Skills(技能生成器)

> **这是一个生成器 skills。它本身不解决业务问题,它的产出是另一个 skills 目录。**

## 触发指令(直接调用)

| 触发方式 | 用户输入 | 备注 |
|---|---|---|
| **斜杠命令**(推荐) | `/generate-front-business-skills` | 最快、最确定 |
| **明确意图** | "帮我生成一个 skill"、"按规范生成技能"、"做一个技能"、"把这个流程固化成 skill" | 由 Claude Code 按 frontmatter `description` 自动匹配触发 |
| **描述目标** | "为新功能做架构预检"、"为提 PR 写规范"、"为联调做接口对齐"、"为 bug 修复做流程" | 描述流程意图即可,生成器会按多轮问答固化为 skill |

## 默认值速查(跑一次极简流程)

> ⚠ **本生成器的"默认值"是固化的**——跑 `/generate-front-business-skills` 时,大部分题目都有默认值,
> 用户选"沿用"即可跳过。**最简路径下,用户只需答 Q3 一句话 description + Q11 落盘确认**。

| 项 | 默认值 | 是否可跳过 |
|---|---|---|
| Q1 目标 | 协助用户使用 AI 工具编写业务代码 | ✅ 沿用即跳过 |
| **Q2 命名(slug)** | **`business-coding-flow`** | ✅ 沿用即跳过(用户可改) |
| Q3 一句话 description | (用户必填) | ❌ 必填(决定默认触发是否生效) |
| Q4 阶段数 | 5 步闸门 | ✅ 沿用即跳过 |
| Q5 各阶段输入输出 | (Q4 选自定义才展开) | ✅ 沿用即跳过 |
| Q6 子目录 | 不建(命令写进「本栈」) | ✅ 沿用即跳过 |
| Q7 业务分支梳理 | 启用(命中大量既有业务时) | ✅ 沿用即跳过 |
| Q8 业务落点变动规则 | 启用(总是) | ✅ 沿用即跳过 |
| **Q9 输出位置** | **`<project>/.cursor/skills/<slug>/` + `.cursor/rules/<slug>.mdc`** | ✅ 沿用即跳过(用户可改) |
| **Q10 目标编辑器** | **仅 Cursor** | ✅ 沿用即跳过(用户可改) |
| Q11 最终复述 | (必填,确认才落盘) | ❌ 必答 |

**最简路径示意**:Q1 → 沿用 → Q2 → 沿用(`business-coding-flow`) → Q3(填一句话 description)→ Q4~Q10 全部沿用 → Q11 确认 → **只落盘 `<project>/.cursor/skills/business-coding-flow/` + `<project>/.cursor/rules/business-coding-flow.mdc`**

---

## 原始目标(本生成器自身的定位)

> ⚠ **本节是 generate-front-business-skills 的核心定位,不需要在每次生成时询问用户。**

本生成器产出的所有 skill,**专用于协助用户使用 AI 工具编写业务代码**。
其"原始目标"是固定的,具体表现为以下四类产出:

| 类别 | 典型例子 |
|---|---|
| **代码编写规范** | 命名约定、目录结构、风格统一、注释规则、错误处理规范 |
| **工程流程固化** | 架构预检、TDD 红绿、Code Review、PR 提交流程、bug 修复流程 |
| **最佳实践沉淀** | 业务分支梳理、改动影响确认、技术栈适配、跨端平台分支 |
| **AI 工具落地** | 触发命令、扫描提示、错误提醒、与编辑器(Claude Code / Cursor / Codex)集成 |

**Q1 预填默认值**(每次跑 `/generate-front-business-skills` 时,这一题不需要重新设计):

```
目标:    协助用户使用 AI 工具编写业务代码
场景:    团队/个人希望把"用 AI 写代码"的最佳实践固化为可复用的 skill
产出:    一个强制 5 步闸门(共识 → 架构预检 → TDD → 跑完即删 → Lint)的 skill
```

> 生成器**只**问用户一次:**"沿用默认目标,还是自定义?"**
> - 选 **沿用**(推荐) → 直接进入 Q2(命名),不再追问 Q1
> - 选 **自定义** → 才展开问"这个 skill 的具体场景是什么"

> 安装位置:本 skill 需放在 Claude Code 能识别的 skills 目录下,**用户级**推荐:
>
> ```
> C:\Users\37313\.claude\skills\generate-front-business-skills\
> ```
>
> 项目级:`<project>/.claude/skills/generate-front-business-skills/` 或 `<project>/.cursor/skills/generate-front-business-skills/`(Cursor 兼容)。

## 0. 何时启用

满足以下任一条件即触发:

- 用户说 "帮我生成/写一个 skill"、"做一个技能"、"把这个流程固化成 skill"
- 用户描述了一个想要长期复用的工作流(团队约定、代码规范、提交流程、bug 处理流程…)
- 用户希望把当前的对话步骤沉淀成可被 `/<name>` 触发的能力

不适用:临时一次性任务、纯问答、纯代码调试(直接做即可,不需要生成 skill)。

---

## 1. 强制 5 步闸门(本生成器自身也必须遵守)

```
generate-front-business-skills Gates:
- [ ] 1. 共识      → 与用户就"目标 skill 的产出形态"达成逐项确认(每次仅问一个问题)
- [ ] 2. 架构预检  → 扫描当前工程,落定 skill 目录与依赖
                    含 §2.4 TDD / §2.5 Lint / §2.6 类型检查 三个环境检测
- [ ] 3. TDD 红绿  → 必做:对"询问脚本/询问清单"先写一份自检脚本并跑通
        ⚠ 若工程未具备 TDD 环境,见 §2.4.2 处理;经用户确认可降级为"最小自检"
- [ ] 4. 跑完即删  → 自检通过后,删除测试脚本/中间产物
- [ ] 5. Lint      → SKILL.md 与 references/*.md 通过 Markdown 结构与 frontmatter 校验
        ⚠ 生成的 skill 自身的 Lint 命令不依赖工程 Lint 环境(它是 Markdown 校验)
```

> 这一闸门链必须出现在**每一个被生成的 skill** 中(见第 7 节"必须注入的核心规则")。
> 第 3 步**默认严格 TDD**;仅在用户明确选择 §2.4.2 的 (B) 或 (C) 时才允许降级。

---

## 2. 启动:扫描当前工程(必做,不可跳)

进入生成流程前,**先**用只读工具扫描当前工程,把以下信息记录下来,后续问答会用到。

> **重要**:不要默认所有工程都有 `package.json`。Android 原生、原生小程序、Flutter 工程根目录
> 可能是 Gradle / 项目描述文件。扫描必须按技术栈指纹并行,见 [references/tech-stack-adapters.md](references/tech-stack-adapters.md)。

### 2.1 通用扫描表(必跑)

| 维度 | 工具 | 必须拿到的信息 |
|---|---|---|
| 工程根 | `Bash pwd`、`Bash ls -la` | 是否在 git 工程内、是否有 `package.json` / `build.gradle` / `pom.xml` / `Cargo.toml` |
| **技术栈指纹** | 见 §2.2 指纹矩阵 | 命中一个主栈,记下**主栈 + 版本 + 构建工具 + 测试框架 + 包管理器** |
| 包结构 | `Bash ls src/` / `Bash ls app/src/main/`、`Glob "src/**"`、`Read pnpm-workspace.yaml` | 是否 monorepo、包名约定、目录分层(apps/core/shared/services/utils) |
| 目标平台 | `Read manifest.json`(uni-app)、`Read app.json` / `project.config.json`(小程序)、`Read app/build.gradle`(Android) | H5 / 各小程序 / Android / iOS / 桌面 |
| **TDD 环境检查** | 见 §2.4 | 已有测试框架 / 跑测命令能否成功 / 是否需要安装 |
| **Lint 环境检查** | 见 §2.5 | 已有 ESLint / Stylelint / Prettier / 平台自带 lint / 是否需要安装 |
| **类型检查检查** | 见 §2.6 | 已有 TypeScript / 平台自带类型系统 / 是否需要安装 |
| 既有 skills | `Glob ".claude/skills/**/SKILL.md"`、`Glob ".cursor/skills/**/SKILL.md"`、`Glob ".codex/**/SKILL.md"` | 是否有同名 skill 覆盖、是否需要避让 |
| **目标编辑器/工具** | 探测用户级 / 项目级 skills 目录存在情况 | 用户打算在哪些编辑器里使用新 skill |
| 业务代码量 | `Bash du -sh src/ app/src/main/ 2>/dev/null`、`Glob "src/**/*.{ts,tsx,vue,js,kt,java}" -0 \| wc -l` | 行数 / 文件数,用于判断"是否大量既有业务" |
| 既有规则 | `Read CLAUDE.md`、`Glob ".cursor/rules/*.mdc"` | 工程已有约定,新 skill 必须兼容而非覆盖 |

### 2.2 技术栈指纹矩阵(扫描时直接对照)

| 主栈 | 必须命中的指纹 | 包管理器 | 构建工具 | 测试框架 | 默认扫描工具 |
|---|---|---|---|---|---|
| **uni-app** | `manifest.json` + `pages.json` + `App.vue` + `main.{js,ts}` | npm/pnpm 或 HBuilder 内置 | HBuilder / Vite / Webpack CLI | 视项目 | `Bash ls pages/ components/ static/ uni_modules/` |
| **Vue 2/3** | `package.json` 依赖 `vue` + `src/main.{js,ts}` + `src/App.vue` + `vite.config.*` 或 `vue.config.*` | npm/pnpm/yarn | Vite / Vue CLI / Webpack | Vitest / Jest | `Bash ls src/views/ src/components/ src/composables/ src/router/ src/store/ src/api/ src/utils/` |
| **React** | `package.json` 依赖 `react` + `src/main.{js,ts,tsx}` + `src/App.{tsx,jsx}` + `vite.config.*` 或 `next.config.*` | npm/pnpm/yarn | Vite / Webpack / Next.js | Vitest / Jest + RTL | `Bash ls src/pages/ src/components/ src/hooks/ src/contexts/ src/store/ src/api/ src/utils/` |
| **React Native** | `package.json` 依赖 `react-native` + `metro.config.js` + `ios/` + `android/` | npm/yarn | Metro + Gradle + Xcode | Jest + Detox | `Bash ls src/screens/ src/components/ src/navigation/ src/services/ src/utils/` |
| **Android 原生** | `build.gradle` 或 `build.gradle.kts` + `settings.gradle*` + `app/src/main/AndroidManifest.xml` + `app/src/main/java/**` 或 `app/src/main/kotlin/**` | Gradle | Gradle | JUnit / Espresso / Robolectric | `Bash ls app/src/main/java/<package>/{activity,fragment,adapter,model,util,contract,presenter,view,repository}/` |
| **iOS 原生** | `*.xcodeproj` 或 `*.xcworkspace` + `Podfile` + `Package.swift` | CocoaPods / SPM | Xcode | XCTest / Quick | `Bash ls Sources/ Classes/ Modules/` |
| **Flutter** | `pubspec.yaml` + `lib/main.dart` + `android/` + `ios/` | pub | Flutter SDK | test / flutter_test | `Bash ls lib/{models,services,widgets,screens,utils}/` |
| **原生微信小程序** | `app.json` + `app.js/ts/wxss` + `project.config.json` + `sitemap.json` | 无 | 微信开发者工具 | miniprogram-automator | `Bash ls pages/ components/ utils/ images/` |
| **原生鸿蒙(ArkTS)** | `AppScope/` + `entry/` + `oh-package.json5` + `build-profile.json5` | ohpm | Hvigor | 视项目 | `Bash ls entry/src/main/ets/{pages,components,model,viewmodel,utils}/` |
| **Taro**(React/Vue 跨端) | `package.json` 依赖 `@tarojs/taro` + `project.config.json`(微信)+ `babel.config.js` + `app.config.{ts,js}` | npm/pnpm/yarn | Taro CLI + Webpack | Jest / Vitest | `Bash ls src/pages/ src/components/ src/store/ src/utils/ src/assets/` |
| **Electron** | `package.json` 依赖 `electron` + `electron-builder` / `forge.config.*` + `electron/main.{js,ts}` 或 `src/main/*` | npm/pnpm/yarn | electron-builder / Vite + electron | Jest / Vitest + Spectron / Playwright | `Bash ls electron/{main,preload,renderer}/` 或 `src/{main,renderer,preload}/` |
| **Tauri**(Rust + Web) | `src-tauri/Cargo.toml` + `src-tauri/tauri.conf.json` + `src-tauri/src/main.rs` + 前端 `package.json` | 前端 npm + Rust cargo | Tauri CLI + Vite + Cargo | 前端 Vitest/Jest + Rust cargo test | `Bash ls src/{pages,components,hooks,utils}/` + `Bash ls src-tauri/{src,icons,capabilities}/` |

> **冲突时优先级**:uni-app > Vue / React(uni-app 工程通常依赖其中之一);原生小程序 > 同框架跨端。
> 多栈混合(如 uni-app + Vue3、React Native + TypeScript)按"主栈 + 辅栈特性"两栏同时记录。

### 2.3 业务逻辑量判断阈值(技术栈无关)

任一命中即视为"大量既有业务":

- 业务源码总行数 > 5000 行 **或**
- 业务模块目录(apps / core / services / business / domain / modules / model / repository 等)文件数 ≥ 30 **或**
- 存在明显的分层架构(core / shared / apps / services / utils)且每个分层有多个文件

> 命中 → 主 SKILL.md 第 7 节的"业务分支梳理"规则**必须**注入到生成的 skill 中。

### 2.4 TDD 环境检测与处理(必做)

> 5 步闸门的第 3 步 "TDD 红绿" 依赖工程具备可跑的测试环境。
> 扫描阶段**必须**做以下检测,缺则提醒并询问用户怎么处理。

#### 2.4.1 检测方法(按主栈分支)

| 主栈 | 检测命令 | 命中即视为"已具备 TDD 环境" |
|---|---|---|
| uni-app / Vue / React / RN | `cat package.json \| grep -E "vitest\|jest\|@vue/test-utils\|@testing-library\|@dcloudio/uni-app"` 或 `Bash ls -la node_modules/.bin/vitest 2>/dev/null` | devDependencies 含任一测试包 |
| Android | `Bash ls app/src/test/` 或 `Bash cat app/build.gradle \| grep -E "junit\|mockk\|mockito\|robolectric\|espresso"` | 任一命中 |
| iOS | `Bash find . -name "*Tests.swift" -o -name "*Spec.swift"` 或 `cat Package.swift` | 任一命中 |
| Flutter | `Bash ls test/` 或 `Bash cat pubspec.yaml \| grep -E "flutter_test\|test:"` | 任一命中 |
| 原生微信小程序 | `Bash find . -name "*.test.js"` 或 `cat package.json \| grep miniprogram-automator` | 任一命中 |
| 鸿蒙 | `Bash ls entry/src/test/` 或 `cat entry/oh-package.json5 \| grep -E "test\|@ohos\|hypium"` | 任一命中 |

#### 2.4.2 缺失时必须执行的提醒与询问

扫描判定为 **"未具备 TDD 环境"** 时,生成器**必须**做以下三件事:

1. **告知** —— 列出当前主栈缺失的具体内容(如"未检测到 vitest/jest,无测试目录")
2. **给出安装建议** —— 按主栈给一条可直接复制的安装命令(详见 [tech-stack-adapters.md](references/tech-stack-adapters.md) 各节 "TDD 缺失时的安装建议")
3. **询问用户**(一次一问)—— 选项:
   - **(A) 现在安装**(推荐)→ 装好后再继续生成流程
   - **(B) 跳过 TDD,降级为"最小自检"** → 5 步闸门第 3 步从 "TDD 红绿" 降级为 "最小自检(可选)",并在生成的 skill 中标注 "本工程暂未启用 TDD,新逻辑不写测试"
   - **(C) 延后处理** → 生成的 skill 标注 "需先安装 TDD 环境才可使用第 3 步",等用户后续补上

> **不得** 在用户未确认前擅自安装测试框架 / 擅自降级闸门。

#### 2.4.3 安装命令索引(按主栈,具体见 references/tech-stack-adapters.md)

| 主栈 | 缺失时推荐安装命令 |
|---|---|
| uni-app | `npm i -D vitest @dcloudio/uni-app @vue/test-utils` |
| Vue | `npm i -D vitest @vue/test-utils jsdom` |
| React | `npm i -D vitest @testing-library/react jsdom` |
| React Native | `npm i -D jest @testing-library/react-native` |
| Android | `app/build.gradle` 添加 `testImplementation 'junit:junit:4.13.2'` + `testImplementation 'org.mockito:mockito-core:5.x'` |
| iOS | 用 Xcode 创建 Unit Test Bundle target,或 `swift package add-dependency` Quick/Nimble |
| Flutter | `flutter pub add --dev test flutter_test`(默认随 `flutter create` 已带) |
| 原生微信小程序 | `npm i -D miniprogram-automator jest` |
| 鸿蒙 | `entry/oh-package.json5` 添加 `@ohos/hypium` |

### 2.5 Lint 环境检测与处理(必做)

> 5 步闸门的第 5 步 "Lint" 本身是 Markdown 校验,不依赖工程 Lint;
> 但**被生成的 skill** 内部的 Lint 步骤会引用工程 Lint 命令(若工程有)。
> 所以扫描阶段**必须**做 Lint 环境检测,缺则提醒并询问用户。

#### 2.5.1 检测方法(按主栈分支)

| 主栈 | 检测命令 | 命中即视为"已具备 Lint 环境" |
|---|---|---|
| uni-app / Vue / React / RN | `Bash cat package.json \| grep -E "eslint\|stylelint\|prettier\|@vue/eslint"` 或 `Bash ls -la .eslintrc* eslint.config.* 2>/dev/null` | devDependencies 含任一,或存在 eslint 配置文件 |
| Android | `Bash cat app/build.gradle \| grep -E "lintOptions\|lint "` 或 `Bash ls app/lint.xml 2>/dev/null` | build.gradle 启用 lint 或存在 lint.xml |
| iOS | Xcode 自带 + SwiftLint:`Bash which swiftlint 2>/dev/null` 或 `cat .swiftlint.yml 2>/dev/null` | 任一命中 |
| Flutter | `Bash cat pubspec.yaml \| grep -E "flutter_lints\|lint:"` 或 `Bash ls analysis_options.yaml 2>/dev/null` | 任一命中 |
| Taro | `Bash cat package.json \| grep -E "@tarojs/eslint-config\|eslint"` | devDependencies 含 |
| Electron | 同 uni-app/Vue/React(取决于主框架) | 同上 |
| Tauri | 前端部分同主框架;Rust 部分:`cat src-tauri/Cargo.toml \| grep -E "clippy\|rustfmt"` | 任一命中 |
| 原生微信小程序 | 开发者工具自带 + `cat .eslintrc* 2>/dev/null` | 任一命中 |
| 鸿蒙 | hvigor 自带 lint | `Bash ls -la hvigorw 2>/dev/null` 视为具备 |

#### 2.5.2 缺失时必须执行的提醒与询问

扫描判定为 **"未具备 Lint 环境"** 时,生成器**必须**做以下三件事:

1. **告知** —— 列出当前主栈缺失的具体内容(如"未检测到 ESLint/Stylelint,无 .eslintrc 配置文件")
2. **给出安装建议** —— 按主栈给一条可直接复制的安装命令(详见 [tech-stack-adapters.md](references/tech-stack-adapters.md) §16)
3. **询问用户**(一次一问)—— 选项:
   - **(A) 现在安装**(推荐)
   - **(B) 跳过 Lint 配置,第 5 步仅做 Markdown 结构校验**(降级)
   - **(C) 延后处理,生成的 skill 标注"需先安装 Lint 环境"**

> **不得** 在用户未确认前擅自安装 Lint 工具 / 擅自降级。

### 2.6 类型检查环境检测与处理(必做)

> TypeScript 工程依赖类型检查,Android/iOS/Flutter/鸿蒙依赖各自类型系统。
> 扫描阶段必须做检测,缺则提醒并询问用户。

#### 2.6.1 检测方法(按主栈分支)

| 主栈 | 检测命令 | 命中即视为"已具备类型检查环境" |
|---|---|---|
| uni-app / Vue / React / RN / Taro / Electron / Tauri(前端) | `Bash cat package.json \| grep -E "\"typescript\":\|\"@types/"` 或 `Bash ls tsconfig.json 2>/dev/null` | devDependencies 含 typescript + tsconfig 存在 |
| Android(Kotlin) | `Bash cat app/build.gradle \| grep kotlin` 或 `Bash find app/src -name "*.kt"` | 任一命中 |
| Android(Java) | 自带 javac,无需额外配置(默认具备) |
| iOS | Xcode 自带 + Swift 编译器(默认具备) |
| Flutter | Dart 自带 + `Bash cat pubspec.yaml \| grep flutter:` | 任一命中 |
| 鸿蒙 | hvigor 自带类型检查(默认具备) |

#### 2.6.2 缺失时必须执行的提醒与询问(仅 TypeScript 工程需要)

> Java/Kotlin/Swift/Dart/ArkTS 工程类型检查默认具备,无需询问。
> 仅 **TypeScript 工程缺 typescript / tsconfig** 时询问。

1. **告知** —— 如"未检测到 typescript 依赖,无 tsconfig.json"
2. **给出安装建议** —— `npm i -D typescript` + 初始化 `npx tsc --init`
3. **询问用户**(一次一问)—— 选项:
   - **(A) 现在安装**(推荐)
   - **(B) 跳过类型检查,生成的 skill 标注"暂不启用类型检查"**(降级)
   - **(C) 延后处理**

> **不得** 擅自安装 typescript 或擅自降级闸门。

---

## 3. 多轮一对一问答(每次仅问一个问题)

> **铁律:一次只问一个问题。** 用户回答 + 显式确认("OK / 确认 / 按这个走 / 同意 / 不改")
> 之后,才把答案记入工作记忆并进入下一个问题。未得到确认 → 不记录、不进入下一步。

### 3.1 问题清单(按顺序,逐个问)

> **本节共 11 题**。Q1 / Q2 / Q4 / Q6 / Q7 / Q8 / Q9 / Q10 全部**预填默认值**(见下方"默认值速查"),选"沿用"即直接跳过。
> Q3(一句话 description)**必填**;Q5(各阶段输入输出)若 Q4 选"自定义"才需要展开;Q11 最终复述必答。
> Q1.5 / Q4.5 / Q6.5 / Q-Lint / Q-TypeCheck 是**条件触发题**:只有扫描结果命中相应条件时才会问。

#### 默认值速查(用户选"沿用"即用这些)

| 项 | 默认值 |
|---|---|
| Q1 目标与触发场景 | "协助用户使用 AI 工具编写业务代码"(本生成器原始目标) |
| **Q2 命名(slug)** | **`business-coding-flow`**(本生成器预设,代表"业务编码流程");用户可指定其他 |
| Q3 description | (用户必填,一句话) |
| Q4 核心阶段数 | 5 步闸门(共识 → 架构预检 → TDD → 跑完即删 → Lint) |
| Q6 子目录 | 不建;本栈命令写进 SKILL.md。放不下才加一个 `references/` 文件 |
| Q7 业务分支梳理 | 默认启用(命中"大量既有业务"时) |
| Q8 业务落点变动规则 | 默认启用(总是) |
| **Q9 输出位置(项目级默认)** | **`<project>/.cursor/skills/business-coding-flow/`** + **`<project>/.cursor/rules/business-coding-flow.mdc`**(`alwaysApply: true`);用户可改 |
| **Q10 目标编辑器** | **仅 Cursor**;用户可改成追加 Claude Code / Codex / 其他 |
| Q-Editor-Path | 项目级(默认触发) |

#### 题目

1. **目标与触发场景** —— **预填默认**(见 §"原始目标"),只问"沿用 / 自定义":
   > 本生成器产出专用于"协助用户用 AI 工具编写业务代码"的 skill。
   > 预填目标已固定,这次要:
   > - **沿用**(推荐)—— 直接进入下一题
   > - **自定义** —— 这个 skill 是为了解决什么**特定**的重复问题?在什么场景下被触发?

2. **命名(slug)** —— **预填 `business-coding-flow`**,只问"沿用 / 自定义":
   > 产物默认名:`business-coding-flow`(业务编码流程,本生成器预设)。
   > 会作为目录名与 `/<slug>` 命令(若用命令触发)。
   > - **沿用**(推荐)—— 直接进入下一题
   > - **自定义** —— 给出新的 kebab-case slug

3. **一句话描述** —— **必填**(用于 frontmatter `description`,决定默认触发是否生效):
   > 用一句话写它的 description,描述**触发场景 + 产出**;description 写得宽泛才能让 AI 工具在编码场景默认加载。
   > 例:"按 5 步闸门(共识 → 架构预检 → TDD → 跑完即删 → Lint)修复 bug,自动按工程主栈分支命令,扫描阶段检测 TDD/Lint/类型检查环境。"

4. **核心阶段数** —— **预填 5 步闸门**,只问"沿用 / 自定义":
   > - **沿用**(推荐)—— 默认 5 步闸门
   > - **自定义** —— 给阶段数 + 每阶段名字

5. **每个阶段的输入/输出/门禁条件** —— 选"自定义"时展开(逐个阶段追问)

6. **是否需要子目录** —— **预填不建**,只问"沿用 / 自定义":
   > - **沿用**(推荐)—— 不建子目录,本栈命令写在 SKILL.md
   > - **自定义** —— 确实放不下时再加 `references/`

7. **是否注入业务分支梳理规则** —— **预填启用**(命中"大量既有业务"时),只问"沿用 / 自定义":
   > - **沿用**(推荐)
   > - **禁用** —— 不注入此规则

8. **是否注入业务落点变动触发提示词补充** —— **总是启用**(总是注入,除非用户明确说不要),只问"启用 / 禁用":
   > - **(A) 默认启用**(推荐)
   > - **(B) 禁用**(仅当用户明确说不要)

9. **输出位置** —— **预填项目级 Cursor 目录**,只问"沿用 / 自定义":
   > 默认只写两处:
   > - `<project>/.cursor/skills/business-coding-flow/`
   > - `<project>/.cursor/rules/business-coding-flow.mdc`(`alwaysApply: true`)
   > - **沿用**(推荐)
   > - **用户级** —— `C:/Users/37313/.cursor/skills/business-coding-flow/`(规则仍要进工程 `.cursor/rules/`,否则闸门不会每轮执行)
   > - **自定义** —— Other 输入

10. **目标编辑器 / 工具** —— **预填仅 Cursor**,只问"沿用 / 自定义":
    > - **沿用**(推荐)—— 只生成 Cursor
    > - **自定义** —— 追加或改成 Claude Code / Codex / 其他(Codex 需用户告知目录与格式,否则只标 TODO)

11. **最终复述与落盘确认** —— 把所有答案按 SKILL.md 模板复述一遍(含 slug、description、目标编辑器与默认触发方式),等用户最终确认才落盘

### 3.1+ 条件触发题与 Q10 展开题

> 本节包含两类题:
> - **条件触发题**(由扫描结果决定是否问):Q1.5 / Q2.5 / Q-Lint / Q-TypeCheck / Q4.5 / Q6.5
> - **Q10 展开题**(若用户在 Q10 选了"自定义"或多个编辑器才问):Q-Editor-Path / Q-Editor-Sync

#### 条件触发题(按扫描结果选择性问,每次仍只问一个)

- **Q1.5 技术栈确认** —— 扫描已命中指纹矩阵,列出推断结果,请用户确认或覆盖:
  > 扫描结果判定工程主栈为 **`<X>`**(依据:`<指纹列表>`)。
  > 包管理器 `<X>`、构建 `<X>`、测试 `<X>`、目标平台 `<X>`。
  > 确认无误?或要覆盖成哪一个?(主栈候选:uni-app / Vue2 / Vue3 / React / React Native / Android / iOS / Flutter / 原生小程序 / 鸿蒙 / 其他)

- **Q2.5 TDD 环境处理** —— **仅当扫描判定"未具备 TDD 环境"** 时问,其余跳过:
  > 扫描未在工程中检测到可用的测试框架(具体依据:`<缺失项>`)。
  > 缺失建议安装命令:
  > ```
  > <按主栈的具体命令,见 references/tech-stack-adapters.md §X>
  > ```
  > 怎么处理?(一次仅答一项)
  > - **(A) 现在安装并继续**(推荐)
  > - **(B) 跳过 TDD,第 3 步降级为"最小自检"(可选)**
  > - **(C) 延后处理,生成的 skill 标注"需先安装 TDD 环境"**

- **Q-Lint 环境处理** —— **仅当扫描判定"未具备 Lint 环境"** 时问,其余跳过:
  > 扫描未在工程中检测到可用的 Lint 工具(具体依据:`<缺失项>`)。
  > 缺失建议安装命令:
  > ```
  > <按主栈的具体命令,见 references/tech-stack-adapters.md §16>
  > ```
  > 怎么处理?(一次仅答一项)
  > - **(A) 现在安装并继续**(推荐)
  > - **(B) 跳过 Lint 配置,第 5 步仅做 Markdown 结构校验**(降级)
  > - **(C) 延后处理,生成的 skill 标注"需先安装 Lint 环境"**

- **Q-TypeCheck 环境处理** —— **仅当 TypeScript 工程扫描判定"未具备类型检查"** 时问,其余跳过:
  > 扫描未在工程中检测到 TypeScript 类型检查配置(具体依据:`<缺失项>`)。
  > 缺失建议安装命令:
  > ```
  > <按主栈的具体命令,见 references/tech-stack-adapters.md §17>
  > ```
  > 怎么处理?(一次仅答一项)
  > - **(A) 现在安装并继续**(推荐)
  > - **(B) 跳过类型检查,生成的 skill 标注"暂不启用类型检查"**(降级)
  > - **(C) 延后处理**

- **Q4.5 目标平台** —— **仅当主栈为 uni-app / 原生小程序 / RN / Flutter / 鸿蒙** 时问,其余跳过:
  > 这个 skill 需要覆盖哪些目标平台?(多选)
  > 候选:H5 / 微信小程序 / 支付宝小程序 / 抖音小程序 / 百度小程序 / Android App / iOS App / 鸿蒙 / 桌面
  > (默认全选,可在生成出的 skill 中按平台分支细化)

- **Q6.5 技术栈分支策略** —— **仅当主栈为跨端栈(uni-app / RN / Flutter)** 时问:
  > 跨端工程下,这个 skill 的工作流是否需要**按平台分支**?(例如:小程序与 App 走不同构建命令)
  > 候选:
  > - 统一一套流程,平台差异用 if 分支写在闸门细节里(默认)
  > - 每平台一套独立子小节(流程长但清晰)
  > - 仅差异点单独说明,公共流程只写一次(推荐)

#### Q10 展开题(根据 Q10 答案选择性问)

- **Q-Editor-Path 每个编辑器的具体落点** —— **当选了 ≥1 个编辑器时,逐个问**(每个编辑器一问):
  > 默认候选:用户级 / **项目级(默认,确保默认触发)** / 自定义
  > 若选项目级,生成的 skill 会**默认触发**(项目级 skills 目录在 IDE 启动时自动扫描)。
  > 详细话术与前置检查(同名 skill 冲突处理)见 [references/interview-questions.md §Q-Editor-Path](references/interview-questions.md)。

- **Q-Editor-Sync 多处复用策略** —— **仅当选了 ≥2 个编辑器时**问:
  > 默认 (A) 一源多端部署 + `scripts/sync.ps1` / `sync.sh`。
  > 详细四策略话术见 [references/interview-questions.md §Q-Editor-Sync](references/interview-questions.md)。

### 3.2 询问方式

- **优先用 `AskUserQuestion`**:单选/多选题,带 2–4 个候选 + Other
- **填空类**(命名、一句话描述)用普通消息提问,**不要**塞进选项
- 每次提问前**复述上一题已确认的内容**,让用户回看

### 3.3 失败模式

- 用户回答模糊 → 再问一次,聚焦"哪一个选项最贴近?"
- 用户说"随便 / 你定" → 给一个**带说明的推荐选项**(标 Recommended),仍需用户点选才记录
- 用户中途要改前面的答案 → 退回那一题重答,后续已答但已记录的事实一并刷新

---

## 4. 架构预检:落定 skill 目录

| 决策点 | 默认 | 备选 |
|---|---|---|
| 目录名 | `<skill-name>/` (kebab-case) | 用户指定 |
| 主文件 | `SKILL.md` 必含;一级标题后第一节必须是「运行时执行协议」 | — |
| Cursor 常驻规则 | 目标含 Cursor 时必写 `<project>/.cursor/rules/<slug>.mdc`(`alwaysApply: true`);技能源内同内容文件名为 `cursor-rule.mdc` | 不选 Cursor 则不写 |
| frontmatter | `name` + `description`(单行 or >-),与目标编辑器格式兼容 | 可加 `when_to_use`(若编辑器支持) |
| 目标编辑器 | **仅 Cursor** | 用户在 Q10 追加 Claude Code / Codex / 其他 |
| 每个编辑器的具体落点 | 项目级 `.cursor/skills/<slug>/` + `.cursor/rules/<slug>.mdc` | 用户级 / 自定义 |
| 多处复用统一性 | 默认单编辑器,不生成 sync | 选了 ≥2 个编辑器才问 A/B/C/D |
| 子目录 | 不建 | 本栈命令放不下时才加一个 `references/` |
| 模板来源 | [references/skill-template.md](references/skill-template.md) | 用户已有模板 |
| 兼容既有 skill | 不覆盖已有同名 | 提示用户决定 rename / 替换 |
| 同步脚本 | A/D 策略时生成 `scripts/sync.ps1` + `scripts/sync.sh` | B/C 策略不生成 |

---

## 5. TDD 红绿:对生成器自身做一次自检

> 既然生成器本身也是代码,就要 TDD。

最小自检集(写在 `local-tests/generate-front-business-skills.test.md` 里,**跑完即删**):

```
Self-check:
- [ ] 给一个虚构需求,按 3.1 问完 9 步,生成出来的 SKILL.md 含 frontmatter(name+description)
- [ ] 生成出来的 SKILL.md 一级标题后第一节是「运行时执行协议」,含 `当前闸门: N/5` 与禁止写文件,且不重复常驻规则全文
- [ ] `cursor-rule.mdc` 含 5 步顺序,且 `alwaysApply: true` 的副本在 `.cursor/rules/<slug>.mdc`
- [ ] SKILL.md 只有命中的主栈,没有 12 栈全表,宜 ≤ 80 行
- [ ] 若命中"大量既有业务",生成的 SKILL.md 含"业务分支梳理"规则原文
- [ ] 路径、命名、Markdown 标题层级合规
```

---

## 6. 输出模板

产物骨架以 [references/skill-template.md](references/skill-template.md) 为准。停步全文只放 `cursor-rule.mdc`,原文在 [references/workflow-gates.md](references/workflow-gates.md)「Cursor 常驻规则」。不要在产物 SKILL.md 里再抄一遍。

**产物预算(默认只生成 Cursor)**

- 只写 `<project>/.cursor/skills/<slug>/SKILL.md` 与 `<project>/.cursor/rules/<slug>.mdc`
- SKILL.md 宜 ≤ 80 行:闸门指针、何时使用、本栈(只含命中的那一栈)、第 1 步确认点、必要的业务规则与反模式
- 禁止写入:12 栈全表、同步脚本、安装教程、与闸门平行的问答清单、「强制工作流」索引
- 环境正常则不写 TDD / Lint / 类型检查状态节;降级时各一行
- 用户改了 Q10 才追加其他编辑器目录;选了两个及以上且策略为 A/D 时才生成 sync 脚本

---

## 7. 必须注入到生成结果中的核心规则(任何被生成的 skill 都必带)

1. **5 步闸门**:共识 → 架构预检 → TDD 红绿重构 → 跑完即删 → Lint(顺序不可换)。停步全文只写 `cursor-rule.mdc` 并复制到 `<project>/.cursor/rules/<slug>.mdc`(`alwaysApply: true`)。SKILL.md 只留指针,不抄全文,不另开「强制工作流」或「多轮一对一问答」。
2. **一次一问**:每次仅问一个问题,得到确认才记录
3. **必扫工程**:动手前先扫当前工程目录、技术栈、代码落点、TDD 环境
4. **业务分支梳理**(条件触发,见第 2 节阈值):
   > 每次实现功能改造或者新功能,需要梳理原业务分支,并列出改动影响,询问确认后再往下执行
   > (每次仅问一个问题),最终达成共识才执行。
5. **业务落点变动触发提示词补充**(总是注入,与上一条并列):
   > 如果业务逻辑或工程结构代码发生变动导致代码落点变化,提示词缺失或者需要补充,
   > 也需要询问用户是否补充调整。

---

## 8. 跑完即删

生成完成、自检通过后:

- 删除 `local-tests/generate-front-business-skills.test.md`(若有)
- 删除临时工作记忆(最终 SKILL.md 只留闸门指针、何时使用、本栈命令与落点)
- 提示用户:`/generate-front-business-skills` 一次流程结束,产出在 `<path>`

---

## 9. Lint 闸门(生成的 SKILL.md 必须通过)

```
Skill Lint:
- [ ] frontmatter 含 name + description,description 行长 ≤ 1024
- [ ] 顶层标题 # 与 frontmatter name 对应(可读形式)
- [ ] 含"何时使用"与"本栈"(只含命中的那一栈,没有 12 栈全表)
- [ ] 一级标题后第一节是「运行时执行协议」,含 `当前闸门: N/5` 与禁止写文件,且不重复常驻规则全文
- [ ] 没有「强制工作流」索引,也没有与闸门平行的「多轮一对一问答」
- [ ] 技能源含 `cursor-rule.mdc`,项目落盘含 `.cursor/rules/<slug>.mdc`,`alwaysApply: true`
- [ ] 常驻规则含 5 步顺序、四行共识、确认前禁止写文件、先失败测试、删除 `local-tests/`、lint 0 error、禁止猜字段
- [ ] 默认未改 Q10 时,没有 Claude Code 目录,也没有 sync 脚本
- [ ] 若工程命中"大量既有业务",必含"业务分支梳理与改动影响确认"小节且原文一致
- [ ] 必含"业务落点变动触发提示词补充"小节且原文一致(总是注入)
- [ ] 环境正常时无 TDD/Lint/类型检查状态大段;用户选 (B)/(C) 时各一行
- [ ] Markdown 标题层级连续(不跳级)
- [ ] SKILL.md 宜 ≤ 80 行
- [ ] 若用户选了 ≥2 个编辑器且策略 = A/D,才含 scripts/sync.ps1 + scripts/sync.sh
- [ ] 若选了 Codex,SKILL.md 含 TODO,且不擅自生成 Codex 目录
```

未通过 → 回到第 3 节相关问题重答或重写。

---

## 10. 反模式(不要这样做)

- ❌ 一次问多个问题 / 把多个问题塞进一个 `AskUserQuestion` 的多条
- ❌ 用户未确认就把答案写入最终 SKILL.md
- ❌ 跳过工程扫描直接生成
- ❌ 5 步闸门漏掉任意一步 / 改顺序
- ❌ 5 步闸门只写成清单,或在 SKILL.md 与常驻规则里各抄一遍长文
- ❌ 默认同时生成 Claude Code 目录或 sync 脚本(用户没改 Q10 就只写 Cursor)
- ❌ 把 12 栈全表、安装教程、第二套问答流程写进产物
- ❌ 只写 `.cursor/skills/`,不写 `alwaysApply: true` 的 `.cursor/rules/<slug>.mdc`
- ❌ 工程明明命中"大量既有业务",却不注入业务分支梳理规则
- ❌ 把生成过程产生的中间文件、测试文件留在产物目录里
- ❌ 覆盖 `.claude/skills/` / `.cursor/skills/` / `.codex/skills/` 下已有的同名 skill 而不告知用户
- ❌ 检测到工程未具备 TDD 环境却擅自安装测试框架 / 擅自降级闸门(必须先问)
- ❌ 检测到工程未具备 Lint 环境却擅自安装 Lint 工具 / 擅自降级闸门(必须先问)
- ❌ TypeScript 工程检测到缺 typescript 时擅自安装 / 擅自降级类型检查闸门(必须先问)
- ❌ 工程结构 / 业务逻辑发生变动时,直接修改落点而不询问用户是否补充提示词
- ❌ 目标编辑器 / 工具落点未确认前写入文件(必须先答 Q-Editor + Q-Editor-Path)
- ❌ **跳过 Q10 目标编辑器**(必答题,不能跳过;否则产物没有"默认触发"的落点)
- ❌ 产物只放桌面 / 用户级,**不放项目级** skills 目录(违反"默认触发"原则)
- ❌ **生成需要 `/<slug>` 命令才能触发的 skill**(违反"只要用 AI 编码就默认触发"的硬要求)
- ❌ `description` 写得**过窄**(导致默认触发失效,匹配不到编码意图)
- ❌ 多处复用但内容不一致(各编辑器目录的 SKILL.md 必须字节级一致,或走 wrapper / 软链接)
- ❌ 选了 Codex 但未确认其具体目录格式就硬套 Claude Code 路径
- ❌ Q1 让用户重新发明"做什么"(本生成器的原始目标已固定,Q1 只问"沿用 / 自定义",选沿用即跳过)

---

## 11. 编辑器 / 工具适配总表

> 不同编辑器对 skill 的目录、frontmatter、默认触发机制不一样。
> 完整适配矩阵见 [references/editor-adapters.md](references/editor-adapters.md)。

| 编辑器 / 工具 | 用户级目录 | 项目级目录 | frontmatter | **默认触发**(只要 AI 编码就生效) | 备注 |
|---|---|---|---|---|---|
| **Claude Code** | `~/.claude/skills/<slug>/` | `<project>/.claude/skills/<slug>/` | `name` + `description`(推荐 `>-` 多行) | **项目级 skills 目录自动扫描 + `description` 意图匹配** —— 不需要 `/<slug>` 命令,只要编码场景触发 | 已验证支持 |
| **Cursor** | `~/.cursor/skills/<slug>/` | `<project>/.cursor/skills/<slug>/` **并且** `<project>/.cursor/rules/<slug>.mdc`(`alwaysApply: true`) | 同上 | **`.cursor/rules/<slug>.mdc` 每轮注入闸门**;skills 目录提供完整协议 | 已验证支持(本生成器即活体样例) |
| **Codex** | TODO | TODO | TODO | TODO(默认触发机制以该工具文档为准) | 生成器先用 placeholder,在用户告知具体格式后回填 |
| **其他** | 由用户告知 | 由用户告知 | 由用户告知 | 由用户告知 | 用户提供具体目录与格式后生成器回填 |

> ⚠ **默认只生成 Cursor。** Claude Code / Codex 仅当用户在 Q10 改选时才写对应目录。
> Cursor 停步闸门写 `<project>/.cursor/rules/<slug>.mdc`(`alwaysApply: true`);`SKILL.md` 只留短指针和本栈命令。只放 skills 目录时闸门不会每轮执行。

### 11.1 多处复用的文件统一性

选 ≥2 个编辑器时,生成器**必须**按 Q-Editor-Sync 答案处理:

| 策略 | 文件统一性如何保证 | 适用场景 |
|---|---|---|
| **A 一源多端部署**(推荐) | 主源写一次,`scripts/sync.ps1` + `scripts/sync.sh` 复制到所有编辑器目录;每次改主源后跑一次 sync | 想保持源清晰,跨机器同步方便 |
| **B 软链接 / junction** | 主源放一处,各编辑器目录 `mklink /J`(Windows)/ `ln -s`(Linux/macOS)指向主源;零复制 | 同机器多 IDE 同时用 |
| **C 每端独立副本** | 每个编辑器一份,各自维护;生成器不保证一致 | 编辑器之间差异大,确实需要分支 |
| **D 主源 + 每端 wrapper** | 主源放完整 SKILL.md,各编辑器目录只放极简 frontmatter + 链接指回主源 | 想避免重复又不想用软链接 |

> **推荐 A**,因为:1) 编辑器目录是 IDE 自动扫描的,放完整 SKILL.md 最稳;2) 同步脚本可放进 git;3) 与"一源多端"的 CI 思路一致。

### 11.2 同步脚本占位(策略 A/D 时生成)

**scripts/sync.ps1**(Windows PowerShell):

```powershell
# 用法:.\scripts\sync.ps1 [-DryRun]
$ErrorActionPreference = "Stop"
$src = Split-Path -Parent $PSScriptRoot
$sources = @{
  "Claude Code" = Join-Path $env:USERPROFILE ".claude\skills\<slug>"
  "Cursor"      = Join-Path $env:USERPROFILE ".cursor\skills\<slug>"
  # Codex / 其他:由用户告知具体路径后追加
}
foreach ($kv in $sources.GetEnumerator()) {
  $dst = $kv.Value
  if (-not (Test-Path $dst)) { New-Item -ItemType Directory -Force -Path $dst | Out-Null }
  Write-Host "[$($kv.Key)] $src -> $dst" -ForegroundColor Cyan
  if (-not $DryRun) {
    Copy-Item -Recurse -Force "$src\*" $dst
  }
}
# Cursor 常驻闸门:必须写到工程的 .cursor/rules,skills 目录不会每轮注入。
# 在工程根执行本脚本。
$ruleSrc = Join-Path $src "cursor-rule.mdc"
if (Test-Path $ruleSrc) {
  $ruleDstDir = Join-Path (Get-Location) ".cursor\rules"
  $ruleDst = Join-Path $ruleDstDir "<slug>.mdc"
  Write-Host "[Cursor rules] $ruleSrc -> $ruleDst" -ForegroundColor Cyan
  if (-not $DryRun) {
    if (-not (Test-Path $ruleDstDir)) { New-Item -ItemType Directory -Force -Path $ruleDstDir | Out-Null }
    Copy-Item -Force $ruleSrc $ruleDst
  }
}
```

**scripts/sync.sh**(Bash):

```bash
#!/usr/bin/env bash
set -euo pipefail
SRC="$(cd "$(dirname "$0")/.." && pwd)"
SLUG="<slug>"
declare -A DST=(
  ["Claude Code"]="$HOME/.claude/skills/$SLUG"
  ["Cursor"]="$HOME/.cursor/skills/$SLUG"
  # Codex / 其他:由用户告知具体路径后追加
)
for key in "${!DST[@]}"; do
  dst="${DST[$key]}"
  mkdir -p "$dst"
  echo "[$key] $SRC -> $dst"
  rsync -a --delete "$SRC/" "$dst/"
done
# Cursor 常驻闸门:在工程根执行。skills 目录不会每轮注入。
if [[ -f "$SRC/cursor-rule.mdc" ]]; then
  mkdir -p ".cursor/rules"
  echo "[Cursor rules] $SRC/cursor-rule.mdc -> .cursor/rules/$SLUG.mdc"
  cp -f "$SRC/cursor-rule.mdc" ".cursor/rules/$SLUG.mdc"
fi
```

---
