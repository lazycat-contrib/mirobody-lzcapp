# Mirobody 懒猫微服打包（LPK）

[Mirobody](https://github.com/thetahealth/mirobody)（Apache-2.0）的懒猫微服 LPK 打包仓库。Mirobody 是自托管健康数字分身：汇聚体检报告（PDF / 照片等 23 种文件）、Apple Health / Garmin / Oura / Whoop 等穿戴数据与基因数据，统一指标名称与单位，每个数值可追溯到来源，并内置个人 MCP 服务。

本仓库为**镜像模式**打包：直接复用上游官方镜像 `thetahealth4mirobody/mirobody`（Docker Hub），由 GitHub Actions（[ca-x/lazycat-github-action](https://github.com/ca-x/lazycat-github-action)）每日跟随上游 stable tag（`^\d+\.\d+\.\d+$`）自动复制镜像并发布到喵喵商店。不存储上游源码。

## 服务结构

| 服务 | 镜像 | 说明 |
|------|------|------|
| `pg` | pgvector/pgvector:0.8.6-pg17-trixie | 上游 compose 固定的 Postgres 17 + pgvector |
| `mirobody` | thetahealth4mirobody/mirobody | Web 应用 + API（18060） |
| `mirobody_worker` | thetahealth4mirobody/mirobody | 后台任务 worker |

与上游 compose 的差异：

- 上游的 `mirobody_init` 一次性容器（修正上传目录属主）改为平台侧 `binds` 目录权限处理；懒猫 bind 目录按挂载 UID 管理，`user: "10001:10001"` 与镜像内 mirobody 用户一致。
- 上游 llama.cpp 本地模型 profile（`local` / `local-cpu`）未打包：需要 GPU 或 8GB+ 内存专用容器，懒猫部署请使用云端模型 Key。
- 密钥（PG 密码、PG/配置加密钥、JWT、SETUP_TOKEN）使用平台稳定密钥（`stable_secret`），重装不变。

## 首次使用

1. 安装后打开应用，登录演示账号 `you@mirobody.ai` 或 `mom@mirobody.ai`，邮箱验证码 `111111`（合成演示数据）。注册自己的账号走邮箱 + 密码注册（部署不发送邮件，自注册账号仅密码登录可用）。
2. **模型配置（必须）**：访问 `设置 → 模型设置`（`/setup?token=<SETUP_TOKEN>`，SETUP_TOKEN 为平台稳定密钥，见部署参数说明），填入任一支持的 LLM API Key（OpenRouter / DashScope / Gemini / OpenAI / Anthropic / DeepSeek）。不填 Key 时登录与浏览演示数据可用，文档识别与问答不可用。
3. 数据持久化在 `/lzcapp/var`（Postgres 数据与上传文件）。

## 说明

- `min_os_version: 1.5.0`（LPK v2）
- 图标来自上游 `docs/images/mirobody-icon.svg`，转为 512×512 PNG
- 请勿在应用内执行自更新，版本跟随由 CI 维护
