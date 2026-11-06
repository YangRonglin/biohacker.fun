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
assets/css/fonts.css   自托管字体声明（3 个 @font-face，可变字体）
assets/fonts/*.woff2   自托管字体（3 个文件，185 KB）
assets/js/site.js      交互（无框架，11 个模块，含 reduced-motion 降级）
assets/shots/*.webp    真实产品截图（WebP + PNG 兜底，共 162 KB）
assets/pay-wechat.png  收款码（全站唯一位图之一，360×360，已去白底）
assets/og.png          分享预览图 1200×630
assets/favicon.svg     站点图标
CNAME                  自定义域名 biohacker.fun
```

## 字体

三个家族，全部自托管、**每族一份可变字体**：

| 族 | 文件 | 体积 | 轴 |
|---|---|---|---|
| Baloo 2 | `baloo2.woff2` | 13 KB | wght 400–800 |
| Noto Sans SC | `notosans-sc.woff2` | 163 KB | wght 400–800 |
| JetBrains Mono | `jetbrains-mono.woff2` | 9 KB | wght 400–800 |

**两条踩过坑才定下来的约束：**

1. **声明范围必须等于轴范围。**
   浏览器按 CSS Fonts 4 把「请求字重」在「@font-face 声明范围」内归一化，
   再线性映射到「字体 fvar 轴范围」。两者不等长就产生偏移——
   早期版本声明 `300 900` 而 fvar 轴是 `100 900`，正文 400 实际渲染在
   **wght 233**：全站中文偏细，且同段拉丁更细（wght 100），中英不同粗。
   现在声明 `400 800`、轴也是 `400 800`，映射恒等。

2. **一份文件覆盖全部用字，不能按字重拆文件。**
   CSS Fonts 4 的字形回退：某字在匹配到的 @font-face 里缺字形时，
   浏览器沿 font-family 栈往后找，**不会回头找同家族的其它字重**。
   「每个字重一个静态文件」一旦文案改字就会掉字（Windows 下掉到宋体 → 中英跳脸）。
   可变字体没有这个风险。

字形集合按站点实际用字裁剪（723 个字符，含 613 个汉字），拉丁两族裁到 97 个拉丁字符，
它们缺的中文标点由 `--mono` 栈里的 Noto Sans SC 接管。

### 重新生成字体

改动文案后需要重建（新增的汉字必须进字库，否则会掉字）：

```bash
python .ref/collect_chars.py      # 采集当前用字
python .ref/static_weights.py     # 按字重归类
python .ref/build_vf2.py          # 构建三份可变字体
python .ref/verify_vf.py          # 验证字重映射 + 逐字符掉字检查
```

`verify_vf.py` 会逐个字符在 400/700/800 三档下做像素比对，
任何一个字符在三档中任一档画不出来就判定失败。

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
