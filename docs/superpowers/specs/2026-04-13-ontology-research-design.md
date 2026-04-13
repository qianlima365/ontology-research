# Ontology Research 知识库设计文档

**日期**: 2026-04-13
**目标**: 建立一个可持续的 ontology 研究知识库，支持 Markdown 为主的内容沉淀，并具备自动化的开源项目/网页分析、图片保存、GitHub 自动提交和 MkDocs 静态站点发布能力。

---

## 1. 项目定位与范围

- **领域覆盖**: 综合方向，包括知识图谱/语义网、哲学本体论、生物医药/金融等行业本体。
- **内容来源**: 开源项目分析、网页文章/论文/文档分析、个人知识整理。
- **输出格式**: 以 Markdown 为主，图片随文档就近存放。
- **发布方式**: GitHub 原生浏览 + MkDocs 静态站点（GitHub Pages）。

## 2. 顶层目录结构

```text
ontology-research/
├── docs/                       # MkDocs 站点根目录 / 文档库
│   ├── index.md               # 首页 + 知识库导航
│   ├── topics/                # 按领域分类的深度研究
│   │   ├── knowledge-graph/
│   │   ├── philosophy/
│   │   ├── bio-ontology/
│   │   └── ...
│   ├── projects/              # 开源项目研究（跨领域）
│   ├── tools/                 # 工具软件研究
│   └── templates/             # Markdown 模板
├── mkdocs.yml                 # MkDocs 配置
├── requirements.txt           # Python 依赖
├── Makefile                   # 常用命令封装
├── README.md
└── .github/workflows/
    └── deploy.yml             # GitHub Pages 自动部署
```

## 3. 文档组织规则

- **单篇报告 = 一个 `.md` 文件 + 一个 `_images/` 目录**。
- 图片与 Markdown 文件同目录存放，例：
  ```text
  docs/projects/neo4j.md
  docs/projects/neo4j_images/
      ├── architecture.png
      └── screenshot.png
  ```
- 新报告生成后，同步更新 `docs/index.md` 中的导航/索引。

## 4. 模板体系

存放在 `docs/templates/`，共三套：

1. `note.md` — 轻量速记（300-800 字）
2. `research.md` — 中等深度（3-5 页，**默认模板**）
3. `deep-dive.md` — 深度报告（5 页以上）

每套模板包含统一的 `meta` 区块（来源 URL、分析日期、标签、领域），不干扰 Markdown 原生渲染。

## 5. 研究工作流

### 5.1 开源项目分析

1. **定位目标**：解析 GitHub/Gitee/其他代码托管平台地址。
2. **信息采集**：通过 `gh` CLI、WebFetch 读取 README、Release 文档、官方文档、核心模块说明。
3. **内容生成**：选择模板，生成 `docs/projects/<name>.md`，图片保存至 `<name>_images/`。
4. **索引更新**：在 `docs/index.md` 和 `docs/projects/` 中更新导航。
5. **自动提交**：执行 `git add → commit → push`。

### 5.2 网页分析

1. **信息采集**：通过 WebFetch 提取页面核心内容。
2. **内容生成**：根据页面类型选择模板和领域目录（`docs/topics/<领域>/`）。
3. **图片保存**：关键图表保存到同目录 `_images/`。
4. **索引更新 + 自动提交**：同上。

### 5.3 异常处理

- 若仓库私有或页面需登录，记录失败原因并继续生成报告框架，同时告知用户。

## 6. MkDocs 静态站点配置

- **主题**: `mkdocs-material`
- **插件**: `search`（内置搜索）
- **导航**: `mkdocs.yml` 中手动配置 `nav`，保证灵活性
- **图片兼容**: 标准 Markdown 图片语法 `![alt](path)` 直接可用
- **部署**: GitHub Actions 工作流 `.github/workflows/deploy.yml`，每次 push 到 `main` 自动部署到 GitHub Pages

## 7. 常用命令（Makefile）

```makefile
serve    # mkdocs serve，本地预览
build    # mkdocs build，本地构建
deploy   # mkdocs gh-deploy，手动部署
```

## 8. 自动提交策略

- 研究报告生成完毕后，自动执行完整提交链：
  ```bash
  git add .
  git commit -m "Add research: <报告标题>"
  git push origin main
  ```
- 提交信息格式统一为 `Add research: <标题>` 或 `Update index: <变更描述>`。
