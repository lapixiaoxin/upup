# 企业级 Git 项目管理实战指南

在真实的企业环境中，Git 已经不仅仅是一个版本控制工具，它更是**团队协作的基石、自动化流水线的触发器、以及代码质量与安全的守门员**。

以下是企业级项目中 Git 运用的核心知识大纲，涵盖了从分支模型到安全合规的方方面面。

## 1. 企业级分支管理模型 (Branching Strategies)
决定了团队协作的节奏和环境部署的方式。
* **GitFlow**：经典的严格分支模型（主干、开发、特性、发布、热修复）及其适用场景。
* **GitHub Flow / GitLab Flow**：轻量级分支模型，适合持续交付（CI/CD）的现代敏捷开发。
* **Trunk-Based Development (主干开发)**：极简模型，大厂（如 Google）及微服务架构下的高频发布实践。
* **环境隔离与分支映射**：如何将不同分支（如 `dev`, `release`, `main`）与测试环境、预发环境、生产环境进行物理和逻辑对应。

## 2. 规范化提交与变更追踪 (Commit Conventions & Tracking)
告别无意义的 `update` 或 `fix bug` 提交，让代码历史具备业务可读性。
* **约定式提交 (Conventional Commits)**：`feat`, `fix`, `chore`, `refactor`, `docs` 等规范语义及版本号自动生成（SemVer）。
* **Commit 关联工单与业务线**：如何与 Jira, 飞书, Trello 等任务管理系统联动（例如 `feat(user): [JIRA-123] add login`），实现代码与业务的闭环。
* **工具链保障**：在企业工程中集成 `Commitizen` 和 `husky` + `commitlint`，通过底层拦截强制约束提交格式。

## 3. 代码审查与合并流程 (Code Review & Pull/Merge Requests)
保障主干代码质量的最核心环节。
* **PR/MR 模板 (Templates)**：规范化提出合并请求时必须填写的表单信息（需求背景、变更点、测试用例、自测截图等）。
* **分支保护规则 (Branch Protection)**：禁止直接 push 主干，必须通过 PR、至少 N 名 Reviewer 批准 (Approve)、必须通过 CI 自动化测试等硬性指标。
* **Merge 策略的企业级取舍**：
  * `Merge commit` (保留分支结构)
  * `Squash and merge` (压缩合并，保持主干整洁)
  * `Rebase and merge` (变基合并，线性历史)

## 4. 冲突解决、历史线维护与救火 (Conflicts, History & Emergency)
在多人协作引发混乱时，如何优雅、安全地恢复。
* **Rebase (变基) 的黄金法则**：什么时候必须用 Rebase 保持历史整洁，什么时候**绝对不能用**（避免毁坏公共分支）。
* **高级冲突处理**：理解底层的树冲突，利用现代 IDE 和 Git 机制解决复杂的代码冲突。
* **Git 救火指南**：
  * `git reflog`：找回丢失的提交或被误删的分支。
  * `git cherry-pick`：跨分支精确摘取特定功能（常用于紧急热修复和多版本同步）。
  * `git revert`：安全、可追溯地回滚线上问题代码，而不是使用破坏性的 `reset`。

## 5. 持续集成与自动化 (CI/CD Integration & Git Hooks)
Git 作为 DevOps 的起点。
* **Git Hooks 客户端实战**：利用 `pre-commit`, `pre-push` 钩子，在代码提交前自动进行代码格式化 (Prettier/ESLint) 和静态漏洞扫描。
* **服务端联动与 Webhook**：通过 Git Webhook 触发 Jenkins / GitLab CI / GitHub Actions 自动化流水线，实现“提交即测试，合并即部署”。

## 6. 权限管理、安全与大型架构 (Security, ACL & Architecture)
保护企业核心资产。
* **敏感信息拦截**：防止 AKS、Secret、数据库密码被意外提交到仓库的扫描机制 (如 git-secrets, trufflehog)。
* **代码签名与身份防伪**：使用 GPG 签名提交 (Verified Commits)，防止伪造他人提交代码。
* **细粒度权限控制**：基于角色 (RBAC) 的仓库及受保护分支访问权限管理。
* **大型项目架构的 Git 应对**：Monorepo (单体仓库) 与 Multi-repo 的优劣势对比，以及 Git Submodule/Subtree 在依赖组件管理中的企业级运用。
