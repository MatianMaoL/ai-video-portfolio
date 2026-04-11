# AI 视频作品集
原作者地址：https://github.com/fireshort/feishu-website
基于飞书多维表格 + GitHub Pages 的自动化视频作品集网站。

## 功能

- 从飞书多维表格自动拉取视频数据
- 清新现代的响应式卡片布局
- 按类型筛选视频
- 点击播放、全屏观看
- 每 12 小时自动刷新视频链接

## 部署

### 1. 配置飞书应用

1. 在[飞书开发者后台](https://open.feishu.cn/app/)创建企业自建应用，获取 **App ID** 和 **App Secret**
2. 确保应用有飞书多维表格的读取权限
3. 在目标多维表格中，将应用添加为**协作者（可阅读）**

### 2. 配置 GitHub Secrets

在仓库 **Settings > Secrets and variables > Actions** 中添加：

| Secret | 说明 |
|--------|------|
| `LARK_APP_ID` | 飞书应用 App ID |
| `LARK_APP_SECRET` | 飞书应用 App Secret |
| `LARK_BASE_TOKEN` | 多维表格 App Token |
| `LARK_TABLE_ID` | 子表 Table ID |

### 3. 开启 GitHub Pages

**Settings > Pages**，源选择 `Deploy from a branch`，分支选 `main`。

### 4. 首次同步

进入 **Actions > Auto Refresh Feishu Videos**，点击 **Run workflow** 手动触发首次数据同步。

## 技术栈

- HTML5 / CSS3 / ES6
- Python 3（飞书 OpenAPI 数据同步）
- GitHub Actions CI/CD
