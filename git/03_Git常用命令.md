# Git 开发规范与常用命令

## 1. 当前项目 Git 流程

当前项目采用 GitHub Flow：

- 远程仓库只有 `main` 作为长期主分支。
- `main` 是受保护分支，禁止直接推送。
- 所有功能、新需求、修复都从最新 `origin/main` 新建分支。
- 所有变更通过 Pull Request / Merge Request 合并回 `main`。
- 已经合并到 `main` 的历史开发分支默认不再复用。
- 每个新功能、新需求、新一轮修复，原则上都重新从最新 `origin/main` 创建新分支。
- 推送前需要确认当前分支已经同步最新 `origin/main`，并在本地解决完冲突。

## 2. 开始新需求的标准流程

```bash
# 1. 切回 main
git checkout main

# 2. 拉取远程最新 main
git fetch origin
git pull --ff-only origin main

# 3. 从最新 main 创建新分支
git checkout -b feat/your-feature-name
```

示例：

```bash
git checkout main
git fetch origin
git pull --ff-only origin main
git checkout -b feat/timer-trigger-name-routing
```

## 3. 分支命名建议

```bash
# 新功能
feat/xxx

# Bug 修复
fix/xxx

# 重构
refactor/xxx

# 文档
docs/xxx

# 测试
test/xxx

# 杂项维护
chore/xxx
```

示例：

```bash
feat/faas-timer-trigger-update
fix/auth-callback-error
refactor/browser-auth-cookie-adapter
docs/update-agent-git-flow
test/browser-auth-task
```

## 4. 提交代码前检查

```bash
# 查看当前分支和工作区状态
git status --branch --short

# 查看具体改动
git diff

# 查看已暂存改动
git diff --cached
```

运行相关测试：

```bash
python3 -m unittest test.test_xxx
```

运行语法检查：

```bash
python3 -m py_compile path/to/file.py
```

## 5. 提交代码

```bash
# 添加指定文件
git add path/to/file.py

# 添加所有改动
git add -A

# 提交
git commit -m "<type>(<scope>): <subject>"
```

提交信息格式：

```text
<type>(<scope>): <subject>
```

常用 `type`：

```text
feat: 新功能
fix: Bug 修复
docs: 文档修改
style: 格式调整，不影响逻辑
refactor: 重构
test: 测试相关
chore: 构建、依赖、辅助工具等杂项
```

示例：

```bash
git commit -m "feat(auth): add browser auth callback"
git commit -m "fix(faas): handle timer trigger event"
git commit -m "refactor(auth): simplify cookie adapter"
git commit -m "docs(agent): update git workflow"
git commit -m "test(faas): add timer trigger integration test"
```

## 6. 推送当前开发分支

首次推送新分支：

```bash
git push -u origin feat/your-feature-name
```

后续推送：

```bash
git push origin feat/your-feature-name
```

或当前分支已设置 upstream 后：

```bash
git push
```

## 7. 推送前同步最新 main

如果你的分支开发了一段时间，`main` 可能已经前进。推送或提 MR 前建议同步最新 `origin/main`。

先获取远程最新信息：

```bash
git fetch origin
```

### 个人独占分支

优先使用 rebase：

```bash
git rebase origin/main
```

如果出现冲突：

```bash
# 1. 手动解决冲突文件
# 2. 标记冲突已解决
git add path/to/conflict-file.py

# 3. 继续 rebase
git rebase --continue
```

如果想放弃这次 rebase：

```bash
git rebase --abort
```

rebase 后如果分支之前已经推到远程，需要：

```bash
git push --force-with-lease
```

不要使用：

```bash
git push --force
```

### 多人共享分支

不要随意 rebase，使用 merge：

```bash
git merge origin/main
```

如果出现冲突：

```bash
# 1. 手动解决冲突文件
git add path/to/conflict-file.py

# 2. 提交 merge commit
git commit

# 3. 推送
git push
```

## 8. 为什么不要复用已合并分支

不推荐在已经合并到 `main` 的旧分支上继续开发新功能。

原因：

- 旧分支会带着历史提交和冲突处理记录。
- 后续 MR 差异可能变复杂。
- 容易重复引入已经合并过的上下文。
- 更容易出现远程合并冲突。
- 不符合“一事一分支”的 GitHub Flow 思路。

推荐做法：

```bash
# 合并完成后，回到 main
git checkout main

# 拉最新 main
git fetch origin
git pull --ff-only origin main

# 删除旧本地分支
git branch -d feat/old-branch

# 新需求重新建分支
git checkout -b feat/new-feature
```

## 9. 删除本地分支

查看本地分支：

```bash
git branch
```

删除已经合并的本地分支：

```bash
git branch -d feat/old-branch
```

强制删除本地分支：

```bash
git branch -D feat/old-branch
```

注意：不能删除当前所在分支，需要先切到其他分支，例如：

```bash
git checkout main
git branch -d feat/old-branch
```

## 10. 删除远端分支

删除远端分支：

```bash
git push origin --delete feat/old-branch
```

删除后清理本地远端跟踪引用：

```bash
git fetch -p
```

或：

```bash
git remote prune origin
```

## 11. 为什么远端删了，本地还看得到

`git branch -a` 会显示两类内容：

```text
main
feat/xxx
remotes/origin/feat/xxx
```

含义：

```text
main                         本地分支
feat/xxx                     本地分支
remotes/origin/feat/xxx      本地保存的远端跟踪引用
```

远端分支删除后，本地的 `remotes/origin/xxx` 不会自动消失。

清理方式：

```bash
git fetch -p
```

然后再查看：

```bash
git branch -a
```

## 12. 查看分支合并情况

切到最新 main：

```bash
git checkout main
git fetch origin
git pull --ff-only origin main
```

查看已经合并到当前分支的本地分支：

```bash
git branch --merged
```

查看还没合并到当前分支的本地分支：

```bash
git branch --no-merged
```

通常 `git branch --merged` 里列出的旧功能分支可以考虑删除，但不要删除 `main`。

## 13. 查看当前分支和远端差异

查看当前分支相对 upstream 的状态：

```bash
git status --branch --short
```

查看当前分支和 `origin/main` 的提交数量差异：

```bash
git rev-list --left-right --count origin/main...HEAD
```

输出示例：

```text
0  3
```

含义：

```text
左边：origin/main 有、当前分支没有的提交数
右边：当前分支有、origin/main 没有的提交数
```

## 14. 处理远程 MR 冲突的推荐流程

先确认当前分支：

```bash
git status --branch --short
```

获取最新远端：

```bash
git fetch origin
```

个人独占分支推荐：

```bash
git rebase origin/main
```

多人共享分支推荐：

```bash
git merge origin/main
```

解决冲突后：

```bash
git add -A
```

如果是 rebase：

```bash
git rebase --continue
git push --force-with-lease
```

如果是 merge：

```bash
git commit
git push
```

## 15. 本地已经处理冲突，但远程 MR 仍显示冲突

常见原因：

- 本地解决了冲突，但没有 push 到远端分支。
- 远端 `main` 在你处理后又进了新提交。
- 你看的 MR 页面缓存没刷新。
- 当前 MR 源分支不是你本地正在推的那个分支。

检查：

```bash
git status --branch --short
```

如果显示类似：

```text
## feat/xxx...origin/feat/xxx [ahead 3]
```

说明本地比远端多 3 个提交，需要推送：

```bash
git push
```

## 16. 已经合并过的分支，下一次不要继续用

推荐流程：

```bash
# 当前 MR 合并后
git checkout main
git fetch origin
git pull --ff-only origin main

# 删除旧分支
git branch -d feat/old-branch
git push origin --delete feat/old-branch

# 新任务重新建分支
git checkout -b feat/new-task
```

如果旧分支还没有合并，但确定不要了：

```bash
git branch -D feat/old-branch
git push origin --delete feat/old-branch
```

## 17. Agent 开发前应做的检查

Agent 在开始写代码前，应先判断当前任务是否是新的开发任务。

如果是新功能、新需求、新一轮修复，优先询问或执行：

```bash
git checkout main
git fetch origin
git pull --ff-only origin main
git checkout -b feat/new-task
```

如果用户明确要求继续在当前分支开发，可以继续，但要提醒：

```text
继续复用旧分支会增加后续 MR 冲突概率。
```

## 18. 常用命令速查

```bash
# 查看状态
git status --branch --short

# 查看本地分支
git branch

# 查看所有分支
git branch -a

# 获取远端最新信息
git fetch origin

# 清理已删除的远端跟踪分支
git fetch -p

# 切到 main
git checkout main

# 拉最新 main
git pull --ff-only origin main

# 新建分支
git checkout -b feat/xxx

# 添加全部改动
git add -A

# 提交
git commit -m "feat(scope): subject"

# 推送新分支
git push -u origin feat/xxx

# 推送当前分支
git push

# 删除本地已合并分支
git branch -d feat/xxx

# 强制删除本地分支
git branch -D feat/xxx

# 删除远端分支
git push origin --delete feat/xxx

# 个人分支同步 main
git rebase origin/main

# 共享分支同步 main
git merge origin/main

# rebase 后安全强推
git push --force-with-lease
```
