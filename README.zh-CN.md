# Spec Review Layout

[English](README.md) | 简体中文 | [日本語](README.ja.md)

[Spec Kit](https://github.com/github/spec-kit) 的 preset，调整 `spec.md` 的版式，便于人工 review。它在 spec 里加入由 Mermaid 图组成的总览图，把验收场景、Edge Cases 和 Key Entities 改成表，每条功能需求写成一行，细则写成带标签的子项目和条件表。规格要写的内容不变。

## spec.md 的变化

| 节 | 写法 |
|---|---|
| Overview Map（新增，位于 User Scenarios & Testing 之前） | Screen flow、Data sources、Interactions、Lifecycle 四种图，只保留适合该功能的，至少一张。图里只写名称和编号（`US1`、`FR-012`）。Lifecycle 图的状态与状态 × 操作表一致，起点和终点 `[*]` 可以画。功能有画面时加 Screen layout 表 |
| 验收场景 | `# \| Given \| When \| Then \| Refs` 的表。一行一个场景。`When` 只写一个操作或一个事件（如断网）。`Then` 中每个 `•` 是一个检查点 |
| Edge Cases | `Case \| Outcome \| Refs` 的表 |
| Functional Requirements | 每条 FR 保留 `- **FR-###**:` 的行首格式，细则写成 `**Label**:` 子项目。条件组合表和状态 × 操作表放在 FR 正下方、子项目之前。状态表的格子写 `→ 状态`，用户操作被禁止时写 `— (not allowed)`，事件不可能发生时写 `— (cannot occur)`。引用某条子项目时写 `FR-012 (Sort)` |
| Key Entities | `Entity \| One record is \| Attributes \| Used by` 的表 |

删除的场景和 FR 保留编号，写 `(removed YYYY-MM-DD: reason)`。

内容使用用户要求的语言。标题、标签、列名、标记、图标题等固定文字保持模板中的英文。模板中方括号里的文字是内容的占位。

## 内容

| 文件 | 类型 | 策略 | 作用 |
|---|---|---|---|
| `templates/spec-template.md` | template | replace | 以 Spec Kit v1.4.2 的 spec 模板为底，加入 Overview Map、表格式场景和 FR 子项目 |
| `commands/speckit.specify.md` | command | append | 写 `spec.md` 前读取版式规则。模板中也写有同一份规则的位置 |
| `commands/speckit.clarify.md` | command | append | clarify 写回回答时保持版式，例如追加表的行，不追加列表项 |
| `rules/spec-layout.md` | 规则 | — | 版式规则，两个命令都读取 |

版式规则放在 `rules/spec-layout.md`，没有放在模板注释里。`spec.md` 写完后注释会被删除，只写在注释里的规则 `/speckit.clarify` 读不到。

## 适用场景

- spec 需要人工 review，例如团队评审或 pull request
- 功能有画面、状态变化或较多的条件组合

以下情况不适用：

- 使用按 Given/When/Then 句子解析的扩展，例如 `bdd`、`reqnroll-bdd`、`test-first-governance`
- 希望 `spec-template` 的上游修改自动生效。本 preset 整份替换模板

## 前置条件

- Spec Kit CLI 1.0.0 以上，在 1.0.6 和 1.4.2 上测试过
- 已执行 `specify init` 的项目
- 在 Claude Code integration（skills 模式）上测试过。generic integration 不注册 preset 的命令覆盖，只有模板生效
- 不依赖其他扩展

## 安装

在项目根目录执行。

```bash
specify preset add --from https://github.com/ryukirin/spec-kit-preset-review-layout/archive/refs/tags/v0.6.0.zip
```

从本地目录安装：

```bash
specify preset add --dev <本目录的路径>
```

## 与其他 preset 叠加

本 preset 整份替换 `spec-template`，并向 `speckit.specify` 和 `speckit.clarify` 追加内容。Spec Kit 对每个模板和命令分别解析。priority 数字越小优先级越高，priority 相同时按 preset ID 的字母顺序排列。`replace` 层会遮住它下面的所有层，`append` 和 `wrap` 层可以叠加。

两边都使用默认 priority（10）时，结果如下。例子取自 2026 年 11 月的社区目录。

| 对方 preset | 例子 | 结果 |
|---|---|---|
| 替换 `spec-template`，排在前面 | `mde`、`screenwriting`、`sicario-core` | 本 preset 的模板不生效 |
| 替换 `spec-template`，排在后面 | `toc-navigation` | 对方的模板不生效 |
| 向 `spec-template` 追加或包裹，排在前面 | `a11y-governance`、`okr-bet`、`security-governance` | 对方的内容加到本 preset 的模板上，两边都生效 |
| 向 `spec-template` 追加或包裹，排在后面 | `specassay`、`test-first-governance`、`workflow-preset` | 对方的内容被丢掉 |
| 替换 `speckit.specify`，排在前面 | `canon-core`、`command-density` | 本 preset 给 specify 追加的说明被丢掉。模板中仍写有版式规则的位置，只要对方的命令使用 spec 模板，版式仍然生效 |
| 替换 `speckit.clarify`，排在前面 | `canon-core`、`claude-ask-questions`、`command-density` | 本 preset 给 clarify 追加的规则被丢掉，clarify 写回回答时不再保持版式 |

其他向 `speckit.specify` 或 `speckit.clarify` 追加或包裹内容的 preset，不论顺序都能和本 preset 叠加。

要调整顺序，在安装时用 `--priority` 指定。`specify preset set-priority` 不会更新已经写入的命令和 skill 文件（github/spec-kit#5087），所以要先卸载，再用新的 priority 重新安装。两个 preset 替换同一个模板或命令时，不论顺序如何，只有一个生效。

不支持内置的 `lean` preset。它的 specify 命令不使用 spec 模板。

## 确认安装结果

```bash
specify preset resolve spec-template
```

输出末尾为 `(top layer from: spec-review-layout v0.6.0)`。使用 Claude Code 时，`.claude/skills/speckit-specify/SKILL.md` 和 `.claude/skills/speckit-clarify/SKILL.md` 的末尾有 `## Review Layout (spec-review-layout preset)` 一节。

## 使用方法

照常执行 `/speckit.specify` 和 `/speckit.clarify`（Claude Code 中为 `/speckit-specify` 和 `/speckit-clarify`）。执行 `/speckit.specify` 后，`checklists/requirements.md` 中有列出版式检查项的 Review Layout 一节。

## 已知限制

- 模板整份替换。Spec Kit 修改 `spec-template` 的其他节时需要手动合并。上游计划提供按节替换的策略（github/spec-kit#5421）。
- 项目中存在 `.specify/templates/overrides/spec-template.md` 时，本 preset 的模板不生效（github/spec-kit#5337）。
- 命令覆盖在安装时写入 agent 的 command 或 skill 文件。`specify preset disable` 不会删除这些内容，需要用 `specify preset remove`。
- Windows 上 `python3` 是 Microsoft Store 的应用执行别名时，Spec Kit 的 `resolve-template.ps1` 会选中它，执行失败并报 `Invalid preset manifest`。安装任何 preset 都会出现。从 Spec Kit 1.4 起，解析失败时 `/speckit.specify` 会停止。在“设置 > 应用 > 高级应用设置 > 应用执行别名”中关闭 `python3` 后恢复正常。

## 卸载

```bash
specify preset remove spec-review-layout
```

卸载后，spec 模板和两个命令恢复为下一层（其他 preset 或 Spec Kit core）的版本。

## 许可证

MIT
