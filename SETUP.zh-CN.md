# 本地运行与 Vercel 部署

本工作树连接 kazoottt 的真实公开 Notion 库，未改动库中内容或字段。个人简介、最终域名、评论与统计尚待配置。

## 环境及启动

仓库版本 4.11.0，基线 a2aa934b41c51ea95b74cbb67430e38392be01cd。使用 Node 22 和 Yarn 1.22.22；系统默认 Node 26 不满足项目要求。

```sh
cd /Users/kazoottt/Developer/worktrees/NotionNext/setup-blog
export PATH=/Users/kazoottt/.nvm/versions/node/v22.17.0/bin:$PATH
# 首次配置；已有 .env.local 时先比较，不要覆盖个人设置。
cp -n .env.setup.example .env.local
yarn install --frozen-lockfile
yarn dev --hostname 127.0.0.1 --port 3217
```

访问 http://localhost:3217 。若端口被占用，选择其他空闲端口；NEXT_PUBLIC_LINK 保持正式域名。本机使用开发服务预览。生产构建命令为 `yarn build`，成功后以 `yarn start --hostname 127.0.0.1 --port 3217` 启动，不要与开发服务共用 .next 构建目录。

本次真实库含大小写不同的标签，macOS 默认文件系统在 `/tag/AI` 与 `/tag/ai` 静态输出时发生路径冲突，本地 `yarn build` 退出 1；未改写用户标签。Linux 上的 Vercel 云构建已成功，最终 HTTPS 首页和真实文章已验证。上游编译期间有 Clerk/React 导入及 swcMinify/runtimeConfig 警告，未阻止云端构建。

配置示例为 `.env.setup.example`，本地生效文件 `.env.local` 已被 Git 忽略；无需配置或分享 Notion token。若公开读取失效，先检查 Notion 根数据库及文章的公开分享权限。

## 旧库兼容

当前字段名称和类型匹配 conf/notion.config.js。Post/Page/Notice/Menu/SubMenu/CONFIG 适用；Member/Event/ext 是可选功能，博客无需补建。status 只将 Published 与 Invisible 识别为已发布；Invisible 不显示于普通列表但可通过链接访问，不能用于存放私密内容。Draft 和拼写 Pubslihed 保持原值，不批量纠正。数据库本身公开分享的权限与博客列表过滤是两回事。

站点标题、描述和部分主题配置可能从数据库标题/描述与 CONFIG 页面读取，优先检查原有 Notion 配置再定制，避免环境变量被已有配置覆盖。密码字段不是数据库访问控制的替代品。

## Vercel

当前通过 CLI 本地部署，无需新建 GitHub 仓库或推送。使用已登录身份 kazoottt；不读取 token。

```sh
export PATH=/Users/kazoottt/.nvm/versions/node/v22.17.0/bin:$PATH
npm exec --yes --package=vercel -- vercel whoami
```

Vercel 项目设置应使用 Next.js、Node 22.x、`yarn install --frozen-lockfile`、`yarn build`。正式域名为 https://notion.kazoottt.top 。生产环境至少设置 NOTION_PAGE_ID、API_BASE_URL、NEXT_PUBLIC_THEME、NEXT_PUBLIC_LANG、NEXT_PUBLIC_AUTHOR、NEXT_PUBLIC_BIO 与最终 NEXT_PUBLIC_LINK。后续改动环境变量需要重新部署。不要把本地 localhost 地址作为正式域名。

本次实际服务地址、部署结果、检查退出码和日志见 `/Users/kazoottt/Developer/notionnext-setup-report.md`。运行日志在 `output/setup/`，此目录及 Notion 缓存不上传到 Vercel。上游 docs 在 sparse-checkout 中排除；它们只是文档站，不影响博客构建。

官方入口：https://notionnext.tangly1024.com/user-guide/start-here

## GitHub 持久关联

新版代码保存在 https://github.com/kazoottt/NotionNext 的 setup-blog 分支，来自上游 4.11.0。旧仓库 https://github.com/kazoottt/NotionNext-kazoottt 保留原 main、历史部署与未合并 PR。新 Vercel 项目 notionnext-kazoottt 的生产分支设为 setup-blog；关联与自动部署证据见交付报告。不提交 .env.local、.vercel、日志或缓存。

Vercel 已持久保存示例中的八项公开配置，覆盖 production/preview/development。推送 setup-blog 自动创建生产部署；main 未合并前不作为生产来源。后续合并 PR 后如需转为 main，应同步修改 Vercel 的生产分支并重新核验，避免切换到未配置版本。

## 已验证的在线入口

- 首页：https://notion.kazoottt.top/
- 真实文章：https://notion.kazoottt.top/article/bw2024

文章沿用 Notion CONFIG 的 article/ 前缀，因此 /bw2024 返回 404 属于路由配置结果。Git 自动部署已显式启用（createDeployments=enabled），部署前可检查 Vercel 项目生产分支仍为 setup-blog。个人简介和评论/统计仍可后续按需配置。
