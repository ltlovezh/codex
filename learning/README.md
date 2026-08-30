# 学习记录

这里存放我阅读 / 折腾 Codex 仓库过程中的个人学习记录。

## 分支设计

```text
openai/codex (upstream)
      │  fetch（只拉不推，push 地址已设为 no_push）
      ▼
    main  ──push──►  origin/main     纯镜像，永远不在这里提交
      │  rebase 基线
      ▼
    diy   ──push──►  origin/diy      main + learning/，只有这个目录是我加的
```

| 分支 | 跟踪 | 用途 | 规则 |
| --- | --- | --- | --- |
| `main` | `origin/main` | 同步 `openai/codex` 的原始代码 | 只做快进，不手写提交 |
| `diy` | `origin/diy` | 在 `main` 之上叠加学习记录 | 改动只写在 `learning/` 里 |

三个远端：

- `origin` = `git@github.com:ltlovezh/codex.git`（我的 fork，`main` / `diy` 都推这里）
- `upstream` = `git@github.com:openai/codex.git`（原始仓库，push 地址已禁用，防止误推）

目前 `diy` 的改动都收在 `learning/` 一个目录里，上游不碰这个路径，所以 rebase 到新的 `main` 上不会冲突。

但这是自律约定而非结构保证：一旦开始直接改上游代码（学习时加打印、插桩都很正常），那些文件在 rebase 时就可能冲突。查当前偏离：`git diff --name-only main..diy | grep -v '^learning/'`，输出为空就是还守着约定。

## 同步上游

一条命令走完「拉上游 → 快进 main → 推 origin/main → 把 diy rebase 到新 main → 推 origin/diy」：

```bash
./learning/sync.sh
```

需要在 `diy` 分支上运行——`learning/` 只存在于 `diy`，切到 `main` 后这个文件在工作区里就没了。想在任何分支上跑：

```bash
git show diy:learning/sync.sh | bash
```

如果 `main` 上不小心提交过东西导致无法快进，脚本会报错、切回原分支并退出，不会硬来。

手动等价操作：

```bash
git fetch upstream
git switch main && git merge --ff-only upstream/main && git push origin main
git switch diy && git rebase main && git push --force-with-lease origin diy
```

`diy` rebase 后历史会被改写，所以推送需要 `--force-with-lease`（远端有意外变动时它会拒绝推送，比 `--force` 安全）。

## 写笔记的约定

- 一篇笔记一个文件，命名为 `YYYY-MM-DD-主题.md`，例如 `2026-08-30-codex-rs-启动流程.md`。
- 引用代码写成 `路径:行号`（如 `codex-rs/core/src/lib.rs:42`），方便点击跳转。
- 写完在下面的索引里补一行。

## 索引

| 日期 | 主题 | 笔记 |
| --- | --- | --- |
| - | 暂无 | - |
