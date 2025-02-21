# 个人技术博客

这是我的个人技术博客源码，使用 Hexo 框架和 Butterfly 主题构建。

## 项目结构

- `main` 分支：存放生成的静态网站文件
- `source` 分支：存放网站源代码

## 本地开发环境搭建

1. 安装必要软件：
   ```bash
   # 安装 Node.js: https://nodejs.org/
   # 安装 Git: https://git-scm.com/
   ```

2. 克隆项目：
   ```bash
   git clone https://github.com/dw-ly/dw-ly.github.io.git
   cd dw-ly.github.io
   git checkout source  # 切换到源码分支
   ```

3. 安装依赖：
   ```bash
   npm install
   ```

4. 配置 GitHub 认证：
   - 访问 GitHub.com -> Settings -> Developer settings
   - 生成个人访问令牌 (Personal Access Token)
   - 在本地配置 Git 认证：
     ```bash
     git config --global credential.helper store
     # 首次 push 时输入用户名和访问令牌作为密码
     ```
   - 或者使用 SSH 密钥认证

## 日常工作流程

1. 创建新文章：
   ```bash
   hexo new "文章标题"
   ```
   新文章将会创建在 `source/_posts` 目录下

2. 本地预览：
   ```bash
   hexo server
   ```
   访问 http://localhost:4000 查看效果

3. 部署更新：
   ```bash
   hexo clean      # 清理缓存
   hexo generate   # 生成静态文件
   hexo deploy     # 部署到 GitHub Pages
   ```

4. 保存源码：
   ```bash
   git add .
   git commit -m "更新说明"
   git push origin source
   ```

## 在新电脑上工作

1. 环境准备：
   - 安装 Node.js
   - 安装 Git
   - 配置 GitHub 访问权限（SSH 密钥或个人访问令牌）

2. 安装 Hexo 命令行工具：
   ```bash
   npm install -g hexo-cli
   ```

3. 克隆源码：
   ```bash
   git clone https://github.com/dw-ly/dw-ly.github.io.git
   cd dw-ly.github.io
   git checkout source
   ```

4. 安装项目依赖：
   ```bash
   npm install
   ```

## 目录结构说明

```
.
├── _config.yml          # 网站配置文件
├── _config.butterfly.yml # 主题配置文件
├── package.json        # 项目依赖配置
├── scaffolds/         # 模板文件夹
├── source/           # 源文件夹
│   ├── _posts/      # 文章
│   ├── about/       # 关于页面
│   └── navigation/  # 导航页面
└── themes/          # 主题文件夹
```

## 写作指南

1. 文章格式：
   ```markdown
   ---
   title: 文章标题
   date: 2024-02-21
   categories:
     - 分类
   tags:
     - 标签1
     - 标签2
   ---

   文章内容...
   ```

2. 图片引用：
   - 将图片放在 `source/images` 目录下
   - 在文章中使用相对路径引用：`![描述](/images/图片.jpg)`

## 主题定制

主题配置文件位于 `_config.butterfly.yml`，可以修改：
- 导航菜单
- 侧边栏
- 页面布局
- 配色方案
- 等其他设置

## 常见问题

1. 部署失败：
   - 检查 GitHub 访问令牌是否过期
   - 确认 `_config.yml` 中的部署配置是否正确

2. 本地预览显示异常：
   - 运行 `hexo clean` 清理缓存
   - 检查主题配置是否正确

## 相关链接

- [在线预览](https://dw-ly.github.io)
- [Hexo 文档](https://hexo.io/zh-cn/docs/)
- [Butterfly 主题文档](https://butterfly.js.org/)