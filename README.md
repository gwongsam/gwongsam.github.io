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
└── keel/                     Keel 司元 · 复式记账
    └── （同上）
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

- `<head>` 里一段内联脚本在**首帧之前**给 `<html>` 打上 `lang-zh` / `lang-en`，CSS 据此显隐两篇 `<article>`。放在首帧前是为了不闪一下另一种语言。
- 判定优先级：`?lang=zh|en` 查询参数 → `#zh-sN` / `#en-sN` 锚点前缀 → `localStorage` 上次选择 → `navigator.language` → 兜底英文。
- **`localStorage` 的键是全站共用的 `site-lang`**，不是一 App 一个：所有页面在同一个 origin 下，用户选一次语言应当全站生效。
- **没有 JS 时 `<html>` 不带 class，于是两种语言全部显示**（中文在前，英文在后，中间有分隔线）。法律文档在脚本挂掉时必须依然完整可读，这条优先于「不重复」。同理，切换按钮默认带 `hidden`，由 JS 摘掉——没有 JS 就不该出现一个点了没反应的按钮。
- 想直接给出某一语言，在 URL 后加 `?lang=en` 或 `?lang=zh`。

## 本地预览

没有构建步骤，起个静态服务器即可（直接双击打开 `file://` 也行，只是相对路径的目录索引会略有差异）：

```bash
python3 -m http.server 8000
```

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

## App Store Connect 四个字段怎么填

| 字段 | 必填？ | 填什么 |
|---|---|---|
| Support URL（支持 URL） | **必填** | `.../<app>/support/` |
| Marketing URL（营销 URL） | 选填 | `.../<app>/` |
| Privacy Policy URL（隐私政策 URL） | **必填** | `.../<app>/privacy/` |
| EULA（App 信息 › 许可协议） | 选填，但订阅必须可达 | 选「自定 EULA」填 `.../<app>/terms/` |
