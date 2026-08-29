# Eryk's Blog

基于 Hugo + Paper 主题的技术博客。

## 快速开始

### 本地预览

```bash
hugo server
```

访问 http://localhost:1313

### 创建新文章

```bash
hugo new content/posts/my-new-post.md
```

### 部署

推送到 GitHub 后自动部署：

```bash
git add .
git commit -m "更新内容"
git push
```

## 项目结构

```
.
├── content/           # 内容目录
│   ├── posts/        # 博客文章
│   ├── about.md      # 关于页
│   ├── archives.md   # 归档页
│   └── search.md     # 搜索页
├── static/           # 静态资源
├── assets/           # 站点自定义样式
├── layouts/          # 归档、搜索与搜索索引模板
├── themes/           # 主题目录
│   └── paper/       # Paper Git 子模块
└── hugo.yaml         # 配置文件
```

## 技术栈

- **静态站点生成器**: [Hugo](https://gohugo.io/)
- **主题**: [Paper](https://github.com/nanxiaobei/hugo-paper)
- **部署**: Cloudflare Pages、GitHub Pages
- **CI/CD**: Cloudflare Pages Git 集成、GitHub Actions

## 主要功能

- 📝 Markdown 写作
- 🔍 全站搜索
- 🏷️ 标签和分类
- 📚 按年份归档
- 🌓 明暗主题切换
- 📱 响应式设计
- ⚡ 快速加载

## 配置说明

主要配置在 `hugo.yaml` 中：

- **站点信息**: baseURL, title, description
- **菜单**: menu.main（搜索、归档、关于、邮箱）
- **主页简介**: params.name 和 params.bio
- **社交链接**: params.github、params.twitter 和 params.rss

详细配置请参考 [Paper 主题文档](https://github.com/nanxiaobei/hugo-paper)

## 关于

- **创建日期**: 2026-01-20
- **文章数量**: 63 篇
- **历史记录**: 迁移文档和备份保存在 `.archive/` 目录

## 联系方式

- GitHub: [@xuqi86](https://github.com/xuqi86)
- Twitter: [@eryk_xu](https://x.com/eryk_xu)
- Email: xuqi86@gmail.com

## License

内容版权归作者所有。
