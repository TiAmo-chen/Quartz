# Quartz v4 GitHub Pages 模板

[Quartz][] 是一套工具，可帮助你将个人[数字花园](https://jzhao.xyz/posts/networked-thought)（数字笔记库）和笔记以网站形式免费发布。

本模板仓库具有以下特点：
- 以 Git 子模块形式集成了 Jacky Zhao 开发的 [Quartz][]，以便及时跟进其快速迭代。
- 通过 GitHub Actions（GitHub 自动化工作流）从笔记内容构建网站，并将网站发布到 GitHub Pages。
- 对 [Obsidian](https://obsidian.md/) 笔记软件友好（得益于 [Quartz][] 的支持），只需在 Obsidian 中打开 `content` 文件夹即可编辑笔记。

[Quartz]: https://github.com/jackyzha0/quartz


## 如何在本地使用 Quartz

点击绿色的 **`Use this template`** 按钮创建仓库。

由于本仓库将 [Quartz][] 作为子模块，如果需要使用 Quartz 本地构建/预览网站，需同时克隆主仓库和子模块：

```bash
git clone --recursive https://github.com/TiAmo-chen/Quartz.git

```

如需以预览模式运行 Quartz（详细步骤见 [Building your Quartz](https://quartz.jzhao.xyz/build)），执行以下命令：

```bash
cd quartz  # 进入 Quartz 子模块目录
npm i      # 安装依赖（需先安装 Node.js）
npx quartz build -d ../content --serve  # 构建并启动本地预览服务

#20 线程解析
npx quartz build -d ../content --serve --concurrency 20 

```



#已解决  更新nodejs版本到最新就可以了
在"npx quartz build -d ../content --serve"这个命令之后无法正确启动本地预览服务

显示
```bash
xuan.chen@chenxuan-nb MINGW64 /d/code/code/blogs/Quartz/quartz ((ef29c69...))
$ npx quartz build -d ../content --serve
(node:52280) ExperimentalWarning: Support for loading ES Module in require() is an experimental feature and might change at any time
(Use `node --trace-warnings ...` to show where the warning was created)

 Quartz v4.5.2

```

你可以在 Obsidian（或其他编辑器）中打开 `content` （不是子模块中的文件夹）文件夹编辑/新增笔记。
完成后，通过 GitHub Desktop 或 `git push` 命令提交并推送更改到 GitHub，GitHub Actions 会自动构建并发布网站。

```bash
git add .
git commit -m "update"
git push
```

> [!IMPORTANT]
> 请务必在仓库设置中启用 GitHub Pages：进入仓库设置 → Pages → 将来源选择为 `GitHub actions`。

************************
下面的不经常使用 
************************


## 最简单的使用方法（无需在本地使用 Quartz）

点击仓库页面中醒目的绿色 **`Use this template`（使用此模板）** 按钮创建新仓库。通过 GitHub Desktop（GitHub 桌面客户端）或 `git clone` 命令克隆创建好的仓库：

```bash
git clone https://github.com/<用户名>/<仓库名>.git
```

在 Obsidian 中打开 `content` 文件夹，编辑或新增笔记后，通过 GitHub Desktop 或 `git push` 命令提交并推送更改到 GitHub。GitHub Actions 会自动构建并发布网站。

> [!IMPORTANT]
> 你需要在仓库设置中启用 GitHub Pages：进入仓库设置 → Pages（页面）→ 将来源选择为 `GitHub actions`（GitHub 工作流）。




## Cloudflare Pages（Cloudflare 页面）配置

[Cloudflare Pages](https://dash.cloudflare.com/)（Cloudflare 提供的静态网站托管服务）的构建配置如下：
- 框架预设（Framework preset）：`None`（无）
- 构建命令（Build command）：`cp quartz.config.ts quartz.layout.ts quartz/ && cd quartz && npm ci && npx quartz build --directory ../content`
- 构建输出目录（Build output directory）：`quartz/public`


### 补充说明
1. **术语解释**：
   - **Git 子模块（submodule）**：一种 Git 功能，允许将一个 Git 仓库作为另一个 Git 仓库的子目录，便于独立管理第三方代码（此处即 Quartz 工具）。
   - **GitHub Actions**：GitHub 提供的自动化工具，可通过配置文件定义代码提交、合并后自动执行的操作（此处为“构建网站并发布到 GitHub Pages”）。
   - **Obsidian**：一款流行的笔记软件，以“双链笔记”和“本地文件管理”为核心特点，适合构建个人知识库。

2. **环境依赖**：
   - 本地使用 Quartz 时，需先安装 [Node.js](https://nodejs.org/)（`npm` 是 Node.js 自带的包管理工具，用于执行 `npm i` 等命令）。
   - 克隆仓库时，若未使用 `--recursive` 参数导致子模块未下载，可后续执行 `git submodule update --init --recursive` 补全子模块。
