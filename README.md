<div align="center">

# 序栈 · Xuzhan

**面向个人创作的单用户技术 CMS**<br>
*An editorial CMS for a personal technical site*

[![Status](https://img.shields.io/badge/Status-Active-2ea44f?style=for-the-badge)](https://github.com/kerntau/xuzhan)
[![React](https://img.shields.io/badge/React-19-61DAFB?style=for-the-badge&logo=react&logoColor=black)](https://github.com/kerntau/xuzhan)
[![Go](https://img.shields.io/badge/Go-1.26-00ADD8?style=for-the-badge&logo=go&logoColor=white)](https://github.com/kerntau/xuzhan)
[![License](https://img.shields.io/badge/License-AGPL--3.0-blue?style=for-the-badge)](LICENSE)

</div>

---

## 01. 项目定位 // POSITIONING

序栈把文章、独立页面、分类、标签和站点设置集中到一个单管理员后台。前台和管理界面由 React + Rsbuild 构建，Go API 使用 SQLite 保存运行时内容。项目面向个人站长，不是多租户 SaaS，也不提供角色、组织或媒体存储服务。

## 02. 功能与边界 // SCOPE

- Markdown / MDX 文章与页面，支持草稿、发布、下线、分类和标签。
- 站点设置、导航、友链、SEO、Sitemap、Robots、RSS 和 JSON-LD 配置。
- 管理员会话、登录限流、操作审计、SQLite 备份与后台任务。
- 固定仓库的 Git 检查、更新和回滚，避免 CMS 内容被代码同步覆盖。
- 外链图片只保存 URL；评论审核和建议收件箱目前只有后台数据接口。

## 03. 技术矩阵 // TECHNICAL MATRIX

| 层级 | 实现 | 责任 |
| --- | --- | --- |
| 前台与后台 | React 19、TypeScript、Rsbuild | 页面、Admin 界面和缓存刷新入口 |
| 内容处理 | Markdown / MDX、Contentlayer2 | 历史内容导入与构建期内容 |
| 业务 API | Go 标准库 | 鉴权、内容、审计和系统任务 |
| 数据存储 | SQLite（WAL） | 内容、设置、会话、日志和备份 |
| 运行方式 | Node.js + Go | 前台静态/Node 服务与 API 分离 |

架构路径：

~~~mermaid
flowchart LR
  B[浏览器] --> UI[React + Rsbuild 前台 /admin]
  UI -->|同源 /api/v1| API[Go CMS API]
  API --> DB[(SQLite)]
  API --> JOB[备份、Git、SEO 与缓存任务]
  CONTENT[Markdown / MDX] --> BUILD[Contentlayer 构建]
  BUILD --> UI
~~~

## 04. 目录拓扑 // REPOSITORY TOPOLOGY

~~~text
.
├── backend/             # Go API、SQLite 访问与导入工具
├── content/             # Markdown / MDX 历史内容
├── docs/                # 需求、设计和提交规范
├── public/              # 静态资源与安全头
├── scripts/             # 构建、部署和 SEO 脚本
├── src/
│   ├── app/             # 主站、/admin 和 API 适配
│   ├── features/        # 内容、站点、搜索等业务模块
│   └── shared/          # 跨模块组件和适配器
├── install.sh           # Linux 首次部署入口
├── .env.example
└── backend/.env.example
~~~

## 05. 快速开始 // QUICK START

环境要求：Node.js 20+、pnpm 9+、Go 1.26。

~~~bash
pnpm install
pnpm dev:api       # 终端一：Go API，默认 8080
pnpm dev           # 终端二：React 前台，默认 3000
~~~

服务器首次部署：

~~~bash
bash install.sh
~~~

本地 API 代理使用根目录环境变量 CMS_API_PROXY_URL=http://127.0.0.1:8080。需要导入历史内容时，在 backend 目录运行 go run ./cmd/import-content。

## 06. 部署与验证 // DEPLOYMENT & VALIDATION

- 生产环境建议把前台和 API 分开运行，由 Nginx 或 Caddy 代理 /api/。
- storage/ 必须放在持久化本地磁盘，SQLite 不应放在网络盘。
- 使用 install.sh 只负责依赖安装、构建和进程启动；后续代码更新使用 scripts/deploy.mjs。
- 运行 pnpm build、pnpm typecheck、pnpm test:api 和 go -C backend vet ./... 验证构建与 API。
- 密钥、管理员密码、Git 凭据和推送 Token 只放环境变量，不提交 .env。

## 07. 工程契约 // ENGINEERING CONTRACT

提交前请保持内容与代码分离，不把数据库文件、storage/ 或密钥加入版本库。涉及 API、导入和部署脚本的改动，应同时更新对应 docs，并在描述中说明回滚方式与数据影响。

## 08. 路线图 // ROADMAP

- [x] 单管理员 CMS、SQLite 内容存储和后台任务
- [x] Markdown / MDX 导入、SEO 与 Git 更新流程
- [x] 前台搜索和离线内容兼容路径
- [ ] 接入前台评论提交和建议反馈界面
- [ ] 持续完善备份恢复与部署可观测性

## 09. 许可证 // LICENSE

本项目采用 [AGPL-3.0](LICENSE) 许可。
