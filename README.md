# git-tombstone

> 死路账（Dead-ends-only memory）——给 AI 编码 agent 的极简记忆系统。
> 记忆 = 代码 + git + 一页 DEADENDS。

## 为什么

市面上的 agent 记忆几乎全是 activity log（记干了什么）：claude-mem 93k★、mem0 64k★，卷成红海。真正贵的却是 evidence log：哪条路试过、为什么死、证据在哪——没人做。

一个真实案例：97 条事件的记忆账本，几个月后审计发现——事实层精确（行号级引用全部命中），但 replaces 闭环断裂、零 commit 锚点、日期笔误，读账本的 AI 分不清新旧、把过期当现行。**账本不是烂在幻觉，是烂在没人维护的机械细节。**

## 体系：三道锁

真相源 = 代码 + git。只记代码读不出的两样：死掉的路（证伪）、放弃的决策（没做）。活路不记——代码自己会说话。

| 锁 | 机制 | 防什么 |
|---|---|---|
| 自动加载 | 入口文件必须叫 `AGENTS.md`（复数！OpenCode/Codex/Cursor 等自动读取） | 规矩靠自觉 |
| 日期门禁 | pre-commit 钩子：DEADENDS 新增行日期 ≠ 当天/昨天 → 拒绝提交 | AI 凭直觉写日期（必错） |
| 同窗记账 | 规约：回退/放弃的动作与写死路账在同一轮完成 | 死路无痕、重蹈覆辙 |

## 快速开始

1. 拷 `AGENTS.template.md` → 项目根，改名 `AGENTS.md`，按项目改地图
2. 拷 `DEADENDS.template.md` → `docs/DEADENDS.md`
3. 拷 `pre-commit` → `.git/hooks/pre-commit`
4. 有旧记忆系统要退休？看 [MIGRATION.md](MIGRATION.md)

## 墓碑双通道

- 代码回退：commit message 以 `revert: 方案 → 死因` 开头，`git log --grep="revert:"` 即死路索引
- 无 commit 的死（多轮论证后放弃 / 工作区回退 / 纯对话）：DEADENDS.md 一行：
  `- [日期] ✗ 方案 → 死因 ｜证据: 文件:行号 ｜置信: 真机验证/代码审计/调研推断/仅对话`
  行内日期可省，权威日期 = `git blame`

## 反蔓延设计

- 只收"死"；未验证候选不入账（防变成第二个胖账本）
- 超过 60 行触发归档，git 历史永远找得回
- 灰色地带只准写成"问题"，不准写成"结论"

## 已知局限

- 单模型的系统性盲区防不了（模型天花板，不是账本问题）
- pre-commit 钩子重新克隆后需重装
- 日期门禁容忍 ±1 天（跨午夜）

## License

MIT
