# biohacker.fun

biohacker.fun 的站点仓库。当前托管的是 **项目 001 · netadmin-toolbox** 的落地页。

## 站点构成

纯静态，无构建步骤、无依赖：

```
index.html             单页全部内容（内联 SVG 矢量图形，截图用 WebP + PNG 双份）
404.html               错误页（零外部资源依赖，含蝶形矢量）
robots.txt             爬虫策略（屏蔽 .ref/ 与手册原稿）
sitemap.xml            单 URL 站点地图
manifest.webmanifest   PWA 清单
assets/css/site.css    样式（变量 → 版式 → 版块 → 响应 → 降级）
assets/css/fonts.css   自托管字体声明（6 个 @font-face，单值 font-weight）
assets/fonts/*.woff2   自托管字体（9 个文件，343 KB）
assets/js/site.js      交互（无框架，11 个模块，含 reduced-motion 降级）
assets/shots/*.webp    真实产品截图（8 张，WebP + PNG 兜底）
assets/pay-wechat.png  收款码（全站唯一位图之一，360×360，已去白底）
assets/og.png          分享预览图 1200×630
assets/favicon.svg     站点图标
CNAME                  自定义域名 biohacker.fun
```

## 字体

三个家族，全部自托管、静态实例化：

| 族 | 字重 | 文件 |
|---|---|---|
| Baloo 2 | 800 | `baloo2-800.woff2`（11 KB） |
| Noto Sans SC | 400 / 700 / 800 | `notosans-sc-{400,700,800}.woff2`（各 92–94 KB） |
| JetBrains Mono | 400 / 700 | `jetbrains-mono-{400,700}.woff2`（各 15 KB） |

**关键约束**：`@font-face` 必须写**单值** `font-weight`。
早期版本声明 `font-weight: 300 900` 而字体的 fvar 轴是 `100 900`，
浏览器按 CSS Fonts 4 做「声明范围 → 轴范围」归一化，正文 400 实际渲染在
**wght 233** —— 全站中文偏细，且中英不同粗。改为静态字体 + 单值声明后映射恒等。

字形集合按站点实际用字裁剪（702 个字符，含 594 个汉字），三个字重各自覆盖全集，
任何元素在任何字重下都不会掉字。

## 本地预览

字体是自托管的，浏览器在 `file://` 下会用 CORS 拦掉 woff2，**直接双击 index.html 看到的是回退字体**。请用任意静态服务器：

```bash
python -m http.server 8765 --bind 127.0.0.1
# http://127.0.0.1:8765/index.html
```

## 部署

GitHub Pages，`main` 分支根目录，legacy 构建（无 Actions）。
推送到 `main` 即自动发布，通常 30–60 秒后生效。

> 本机若 `github.com` 被网络策略拦截，可用 `.ref/deploy_pages.py` 走 Git Data API 发布，
> 效果等价于一次整树替换。

## 内容维护

- 模块图谱、分组、筛选条由工具的 `--list` 输出生成，改工具后需同步
- 版本号、下载体积、校验和以 GitHub Releases 为准
- 页面不含第三方统计与外部请求

## 对外宣称的口径

- **只对外宣称已发布 Release**。仓库 `main` 分支可能领先于 Release（当前 v2.19 vs v2.17），
  官网不得宣称未发布的版本
- 校验和来自实际下载的 Release 资产，随版本更新

## 开发脚手架

`.ref/` 是开发脚手架（Playwright 截图/审计脚本、字体构建管线、PDF 提取），
已列入 `.gitignore`，不随站点发布。
