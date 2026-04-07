# Fork 自动同步上游并镜像到 CNB

本仓库新增了工作流 `sync-upstream-and-mirror-cnb.yml`，用于完成下面这条链路：

1. 定时从上游 `amll-dev/amll-ttml-db` 抓取最新提交
2. 将上游的 `main` 精确同步到当前 GitHub 仓库的 `upstream-main` 分支
3. 再将该同步结果镜像推送到 CNB 的 `main` 分支

这样可以避免把自动化工作流直接堆在上游镜像分支上，导致 fork 的 `main` 与上游主分支难以保持干净同步。

## 分支约定

- 当前 GitHub 仓库默认分支：继续保留你自己的工作流与说明文件
- 当前 GitHub 仓库镜像分支：默认 `upstream-main`
- CNB 目标分支：默认 `main`

如果你希望修改这些名称，可以通过 GitHub 仓库变量覆盖。

## 需要配置的 GitHub 仓库变量

- `UPSTREAM_REPO`：可选，默认 `amll-dev/amll-ttml-db`
- `UPSTREAM_BRANCH`：可选，默认 `main`
- `SYNC_BRANCH`：可选，默认 `upstream-main`
- `CNB_REPO_URL`：必填，例如 `https://cnb.cool/<组织>/<仓库>.git`
- `CNB_TARGET_BRANCH`：可选，默认 `main`

## 需要配置的 GitHub Secrets

- `CNB_USERNAME`：CNB 用户名
- `CNB_TOKEN`：CNB Access Token / Deploy Token

## 工作流触发方式

- 每 30 分钟自动同步一次
- 也可以在 GitHub Actions 页面手动触发
- 首次启用建议先手动触发一次，确认 GitHub 的 `upstream-main` 与 CNB 的目标分支都已出现

## 结果说明

- GitHub 仓库中的 `upstream-main` 会被强制更新为上游 `main`
- CNB 的目标分支会被强制更新为同步后的内容
- 上游新增的 tags 也会被推送到 GitHub 和 CNB

## 注意事项

- 这是镜像同步，`upstream-main` 与 CNB 目标分支都应视为只读分支，不建议手动提交
- 如果 CNB 目标分支上有你自己的提交，工作流会在下一次同步时覆盖它
- 如果你启用了 GitHub 分支保护，请确认该工作流可以推送镜像分支
- 如果 CNB Token 或用户名包含特殊字符，工作流会自动做 URL 编码，无需你手动处理
