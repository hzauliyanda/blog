# 网页后台（Sveltia CMS）设置指南

后台地址：`https://blog.hzauliyanda.workers.dev/admin/`

后台用 GitHub 账号登录，写文章后会自动提交到本仓库的 `main` 分支，
Cloudflare 随即自动部署。登录需要一个一次性的 OAuth 授权配置，照下面三步做即可（约 10 分钟，只需做一次）。

---

## 第 1 步：创建 GitHub OAuth App

1. 打开 https://github.com/settings/developers → **OAuth Apps** → **New OAuth App**
2. 填写：
   - **Application name**：`老达子博客后台`（随意）
   - **Homepage URL**：`https://blog.hzauliyanda.workers.dev`
   - **Authorization callback URL**：`https://laodazi-cms-auth.<你的子域>.workers.dev/callback`
     （这个 Worker 还没建，第 2 步建好后回来把真实地址填进去——先随便填，建好再改）
3. 创建后记下 **Client ID**，再点 **Generate a new client secret** 生成 **Client Secret**（只显示一次，复制好）

## 第 2 步：部署 OAuth 中转 Worker

这个小 Worker 负责把 GitHub 登录结果转交给后台。用现成的开源项目 `sveltia-cms-auth`：

**最简方式（网页点选）**：
1. 打开 https://github.com/sveltia/sveltia-cms-auth
2. 按其 README 的 “Deploy to Cloudflare Workers” 按钮（或在 Cloudflare 控制台 *Workers & Pages → Create → 导入该仓库*）部署
3. 部署后在该 Worker 的 **Settings → Variables and Secrets** 添加三个变量：
   - `GITHUB_CLIENT_ID` = 第 1 步的 Client ID
   - `GITHUB_CLIENT_SECRET` = 第 1 步的 Client Secret
   - `ALLOWED_DOMAINS` = `blog.hzauliyanda.workers.dev`
4. 记下这个 Worker 的网址，形如 `https://laodazi-cms-auth.xxx.workers.dev`

## 第 3 步：把地址填回去

1. 回到第 1 步的 GitHub OAuth App，把 **Authorization callback URL** 改成：
   `https://<第2步的Worker地址>/callback`
2. 编辑本仓库 `public/admin/config.yml`，把 `base_url` 改成第 2 步的 Worker 地址（不带 /callback）：
   ```yaml
   base_url: https://laodazi-cms-auth.xxx.workers.dev
   ```
   （改完 `git push`，或直接在 GitHub 网页上编辑这个文件）

---

## 完成后怎么用

1. 打开 `https://blog.hzauliyanda.workers.dev/admin/`
2. 点 **Sign in with GitHub** 授权
3. 进去就能：新建/编辑「文章」和「页面」、上传图片、点 **Publish** 发布
4. 发布后等 1～2 分钟，Cloudflare 自动部署，线上即可见

> 注意：后台是直接读写 GitHub 仓库的，所以本地写作（改 md + git push）和网页后台两种方式可以混用，互不冲突。
