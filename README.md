# Mirobody 懒猫微服打包（LPK）

[Mirobody](https://github.com/thetahealth/mirobody)（Apache-2.0）的懒猫微服 LPK 打包仓库。Mirobody 是自托管健康记录：化验单、可穿戴设备与基因数据汇成一份完整档案，指标名称标准化（LOINC）且每个数值可溯源，内置 AI 助手查询数据，自带 MCP 服务器。

本仓库为**镜像模式**打包：复用上游官方镜像 `docker.io/thetahealth4mirobody/mirobody` 与 `docker.io/pgvector/pgvector`，由 GitHub Actions（[ca-x/lazycat-github-action](https://github.com/ca-x/lazycat-github-action)）每日跟随上游 stable tag 自动复制镜像并发布到喵喵商店。不存储上游源码。

## 首次使用

1. **配置模型**：打开应用后进入设置页（`/setup`），输入 SETUP_TOKEN（见安装时的部署参数说明）解锁，粘贴你自己的模型 API Key（OpenRouter / OpenAI / Gemini / Claude / DeepSeek / DashScope 任一）；Key 保存前会用一次真实请求验证。不配置模型时聊天不可用，上传与浏览报告仍可用。
2. **注册账号**：未配置 SMTP 时，邮箱仅作为用户名、密码注册，先到先得；注册后建议配置 SMTP 或开启 MFA/passkey。演示账号已在 PRODUCTION 模式下禁用。
3. 数据（PostgreSQL、上传文件）持久化在应用数据目录 `/lzcapp/var`；加密密钥由平台稳定生成，迁移微服时需同步迁移。

## 说明

- `min_os_version: 1.5.0`（LPK v2）；双服务（mirobody + pgvector/pgvector 17）
- 打包以 `PRODUCTION: true` 运行：禁用演示账号、强制替换所有占位密钥（JWT_KEY、PG_ENCRYPTION_KEY、CONFIG_ENCRYPTION_KEY、SETUP_TOKEN、PG_PASSWORD 均由平台稳定密钥生成）
- 未打包 llama.cpp 本地模型 profile（需要 16GB 内存与模型下载，云模型 Key 即可完整使用；如需全本地可反馈再加）
- 图标来自上游 `docs/images/mirobody-icon.svg`
- 请勿在应用内自更新，版本跟随由 CI 维护
