# gwongsam.github.io · 各 App 的官网与法律条款

一个仓库装下所有 App 的**产品页、支持页、服务条款与隐私政策**，通过 GitHub Pages 托管。
每个 App 一个顶层目录，四个页面同时充当 App Store Connect 需要填的四个 URL。

**每个页面一个 URL，中英双语同页可切换**——App Store Connect 的每个字段只有一个地址槽位，
分成两套语言地址只会逼出「该填哪一版」的问题。

纯静态 HTML + 一份共用 CSS + 一小段内联 JS，无构建步骤、无外部依赖、无字体请求。

## 目录结构

```
.
├── index.html                作品集落地页，列出各 App
├── assets/
│   └── legal.css             全站唯一样式表（含深色模式与打印样式），所有 App 共用
├── cairn/                    Cairn 司候 · 倒数与纪念日
│   ├── index.html            产品介绍（= Marketing URL）
│   ├── support/index.html    支持与常见问题（= Support URL）
│   ├── terms/index.html      服务条款 / Terms of Service
│   ├── privacy/index.html    隐私政策 / Privacy Policy
│   └── logo.svg              品牌图标，同时用作 favicon / 页头 / hero
├── keel/                     Keel 司元 · 复式记账
│   └── （同上）
└── rayloom/                  Rayloom 织光 · Mac 照片与视频工作台
    ├── index.html · support/ · privacy/ · logo.svg
    └── （没有 terms/：App 里「用户协议」链的是 Apple 标准 EULA，ASC 许可协议也选标准 EULA）
```

**共用的只有 `assets/legal.css`，品牌资源一 App 一份。**样式该统一，品牌不该。
反过来说，改 `legal.css` 会同时影响所有 App 的四个页面，改之前想清楚。

**页面之间全部使用相对路径**，所以整棵 `<app>/` 子树可以整体挪位置而不用改任何链接。

## 加一个新 App

复制一份现成的 `<app>/` 目录，然后：

1. 换掉 `logo.svg`；
2. 四个页面的 `<title>` / `<meta description>` / 页头品牌名 / 页脚；
3. **隐私政策与服务条款必须逐条对着这个 App 的真实行为重写**——它收哪些数据、要哪些系统权限、
   有没有内购、备份加不加密。照抄另一个 App 的隐私政策是审核风险，也是对用户的谎。

`assets/legal.css` 不用动，相对路径的层数（`../assets/` 与 `../../assets/`）也不用动。

## 语言切换是怎么工作的

- `<head>` 里一段内联脚本在**首帧之前**给 `<html>` 打上 `lang-<code>`，CSS 据此显隐各语言的 `<article>`。放在首帧前是为了不闪一下另一种语言。
- 大多数 App 只有中英两种（`lang-zh` / `lang-en`，页头一个切换按钮）。**Keel 有七种**：`zh` / `en` / `de` / `es` / `fr` / `pt`（巴西葡萄牙语）/ `it`，页头是一个原生 `<select>`（`.lang-switch`）。`legal.css` 里多语言的规则只做加法，两种语言的页面不受影响。
- 判定优先级：`?lang=<code>` 查询参数（同时记进 `localStorage`）→ `#<code>-sN` 锚点前缀 → `localStorage` 上次选择 → 浏览器语言偏好（`navigator.languages` 依次取第一个支持的）→ 兜底英文。中英两种的页面仍是旧脚本：`?lang=zh|en` → 锚点 → `localStorage` → `navigator.language`。
- **`localStorage` 的键是全站共用的 `site-lang`**，不是一 App 一个：所有页面在同一个 origin 下，用户选一次语言应当全站生效。只认中英的页面读到 `de` 之类的值会退回按浏览器语言判定。
- **没有 JS 时 `<html>` 不带 class，于是所有语言全部显示**（中文在前，其余依次排在后面，篇与篇之间有分隔线）。法律文档在脚本挂掉时必须依然完整可读，这条优先于「不重复」。行内标签（导航、页脚）无 JS 时只留中文。同理，切换控件默认带 `hidden`，由 JS 摘掉——没有 JS 就不该出现一个点了没反应的控件。
- 各语言的章节 id 带语言前缀（`zh-s3` / `de-s3`），编号一一对应：切换语言时停在同一条靠的就是它。表格要多语就整张复制。
- 想直接给出某一语言，在 URL 后加 `?lang=<code>`。

## 本地预览

没有构建步骤，起个静态服务器即可（直接双击打开 `file://` 也行，只是相对路径的目录索引会略有差异）：

```bash
python3 -m http.server 8000
```

## Keel 的图文教程（`keel/guides/`）

快捷指令配方的设置步骤放在网页上，不画在 App 里：系统界面一改（iOS 27 就把「自动化」挪进了快捷指令本身），
网页当天能改，App 里的示意要等发版。App 只留「添加到快捷指令」按钮和一条「查看图文步骤」链接。

```
keel/guides/
├── guide.css                 教程页的增量样式，配色全取 legal.css 的变量
├── img/                      模拟器实拍截图，裁好后转 WebP（宽 720）
└── doubao-message/index.html 动账短信用豆包识别
```

- **一个配方一页，地址里不带语言**。目前只有豆包配方有教程，页面只有中文（豆包只面向中文用户），所以没有语言切换。
- **系统版本靠 `?ios=26|27` 选**：App 打开时带上它，页面直接停在那一版的步骤；不带就默认 27，页上也能手动切。
  切换用两个单选框 + CSS，无 JS 也能用。
- **截图只用真机界面**：在对应版本的模拟器里把流程走一遍截下来，不画示意图。截图里的银行号码、金额都是示例。
- **App 里链的是 `https://site.gwongsam.net/keel/guides/…`，不是 github.io**（只写在 Keel 仓库 `QuickCaptureGuide.baseURLString` 一处）。
  github.io 在国内时通时不通，所以整个仓库另部署到腾讯云 EdgeOne Makers 国际站（项目 `gwongsam-site`，「全球可用区（不含中国大陆）」，
  免备案），连本仓库 main 分支，**推一次两边同时部署**。EdgeOne 自带的 `*.edgeone.dev` 在国内一律 401，只有自定义域名
  `site.gwongsam.net` 国内外都能开：Cloudflare 上 CNAME `site` → `site.gwongsam.net.pages.dnsoe7.com`（必须 DNS only）、
  TXT `edgeonereclaim.site`（归属权验证），HTTPS 用 EdgeOne 免费证书。

## GitHub Pages 设置

仓库名就是 `gwongsam.github.io`（用户站），**Settings → Pages** → Source 选 **Deploy from a branch** → 分支 `main`、目录 `/ (root)`。站点在根路径提供服务，因此 `keel/` 子目录的地址与仓库改名前的项目站地址完全一致。

## 上线后的地址

| App | 页面 | URL |
|---|---|---|
| — | 作品集落地页 | `https://gwongsam.github.io/` |
| Cairn | 首页（产品介绍） | `https://gwongsam.github.io/cairn/` |
| Cairn | 支持与常见问题 | `https://gwongsam.github.io/cairn/support/` |
| Cairn | 服务条款 | `https://gwongsam.github.io/cairn/terms/` |
| Cairn | 隐私政策 | `https://gwongsam.github.io/cairn/privacy/` |
| Keel | 首页（产品介绍） | `https://gwongsam.github.io/keel/` |
| Keel | 支持与常见问题 | `https://gwongsam.github.io/keel/support/` |
| Keel | 服务条款 | `https://gwongsam.github.io/keel/terms/` |
| Keel | 隐私政策 | `https://gwongsam.github.io/keel/privacy/` |
| Keel | 教程：动账短信用豆包识别 | `https://gwongsam.github.io/keel/guides/doubao-message/` |
| Rayloom | 首页（产品介绍） | `https://gwongsam.github.io/rayloom/` |
| Rayloom | 支持与常见问题 | `https://gwongsam.github.io/rayloom/support/` |
| Rayloom | 隐私政策 | `https://gwongsam.github.io/rayloom/privacy/` |

## App Store Connect 四个字段怎么填

| 字段 | 必填？ | 填什么 |
|---|---|---|
| Support URL（支持 URL） | **必填** | `.../<app>/support/` |
| Marketing URL（营销 URL） | 选填 | `.../<app>/` |
| Privacy Policy URL（隐私政策 URL） | **必填** | `.../<app>/privacy/` |
| EULA（App 信息 › 许可协议） | 选填，但订阅必须可达 | 选「自定 EULA」填 `.../<app>/terms/` |
