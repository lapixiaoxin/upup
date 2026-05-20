# 知识库协作规范

本仓库是个人知识管理项目，用于沉淀可长期维护、可公开发布的系统化学习内容。

## 1. 目录规范

| 版块 | 入口 | 说明 |
| --- | --- | --- |
| Git | [git/_index.md](./git/_index.md) | Git 协作、分支、提交、审查、版本管理 |
| Docker | [docker/_index.md](./docker/_index.md) | 容器化、镜像、Compose、交付与排障 |
| Finance | [finance/_index.md](./finance/_index.md) | 个人理财、资产配置、风险管理 |

规则：

* 每个一级知识版块必须维护 `_index.md`。
* 新增领域时，先建目录和 `_index.md`，再写具体文档。
* 复杂知识点单独拆成 `.md` 文件，不堆在索引里。
* 大纲文件使用 `00_` 前缀；专题文件使用数字前缀。
* 新增、删除、重命名文档后，必须同步更新对应 `_index.md`。

## 2. 写作规范

* 结构遵循 **Why -> What -> How**。
* 保持高知识密度，少铺垫，少空话。
* 使用标准 Markdown 标题组织层级。
* 不使用 Emoji 或装饰性符号。
* 涉及方案选择时，必须说明优缺点、适用场景和不适用场景。
* 技术内容要给出使用场景、前置条件、命令示例和风险边界。
* 理财内容只做知识整理，不构成投资建议；必须区分事实、假设、观点和建议。

## 3. Agent 工作规则

Agent 修改仓库时：

1. 先读本文件。
2. 再读目标版块的 `_index.md`。
3. 只修改与任务直接相关的文件。
4. 保留用户已有改动，不做无关重构。
5. 若发现索引和目录不一致，优先修正索引。
6. 修改完成后检查 `git status --branch --short`。

## 4. Git 维护规则

本仓库默认直接在 `main` 分支维护。除非用户明确要求，否则不要为普通文档更新新建分支或 PR。

Agent 具备自动提交权限：完成用户请求并检查无误后，可以自动执行 `git add`、`git commit`、`git push`，无需再次询问。

默认流程：

```bash
git status --branch --short
git pull --ff-only origin main
git add <changed-files>
git commit -m "docs(scope): subject"
git push origin main
```

限制：

* 只提交本次任务相关文件。
* 不提交 `.DS_Store`、缓存、临时文件、密钥或凭据。
* 不使用 `git push --force`。
* 远端有冲突时，先停止并说明原因。

远程仓库：

```bash
origin git@github.com-personal:lapixiaoxin/upup.git
```

当前仓库应使用个人提交身份：

```bash
git config user.name "lapixiaoxin"
git config user.email "lapixiaoxin6@gmail.com"
```

## 5. 提交信息规范

格式：

```text
<type>(<scope>): <subject>
```

常用类型：

| Type | 场景 |
| --- | --- |
| `docs` | 新增或修改知识文档 |
| `fix` | 修正文档错误或失效链接 |
| `refactor` | 重组文档结构 |
| `chore` | 仓库配置、忽略文件、目录维护 |

常用范围：

| Scope | 场景 |
| --- | --- |
| `global` | 顶层规范、全局索引 |
| `git` | Git 版块 |
| `docker` | Docker 版块 |
| `finance` | Finance 版块 |

示例：

```bash
git commit -m "docs(global): 精简 Agent 协作规范"
git commit -m "docs(git): 补充分支管理说明"
git commit -m "chore(global): 更新忽略规则"
```
