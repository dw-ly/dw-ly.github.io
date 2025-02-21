---
title: 导航
date: 2025-02-21
type: "link"
comments: false
top_img: false
---

<style>
.site-card-group {
    display: flex;
    flex-wrap: wrap;
    justify-content: flex-start;
    margin: -0.5rem;
}

.site-card {
    margin: 0.5rem;
    width: calc(33.33333% - 1rem);
    background: var(--card-bg);
    border-radius: 8px;
    transition: all 0.3s;
    position: relative;
    box-shadow: var(--card-box-shadow);
    backdrop-filter: blur(10px);
    background: rgba(255, 255, 255, 0.7);
    border: 1px solid rgba(255, 255, 255, 0.3);
}

@media screen and (max-width: 768px) {
    .site-card {
        width: calc(50% - 1rem);
    }
}

@media screen and (max-width: 576px) {
    .site-card {
        width: calc(100% - 1rem);
    }
}

.site-card:hover {
    box-shadow: var(--card-hover-box-shadow);
    transform: translateY(-3px);
}

.site-card .info {
    display: flex;
    padding: 1rem;
    align-items: center;
}

.site-card .info img {
    width: 2.5rem;
    height: 2.5rem;
    border-radius: 50%;
    margin-right: 1rem;
    object-fit: cover;
}

.site-card .info .title {
    font-weight: bold;
    color: var(--text-highlight-color);
}

.site-card .info .desc {
    font-size: 0.8rem;
    margin-top: 0.3rem;
    color: var(--text-color);
}

.category-name {
    font-size: 1.2rem;
    font-weight: bold;
    margin: 1rem 0.5rem;
    color: var(--text-highlight-color);
    text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.2);
}

.category-desc {
    font-size: 0.9rem;
    margin: 0.5rem;
    color: var(--text-color);
}

[data-theme="dark"] .site-card {
    background: rgba(0, 0, 0, 0.7);
    border: 1px solid rgba(255, 255, 255, 0.1);
}

[data-theme="dark"] .category-name {
    text-shadow: 2px 2px 4px rgba(255, 255, 255, 0.2);
}

body {
    position: relative;
    min-height: 100vh;
    background-color: transparent !important;
}
</style>

<div class="category-name">开发工具</div>
<div class="category-desc">提高开发效率的利器</div>
<div class="site-card-group">
    <a class="site-card" target="_blank" href="https://github.com/">
        <div class="info">
            <img src="https://github.githubassets.com/images/modules/logos_page/GitHub-Mark.png"/>
            <div class="content">
                <div class="title">Github</div>
                <div class="desc">全球最大的代码托管平台</div>
            </div>
        </div>
    </a>
    <a class="site-card" target="_blank" href="https://code.visualstudio.com/">
        <div class="info">
            <img src="https://code.visualstudio.com/assets/images/code-stable.png"/>
            <div class="content">
                <div class="title">VS Code</div>
                <div class="desc">强大的代码编辑器</div>
            </div>
        </div>
    </a>
    <a class="site-card" target="_blank" href="https://devdocs.io/">
        <div class="info">
            <img src="https://devdocs.io/images/icon-192.png"/>
            <div class="content">
                <div class="title">DevDocs</div>
                <div class="desc">开发文档聚合平台</div>
            </div>
        </div>
    </a>
</div>

<div class="category-name">学习资源</div>
<div class="category-desc">优质的学习平台</div>
<div class="site-card-group">
    <a class="site-card" target="_blank" href="https://developer.mozilla.org/">
        <div class="info">
            <img src="https://developer.mozilla.org/mdn-social-share.cd6c4a5a.png"/>
            <div class="content">
                <div class="title">MDN</div>
                <div class="desc">Web技术权威文档</div>
            </div>
        </div>
    </a>
    <a class="site-card" target="_blank" href="https://www.w3schools.com/">
        <div class="info">
            <img src="https://www.w3schools.com/images/w3schools_logo_436_2.png"/>
            <div class="content">
                <div class="title">W3Schools</div>
                <div class="desc">Web开发学习平台</div>
            </div>
        </div>
    </a>
    <a class="site-card" target="_blank" href="https://www.runoob.com/">
        <div class="info">
            <img src="https://static.runoob.com/images/favicon.ico"/>
            <div class="content">
                <div class="title">菜鸟教程</div>
                <div class="desc">中文编程学习平台</div>
            </div>
        </div>
    </a>
    <a class="site-card" target="_blank" href="https://stackoverflow.com/">
        <div class="info">
            <img src="https://cdn.sstatic.net/Sites/stackoverflow/Img/apple-touch-icon.png"/>
            <div class="content">
                <div class="title">Stack Overflow</div>
                <div class="desc">全球最大的程序员问答社区</div>
            </div>
        </div>
    </a>
    <a class="site-card" target="_blank" href="https://www.zhihu.com/">
        <div class="info">
            <img src="https://static.zhihu.com/heifetz/assets/apple-touch-icon-152.a53ae37b.png"/>
            <div class="content">
                <div class="title">知乎</div>
                <div class="desc">中文知识问答社区</div>
            </div>
        </div>
    </a>
</div>

<div class="category-name">AI工具</div>
<div class="category-desc">智能编程助手</div>
<div class="site-card-group">
    <a class="site-card" target="_blank" href="https://chat.openai.com/">
        <div class="info">
            <img src="https://chat.openai.com/apple-touch-icon.png"/>
            <div class="content">
                <div class="title">ChatGPT</div>
                <div class="desc">OpenAI聊天机器人</div>
            </div>
        </div>
    </a>
    <a class="site-card" target="_blank" href="https://github.com/features/copilot">
        <div class="info">
            <img src="https://github.githubassets.com/images/modules/site/copilot/head.png"/>
            <div class="content">
                <div class="title">GitHub Copilot</div>
                <div class="desc">AI编程助手</div>
            </div>
        </div>
    </a>
    <a class="site-card" target="_blank" href="https://cursor.sh/">
        <div class="info">
            <img src="https://cursor.sh/apple-touch-icon.png"/>
            <div class="content">
                <div class="title">Cursor</div>
                <div class="desc">AI驱动的代码编辑器</div>
            </div>
        </div>
    </a>
</div>

<div class="category-name">实用工具</div>
<div class="category-desc">常用在线工具</div>
<div class="site-card-group">
    <a class="site-card" target="_blank" href="https://tool.lu/timestamp/">
        <div class="info">
            <img src="https://tool.lu/favicon.ico"/>
            <div class="content">
                <div class="title">时间戳转换</div>
                <div class="desc">在线时间戳转换工具</div>
            </div>
        </div>
    </a>
    <a class="site-card" target="_blank" href="https://www.json.cn/">
        <div class="info">
            <img src="https://www.json.cn/favicon.ico"/>
            <div class="content">
                <div class="title">JSON在线解析</div>
                <div class="desc">JSON格式化工具</div>
            </div>
        </div>
    </a>
    <a class="site-card" target="_blank" href="https://caniuse.com/">
        <div class="info">
            <img src="https://caniuse.com/img/favicon-128.png"/>
            <div class="content">
                <div class="title">Can I Use</div>
                <div class="desc">浏览器特性兼容性查询</div>
            </div>
        </div>
    </a>
    <a class="site-card" target="_blank" href="https://www.jianguoyun.com/">
        <div class="info">
            <img src="https://www.jianguoyun.com/static/images/logo@2x.png"/>
            <div class="content">
                <div class="title">坚果云</div>
                <div class="desc">专业的文件同步和共享工具</div>
            </div>
        </div>
    </a>
    <a class="site-card" target="_blank" href="https://space.linkall.com/">
        <div class="info">
            <img src="https://space.linkall.com/favicon.ico"/>
            <div class="content">
                <div class="title">极空间</div>
                <div class="desc">极空间远程访问入口</div>
            </div>
        </div>
    </a>
</div> 