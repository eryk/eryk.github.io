# Git 提交指南

## 当前状态

项目已完成：
- ✅ 从 Hexo 迁移到 Hugo + Stack 主题
- ✅ 配置文件完整
- ✅ 63 篇文章已迁移
- ✅ 临时文件已清理并归档
- ✅ 项目结构整洁

## 提交步骤

### 1. 处理 Stack 主题

Stack 主题目录包含 `.git` 文件夹，需要先处理：

**选项 A: 将主题作为普通文件提交（推荐）**

```bash
cd ~/Documents/eryk.github.io
rm -rf themes/hugo-theme-stack/.git
git add .
```

**选项 B: 使用 Git Submodule**

```bash
cd ~/Documents/eryk.github.io
rm -rf themes/hugo-theme-stack
git submodule add https://github.com/CaiJimmy/hugo-theme-stack.git themes/hugo-theme-stack
```

### 2. 添加所有文件

```bash
git add .
```

### 3. 查看状态

```bash
git status
```

应该看到：
- 新增的配置文件和内容
- Stack 主题文件
- `.archive/` 目录被 `.gitignore` 忽略（不会提交）

### 4. 创建提交

```bash
git commit -m "迁移到 Hugo + Stack 主题

- 从 Hexo 迁移到 Hugo 静态站点生成器
- 使用 Stack 主题（现代卡片式设计）
- 迁移 63 篇文章，保持原有 URL 结构
- 配置搜索、归档、标签云等功能
- 添加社交链接（GitHub、Twitter、Email）
- 清理临时文件，项目结构整洁

Co-Authored-By: Claude Sonnet 4.5 <noreply@anthropic.com>"
```

### 5. 推送到 GitHub

```bash
git push origin main
```

如果是首次推送：
```bash
git push -u origin main
```

## 预期的文件结构

提交后，GitHub 仓库应该包含：

```
eryk.github.io/
├── .github/
│   └── workflows/
│       └── deploy.yml          # GitHub Actions 部署配置
├── assets/
│   ├── css/
│   └── icons/
│       └── mail.svg            # 自定义邮件图标
├── content/
│   ├── posts/                  # 63 篇文章
│   └── page/                   # 归档、搜索、关于页面
├── static/
│   └── img/
├── themes/
│   └── hugo-theme-stack/       # Stack 主题
├── .gitignore
├── README.md
└── hugo.yaml                    # Hugo 配置文件
```

## 不会提交的内容

以下内容被 `.gitignore` 忽略，不会提交到 Git：

- `.archive/` - 归档目录（包含备份和临时文档）
- `public/` - Hugo 生成的静态文件
- `.hugo_build.lock` - Hugo 构建锁文件
- `.DS_Store` - macOS 系统文件

## 验证部署

提交后，GitHub Actions 会自动构建和部署：

1. 访问仓库的 Actions 标签页
2. 查看 "Deploy Hugo site to Pages" 工作流
3. 等待构建完成（约 1-2 分钟）
4. 访问 https://eryk.github.io 查看效果

## 常见问题

### Q: 主题文件太大，提交很慢怎么办？

A: 使用 Git Submodule 方式（选项 B），只提交主题引用而非全部文件。

### Q: 如何更新主题？

**普通方式：**
```bash
cd themes/hugo-theme-stack
git pull
cd ../..
git add themes/hugo-theme-stack
git commit -m "更新 Stack 主题"
```

**Submodule 方式：**
```bash
git submodule update --remote themes/hugo-theme-stack
git commit -am "更新 Stack 主题"
```

### Q: 需要恢复 Hexo 备份怎么办？

备份在 `.archive/backup/hexo-backup/`，本地保留但不会提交到 Git。

### Q: 首次部署失败怎么办？

1. 检查仓库设置 → Pages → Source 是否设置为 "GitHub Actions"
2. 确认 `.github/workflows/deploy.yml` 文件存在
3. 查看 Actions 页面的错误日志

## 后续维护

### 创建新文章

```bash
hugo new content/posts/my-new-post.md
```

### 本地预览

```bash
hugo server
```

### 提交更新

```bash
git add .
git commit -m "更新内容"
git push
```

GitHub Actions 会自动部署更新。

## 完成！

按照以上步骤提交后，您的博客就成功迁移到 Hugo + Stack 主题了！
