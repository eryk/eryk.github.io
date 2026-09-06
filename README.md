# Eryk's Blog

基于 Hugo + Paper 主题的个人博客。

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

### Obsidian 周记

使用固定模板创建草稿：

```bash
hugo new content --kind weekly-notes posts/YYYY-MM-DD-weekly-notes.md
```

模板在 `archetypes/weekly-notes.md`，每次整理遵循以下格式：

- 按北京时间周一至周日的收录记录整理，合并重复主题，不把旧文写成当周新闻。
- 正文用“日期范围＋分类列表”，选 5–8 条，内容少时可以更少；删除空分类，不写开场铺垫和重复总结。
- 每条采用“**主题**：一句话总结”，通常 30–60 字，只留一个核心信息，正文以 300–500 字为宜。
- 外部资料把来源链接嵌入句子，区分作者观点、个人归纳和实际验证；不补写未经记录的经历或结论。
- “本周提醒”最多留一个具体动作；替换所有占位文字、示例链接和文章描述，保留 `draft: true`，确认后再发布。

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
├── layouts/          # 归档、搜索、分类与搜索索引模板
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
- **菜单**: menu.main（搜索、归档、分类、关于、邮箱）
- **主页简介**: params.name 和 params.bio
- **社交链接**: params.github、params.twitter 和 params.rss

详细配置请参考 [Paper 主题文档](https://github.com/nanxiaobei/hugo-paper)

## 关于

- **创建日期**: 2026-01-20
- **文章数量**: 63 篇
- **历史记录**: 迁移文档和备份保存在 `.archive/` 目录

## 联系方式

- GitHub: [@eryk](https://github.com/eryk)
- Twitter: [@eryk_xu](https://x.com/eryk_xu)
- Email: xuqi86@gmail.com

## License

内容版权归作者所有。
