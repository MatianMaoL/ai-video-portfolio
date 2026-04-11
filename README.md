# AI 视频作品集
原作者地址：https://github.com/fireshort/feishu-website

基于飞书多维表格 + GitHub Pages 的自动化视频作品集网站。

**在线访问**：https://matianmaol.github.io/ai-video-portfolio/

---

## 架构概览

```
飞书多维表格（CMS）
       │
       ▼  refresh.py（GitHub Actions 定时调用）
  api/videos.json（数据中间层）
       │
       ▼  index.html（前端读取渲染）
  GitHub Pages（用户访问）
```

- **CMS 后端**：飞书多维表格，存储视频附件和元数据
- **数据同步**：GitHub Actions 每 12 小时运行 `refresh.py`，调用飞书 OpenAPI 拉取视频直链
- **前端展示**：纯静态 HTML/CSS/JS，瀑布流卡片布局，无框架依赖

---

## 飞书表格字段

| 字段名 | 类型 | 用途 |
|--------|------|------|
| `序号` | text | 排序 |
| `内容` | text | 卡片标题 |
| `类型` | select | 筛选标签 |
| `描述` | text | 不展示 |
| `日期` | datetime | 不展示 |
| `样本` | attachment | 视频播放 |

---

## 项目文件说明

```
ai-video-portfolio/
├── index.html                  # 前端页面（清新现代风格）
├── refresh.py                  # 飞书数据同步脚本
├── api/
│   └── videos.json             # 视频数据（由 refresh.py 自动生成）
├── .github/workflows/
│   ├── deploy.yml              # GitHub Pages 部署 workflow
│   └── refresh.yml             # 飞书数据刷新 workflow
└── README.md
```

---

## 部署指南（从零开始）

### 1. 创建飞书应用

1. 前往 [飞书开发者后台](https://open.feishu.cn/app/) 创建**企业自建应用**
2. 获取 **App ID** 和 **App Secret**
3. 在应用权限中开通 `bitable:app:readonly`（多维表格只读）
4. 打开目标多维表格 → 右上角 **分享** → 搜索应用名 → 添加为**协作者（可阅读）**

### 2. 创建 GitHub 仓库

```bash
gh repo create ai-video-portfolio --public
```

将项目代码推送至仓库。

### 3. 配置 GitHub Secrets

在仓库 **Settings > Secrets and variables > Actions** 中添加：

| Secret | 值 | 说明 |
|--------|-----|------|
| `LARK_APP_ID` | `cli_xxxxx` | 飞书应用 App ID |
| `LARK_APP_SECRET` | `xxxxx` | 飞书应用 App Secret |
| `LARK_BASE_TOKEN` | `XyhFbpdEyahWZJs2LvTcCjwdn8d` | 多维表格 App Token（网址栏 `/base/` 后的字符串） |
| `LARK_TABLE_ID` | `tblMmpptHwhuEyUH` | 子表 Table ID（网址栏 `?table=` 后的字符串） |

```bash
gh secret set LARK_APP_ID --body "你的App ID"
gh secret set LARK_APP_SECRET --body "你的App Secret"
gh secret set LARK_BASE_TOKEN --body "XyhFbpdEyahWZJs2LvTcCjwdn8d"
gh secret set LARK_TABLE_ID --body "tblMmpptHwhuEyUH"
```

### 4. 开启 GitHub Pages

```bash
gh api repos/MatianMaoL/ai-video-portfolio/pages -X PUT \
  --input '{"build_type":"workflow","source":{"branch":"master","path":"/"}}'
```

推送代码后会自动触发 `deploy.yml` 部署网站。

### 5. 首次数据同步

进入 **Actions > Refresh Videos**，点击 **Run workflow** 手动触发。

---

## 更新机制

### 自动更新（无需操作）

| Workflow | 触发条件 | 作用 |
|----------|---------|------|
| **Refresh Videos** | 每 12 小时自动运行 | 从飞书拉取最新视频数据，更新 `api/videos.json` |
| **Deploy GitHub Pages** | 每次推送到 master | 部署网站到 GitHub Pages |

**完整链路**：飞书表格有变动 → 12 小时内 Refresh Videos 自动运行 → 提交新的 `videos.json` → 触发 Deploy → 网站更新。

### 手动操作

| 你想做什么 | 操作方式 |
|-----------|---------|
| 在飞书新增/修改了视频，想立刻生效 | [Actions](https://github.com/MatianMaoL/ai-video-portfolio/actions) → **Refresh Videos** → **Run workflow** |
| 修改了网站样式（index.html） | 推送代码后自动部署 |
| 本地调试 | `python refresh.py`（需设置环境变量）→ `python -m http.server 8080` → 浏览器打开 `http://localhost:8080` |

---

## 网站功能

- **瀑布流布局**：自动适配不同视频比例，PC 3列 / 平板 2列 / 手机 1列
- **分类筛选**：按类型（广告、艺术、故事、创意动画等）过滤
- **视频播放**：点击卡片播放/暂停，同时只播放一个视频
- **全屏观看**：悬停卡片右上角全屏按钮
- **封面截取**：利用 `#t=0.001` 自动截取第一帧作为封面

---

## 视觉设计

- 背景：`#FAFBFC`（纯白）
- 主色：`#2563EB`（蓝色系）
- 字体：Inter / 系统无衬线
- 卡片：白色背景 + 圆角 16px + 轻阴影
- 大量留白，间距宽松

---

## 技术栈

- HTML5 / CSS3 / ES6（无框架）
- Python 3（飞书 OpenAPI 数据同步）
- GitHub Actions CI/CD
- GitHub Pages 静态托管
