# Editor Adapters — 编辑器 / AI 工具适配库

> 双用途:
> 1. **给 generate-front-business-skills 自己用** —— 主 SKILL.md §11 总表的展开版,扫描与生成时按本表逐项命中。
> 2. **给被生成的 skill 引用** —— 任何一个被生成的 skill,若需"按目标编辑器分支",
>    应把相关章节复制/链接到自己的 `references/editor-matrix.md`。
>
> **铁律**:每个编辑器都按统一字段写,保证生成器读到任意一种都能产出可复用 skill。
>
> **本节被多端复用**:Claude Code / Cursor 已实测可用;Codex / 其他由用户告知后回填。

---

## 默认触发机制(必读)

> ⚠ **生成器默认只产出 Cursor** 两份文件:`.cursor/skills/<slug>/SKILL.md` 与 `.cursor/rules/<slug>.mdc`。
> 用户在 Q10 改选后,才按下面的表追加 Claude Code / Codex / 其他。
> 停步条文放 rules 文件(`alwaysApply: true`),SKILL.md 不重复抄全文。

不同编辑器实现"默认触发"的具体路径:

| 编辑器 | 默认触发的实现方式 | 触发效果 |
|---|---|---|
| **Claude Code** | 放 `<project>/.claude/skills/<slug>/`(项目级)或 `~/.claude/skills/<slug>/`(用户级);`description` 写得宽泛覆盖"编码场景" | Claude Code 启动时自动扫描 skills 目录;编码意图匹配 `description` 时自动加载,无需命令 |
| **Cursor** | `<project>/.cursor/skills/<slug>/` 放完整协议,**并且** `<project>/.cursor/rules/<slug>.mdc` 设 `alwaysApply: true` | rules 每轮注入闸门停步;只放 skills 目录时正文不会每轮进入上下文,闸门不会执行 |
| **Codex** | 按工具文档(待用户告知) | TODO |
| **Aider** | 放 `<project>/.aider/conventions.md`,CLI 启动时 `--read-conventions` 加载 | Aider 启动自动遵循 |
| **Continue** | 放 `<project>/.continue/prompts/<slug>.prompt` | IDE 输入框 `/<slug>` 触发;**不默认触发** |
| **Cline / Roo Code** | 放 `<project>/.clinerules` 单文件 | IDE 菜单加载;**不默认触发** |
| **Zed** | 放 `<project>/.zed/prompts/<slug>.md` | IDE 输入框 `/<slug>`;**不默认触发** |

### ⚠ 默认触发 vs 命令触发的关键差异

| 维度 | 默认触发(本生成器默认) | 命令触发(可选补强) |
|---|---|---|
| **触发条件** | AI 工具启动 + 编码场景意图匹配 | 用户主动输 `/<slug>` |
| **用户体验** | 无感,自动化 | 需要记住命令 |
| **适用** | 编码规范 / 流程固化 / 最佳实践 | 偶尔触发的特定任务 |
| **风险** | description 写得宽泛会触发过多;description 写得窄会触发不到 | 不会被无意触发 |
| **推荐** | ✅ 推荐(本生成器产物默认) | 仅作补强(如 `/generate-front-business-skills` 本身就是命令触发) |

### 给"被生成的 skill"配默认触发的 checklist

- [ ] 落点是**项目级** skills 目录(IDE 按 description 加载完整协议)
- [ ] 目标含 Cursor 时,另有 `<project>/.cursor/rules/<slug>.mdc`,且 `alwaysApply: true`
- [ ] `description` 写得**足够宽泛**:覆盖"AI 工具执行编码"+"该 skill 的核心场景",且行长 ≤ 1024
- [ ] **不**在 description 里写"必须用 `/<slug>` 命令"(那样就降级成命令触发,违反默认触发原则)
- [ ] 主源放桌面 / 用户级,产物用 sync 脚本 / 直接写入落到项目级 skills 目录
- [ ] 若多编辑器复用,确保各编辑器的项目级目录都已同步(scripts/sync.ps1 / sync.sh)

---

---

## 目录约定总表

| 编辑器 | 用户级路径 | 项目级路径 | frontmatter 字段 | 触发命令 | 备注 |
|---|---|---|---|---|---|
| **Claude Code** | `~/.claude/skills/<slug>/` | `<project>/.claude/skills/<slug>/` | `name` + `description` | `/<slug>` | 已实测 |
| **Cursor**(默认) | `~/.cursor/skills/<slug>/` | `<project>/.cursor/skills/<slug>/` + `<project>/.cursor/rules/<slug>.mdc` | `name` + `description`;规则文件另含 `alwaysApply: true` | 规则每轮注入停步;技能按需加载本栈命令 | 已实测,生成器默认只出这一端 |
| **Codex** | TODO | TODO | TODO | TODO | 由用户告知具体路径与格式 |
| **Aider** | `~/.aider/conventions.md`(单文件) | `<project>/.aider/conventions.md` | YAML front-matter + Markdown | 不支持 slash command,通过 ` aider --read-conventions` | 单文件而非目录式 |
| **Continue** | `~/.continue/prompts/`(单文件) | `<project>/.continue/prompts/<slug>.prompt` | `name` + `description` + `prompt` | `/<slug>`(在 IDE 输入框) | 文件级而非目录式 |
| **Cline / Roo Code** | 不固定;通过自定义指令 | `<project>/.clinerules/`(单文件) | 无 frontmatter;Markdown | 通过 IDE 菜单触发 | 单规则文件 |
| **Zed** | `~/.config/zed/prompts/` | `.zed/prompts/` | 无 frontmatter | `/<slug>`(在 IDE 输入框) | 文件级 |

> **注意**:本表里除 Claude Code / Cursor 之外均为通用规范推断,具体路径与字段以工具最新文档为准。
> 用户实际选用某编辑器时,生成器必须问清具体目录与格式。

---

## 1. Claude Code

### 1.1 识别特征

- 用户级目录:`C:\Users\<user>\.claude\skills\`(Windows)/ `~/.claude/skills/`(Linux/macOS)
- 项目级目录:`<project>/.claude/skills/`
- 扫描方式:Claude Code 启动时自动扫描这两处
- 文件命名:`SKILL.md`(必含)

### 1.2 frontmatter 规范

```yaml
---
name: <slug-kebab-case>      # 必填,作为 /<slug> 命令与目录名
description: >-              # 推荐多行,描述触发场景与产出
  <一句话说明触发场景与产出。>
  <多行补充细节。>
---
```

- **name**:必填,kebab-case,作为 `/<slug>` 命令与目录名
- **description**:必填,**最长 1024 字符**,多行推荐用 `>-` 折叠
- 其它字段(`when_to_use`、`metadata` 等):可选,Claude Code 会忽略未知字段

### 1.3 触发机制

- 用户输入 `/<slug>` → 加载 `<目录>/SKILL.md`
- Claude Code 根据 `description` 自动判断是否触发(用于意图匹配)

### 1.4 多端部署

- 个人全局:`~/.claude/skills/<slug>/`
- 项目内(推荐,团队共享):`<project>/.claude/skills/<slug>/`
- 同时存在时,项目级覆盖用户级

### 1.5 已实测样例

```
C:\Users\37313\.claude\skills\generate-front-business-skills\
├── SKILL.md
├── README.md
├── references/
├── examples/
```

---

## 2. Cursor

### 2.1 识别特征

- 用户级目录:`C:\Users\<user>\.cursor\skills\`(Windows)/ `~/.cursor/skills/`(Linux/macOS)
- 项目级目录:`<project>/.cursor/skills/` 或 `<project>/.cursor/rules/`(旧格式)
- 文件命名:`SKILL.md`
- Cursor **0.49+** 已支持与 Claude Code 同格式的 skills 目录(自动同步)

### 2.2 frontmatter 规范

```yaml
---
name: <slug-kebab-case>
description: >-
  <触发场景与产出>
---
```

> **与 Claude Code 兼容**:目前实测同一份 `SKILL.md` 可被两边共同识别。
> 因此**多处复用时,推荐共用 frontmatter**。

### 2.3 触发机制

- `/<slug>`(在 Cursor Composer 输入框)加载 `SKILL.md`
- `description` 自动匹配后,模型按需阅读 `SKILL.md`
- **5 步闸门的停步不靠 skills 目录。** 必须另写 `<project>/.cursor/rules/<slug>.mdc`,frontmatter 含 `alwaysApply: true`。该文件每轮进入上下文,要求先声明 `当前闸门: N/5`,确认前禁止写文件。正文与技能源里的 `cursor-rule.mdc` 一致,模板见 [workflow-gates.md](workflow-gates.md)「Cursor 常驻规则」。

### 2.4 多端部署

- 个人全局:`~/.cursor/skills/<slug>/`(完整协议;用户级 skills 仍不会每轮注入闸门)
- 项目内技能:`<project>/.cursor/skills/<slug>/`
- 项目内常驻闸门(必写):`<project>/.cursor/rules/<slug>.mdc`

### 2.5 已实测样例

```
C:\Users\37313\.cursor\skills\generate-front-business-skills\
├── SKILL.md
├── README.md
├── references/
├── examples/
```

---

## 3. Codex(占位,需用户告知具体格式)

### 3.1 状态

> ⚠ **本节为占位**。Codex(OpenAI Codex CLI 或 GitHub Copilot 中的 Codex Agent)
> 的 skill / rules 目录与 frontmatter 规范,生成器**未实测确认**。
> 用户实际选用 Codex 时,必须先告知:
> 1. 具体目录路径(用户级 / 项目级)
> 2. 文件命名(`SKILL.md` / `*.md` / `*.mdc` / 其他)
> 3. frontmatter 字段规范
> 4. 触发命令(若有)
>
> 用户告知后,生成器按对应规范生成 SKILL.md,并在 SKILL.md 顶部留一行:
> ```
> > ⚠ Codex 适配信息由用户告知生成,后续 Codex 规范变更时需手动同步。
> ```

### 3.2 临时处理

- 不知道 Codex 规范时,**不要**擅自套用 Claude Code / Cursor 路径与格式
- 在 SKILL.md 的"目标编辑器 / 工具"小节里写明 TODO
- 在生成器的 §11 编辑器适配总表里,把 Codex 行标 TODO 并附占位

---

## 4. Aider / Continue / Cline / Zed(简表)

> 这几个工具的 skill / rules 体系各异,本节只列通用规范,具体以工具文档为准。

### 4.1 Aider

- 单文件式:`~/.aider/conventions.md`(全局)+ `<project>/.aider/conventions.md`(项目)
- 无 frontmatter(纯 Markdown);或 YAML front-matter
- 触发:CLI 启动时 `--read-conventions <file>` 加载

### 4.2 Continue

- 目录式:`~/.continue/prompts/`(全局)+ `<project>/.continue/prompts/`(项目)
- 文件命名:`<slug>.prompt`(注意 `.prompt` 后缀)
- frontmatter:`name` + `description` + `prompt`
- 触发:IDE 输入框 `/<slug>`

### 4.3 Cline / Roo Code

- 单文件式:`<project>/.clinerules/`(项目根单文件)
- 无 frontmatter;纯 Markdown 规则
- 触发:IDE 菜单加载

### 4.4 Zed

- 目录式:`~/.config/zed/prompts/`(全局)+ `.zed/prompts/`(项目)
- 无 frontmatter;纯 Markdown
- 触发:IDE 输入框 `/<slug>`

---

## 5. 多处复用的文件统一性策略

> 配合主 SKILL.md §11.1 与 Q-Editor-Sync。

### 5.1 A 一源多端部署(推荐)

**核心思路**:主源 SKILL.md 写一次,同步脚本复制到各编辑器目录。

**结构**:

```
~/skills/<slug>/              ← 主源
├── SKILL.md
├── cursor-rule.mdc          ← 目标含 Cursor 时必有;sync 复制到 <project>/.cursor/rules/<slug>.mdc
├── README.md(可选)
├── references/
├── examples/
└── scripts/
    ├── sync.ps1             ← Windows 同步脚本
    └── sync.sh              ← Bash 同步脚本
```

**同步脚本职责**:

1. 把主源的所有文件(除 `scripts/` 外)复制到每个目标编辑器目录
2. 若存在 `cursor-rule.mdc`,在工程根再复制为 `<project>/.cursor/rules/<slug>.mdc`(`alwaysApply: true` 已写在文件内)
3. 可选 `-DryRun` 预览
4. 可选 git status 校验(若主源在 git 内)

**主源路径建议**:

- 用户级:`C:/Users/<user>/skills/<slug>/` 或 `~/skills/<slug>/`
- 项目级:`<project>/.skills-source/<slug>/`(隐藏在 `.skills-source/`,避免 IDE 误扫)
- 桌面:`~/Desktop/<slug>/`(本生成器默认)

**优点**:

- 主源唯一,IDE 各目录内容字节级一致
- 同步脚本可纳入 git,跨机器/跨人同步方便
- IDE 自动扫描各目录,无需额外配置

**缺点**:

- 每次改主源要跑一次 sync(可加 git hook 自动跑)
- 多机器同步仍需 push/pull

### 5.2 B 软链接 / junction

**核心思路**:主源放一处,各编辑器目录用符号链接(软链 / junction)指向主源。

**Windows(mklink)**:

```powershell
# junction(目录软链,无需管理员权限)
New-Item -ItemType Junction -Path "C:\Users\37313\.claude\skills\<slug>" -Target "C:\Users\37313\skills\<slug>"
New-Item -ItemType Junction -Path "C:\Users\37313\.cursor\skills\<slug>" -Target "C:\Users\37313\skills\<slug>"
```

**Linux/macOS(ln -s)**:

```bash
ln -s ~/skills/<slug> ~/.claude/skills/<slug>
ln -s ~/skills/<slug> ~/.cursor/skills/<slug>
```

**优点**:

- 零复制,实时同步
- 改主源即时生效

**缺点**:

- Windows 上某些工具可能不识别 junction(实测 Cursor / Claude Code 大多支持)
- 跨机器/跨人时,主源路径硬编码,需配文档说明

### 5.3 C 每端独立副本

**核心思路**:每个编辑器一份 SKILL.md,各自维护。

**适用场景**:

- 不同编辑器的 skill 真的有差异(frontmatter 字段、命令语法不同)
- 团队各编辑器使用习惯差异大

**实现**:生成器一次性写多份;后续编辑分别改;不保证一致。

### 5.4 D 主源 + 每端 wrapper

**核心思路**:主源放完整 SKILL.md;各编辑器目录只放极简 wrapper,链接指回主源。

**主源**:`~/skills/<slug>/SKILL.md`(完整)
**wrapper**(每个编辑器目录):

```markdown
<!-- 此 wrapper 由 <slug> 主源自动同步生成,不要直接编辑 -->
<!-- 主源:~/skills/<slug>/SKILL.md -->

@<主源相对路径>/SKILL.md
```

**优点**:不依赖软链接,也不复制内容;每次改主源只需重生成 wrapper

**缺点**:依赖编辑器支持 `@` include 语法(实测部分编辑器不支持)

---

## 6. 触发方式总表

| 编辑器 | 用户输入 | 触发动作 |
|---|---|---|
| Claude Code | `/<slug>` | 加载 SKILL.md |
| Claude Code | 自然语言意图 | 自动匹配 `description` |
| Cursor | `/<slug>` | 加载 SKILL.md |
| Cursor | 自然语言意图 | 自动匹配 `description` |
| Codex | TODO | TODO |
| Aider | CLI 启动参数 | `--read-conventions` |
| Continue | `/<slug>` | 加载 prompt 文件 |
| Cline | IDE 菜单 | 加载规则文件 |
| Zed | `/<slug>` | 加载 prompt 文件 |

---

## 7. 反模式(多端复用)

- ❌ 各编辑器目录的 SKILL.md 内容不一致(必须走 sync / 软链接 / wrapper)
- ❌ 软链接后忘了同步,或同步后忘了软链接(团队成员之间容易踩坑)
- ❌ 选了 Codex 但未告知具体格式就硬套 Claude Code 路径
- ❌ 在项目级 `.cursor/skills/` 里写个人习惯(团队成员会被覆盖)
- ❌ 同步脚本放进 git 但目标编辑器目录不在 `.gitignore`,导致主源与副本双份提交
- ❌ 改了 SKILL.md 但忘了跑同步脚本
- ❌ 目标含 Cursor 时只同步 `.cursor/skills/`,漏掉 `.cursor/rules/<slug>.mdc`(闸门不会每轮执行)
