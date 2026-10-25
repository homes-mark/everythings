# Git 学习与操作记录

[← 返回目录](./README.md)

结合这个仓库的实际操作学习 Git：每次说明要完成什么、使用什么命令，以及命令执行后内容保存在哪里。后续操作继续按场景补充，新增文档同步维护 README 目录。

## 第一次把 GitHub 仓库下载到本地（2026-10-25）

当时本地还没有这个仓库，所以在 `D:\AAAJob\Github-relevant` 中克隆它：

```powershell
git clone git@github.com:homes-mark/everythings.git everythings
```

`git clone` 会下载远程仓库及其历史，并创建最后指定的 `everythings` 文件夹。克隆后，Git 默认把远程仓库命名为 `origin`。

本次实际运行时加了临时 SSH 参数：

```powershell
git -c core.sshCommand='ssh -o BatchMode=yes -o ConnectTimeout=15' clone git@github.com:homes-mark/everythings.git everythings
```

`-c` 只影响这次命令；`BatchMode=yes` 使用已有认证，避免等待密码输入；`ConnectTimeout=15` 设置 SSH 连接超时为 15 秒。首次执行受到运行环境限制，重试后克隆成功。

已有本地仓库后，获取并整合远程更新通常使用 `git pull`，不需要重新克隆。本次没有执行 `git pull`。

## 修改本地文档，然后上传到 GitHub（2026-10-26）

本次要上传的是 `README.md`、`cron.md` 和这份 `git-learning.md`。下面的 Git 命令都在仓库根目录 `D:\AAAJob\Github-relevant\everythings` 中执行。

保存 Markdown 文件只是修改了本地工作区。要让 GitHub 出现这些内容，需要依次完成：**暂存 → 提交 → 推送**。

### 1. 用 git add 暂存修改

```powershell
git add .
```

`git add` 将修改加入暂存区，决定下一次提交要包含什么。`.` 表示当前目录及其子目录；本次在仓库根目录执行，因此会暂存其中未被忽略的新增、修改和删除内容。本次检查确认待提交的就是上面的三个文档。

暂存区可以理解为“准备放进下一次存档的内容”。这一步还没有创建提交，也没有上传到 GitHub。如果暂存后又修改了文件，需要再次 `git add` 才能把后续修改也放进去。

### 2. 用 git commit 保存一次本地提交

```powershell
git commit -m "docs: add cron and git learning notes"
```

`git commit` 将暂存区的内容保存为一次本地提交，形成一条有编号、可以追溯的历史记录。`-m` 后面的文字是这次提交的说明；这里表示“添加 Cron 和 Git 学习笔记”。

此时修改已经进入本地 Git 历史，但还没有上传到 GitHub。

### 3. 用 git push 上传提交

```powershell
git push origin main
```

`git push` 上传本地提交；`origin` 是克隆时配置的远程仓库名称；`main` 是本次要推送的本地分支。这里将本地 `main` 分支的提交推送到 GitHub 对应分支。

推送成功后，GitHub 上就能看到三个文档，并通过 README 的相对链接打开对应笔记。`git push` 上传的是提交，因此只保存文件、没有提交的修改不会被它上传。

## 本次操作中配合使用的检查命令

这些命令帮助确认提交范围和结果，主流程仍然是上面的 `add → commit → push`。

| 使用时机 | 命令 | 在本次操作中的作用 |
| --- | --- | --- |
| 暂存前、推送后 | `git status --short --branch` | 查看文件状态、当前分支及跟踪分支信息 |
| 推送前 | `git remote -v` | 核对 `origin` 的获取和推送地址 |
| 修改 README 后 | `git diff -- README.md` | 查看 README 尚未暂存的修改，`--` 后面指定文件路径 |
| 暂存前 | `git diff --check` | 检查尚未暂存的差异中的空白问题，不包含未跟踪文件 |
| 暂存后 | `git diff --cached` | 查看暂存区相对于上次提交的差异，确认即将提交的内容 |
| 暂存后 | `git diff --cached --check` | 检查暂存区差异中的行尾空格等空白问题 |

`git status --short --branch` 中，`--short` 使用简洁格式，`--branch` 显示分支信息。文件状态的第一列表示暂存区，第二列表示工作区：

- ` M README.md`：README 已修改，还没有暂存。
- `M  README.md`：README 的修改已经暂存。
- `?? cron.md`：新文件还没有被 Git 跟踪。
- `A  cron.md`：新文件已经加入暂存区。
- `[ahead 1]`：相对于本地记录的远程分支，本地多一个提交；提交后、推送前可能看到它。

推送成功后，如果输出只有 `## main...origin/main`，没有文件状态行，也没有 ahead/behind 提示，就表示工作区干净，且本地分支与本地记录的远程分支一致。

Markdown 链接还通过文件检查确认目标存在，这一步不是 Git 命令。
