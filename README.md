# Keel 司元 · 官网与法律条款

Keel（中文名「司元」）iOS 记账应用的**产品页、支持页、服务条款与隐私政策**，通过 GitHub Pages 托管。
四个页面同时充当 App Store Connect 需要填的四个 URL（见下）。

**每个页面一个 URL，中英双语同页可切换**——App Store Connect 的每个字段只有一个地址槽位，
分成两套语言地址只会逼出「该填哪一版」的问题。

纯静态 HTML + 一份 CSS + 一小段内联 JS，无构建步骤、无外部依赖、无字体请求。

## 目录结构

```
.
├── index.html            首页：产品介绍（= Marketing URL）
├── support/index.html    支持与常见问题（= Support URL）
├── terms/index.html      服务条款 / Terms of Service
├── privacy/index.html    隐私政策 / Privacy Policy
└── assets/
    ├── legal.css         全站唯一样式表（含深色模式与打印样式）
    └── logo.svg          品牌图标，同时用作 favicon / 页头 / hero
```

**页面之间全部使用相对路径**，所以站点放在 `user.github.io/keel/` 子路径下、或将来挂到自定义域名根路径下，都无需修改任何链接。

## 语言切换是怎么工作的

- `<head>` 里一段内联脚本在**首帧之前**给 `<html>` 打上 `lang-zh` / `lang-en`，CSS 据此显隐两篇 `<article>`。放在首帧前是为了不闪一下另一种语言。
- 判定优先级：`?lang=zh|en` 查询参数 → `#zh-sN` / `#en-sN` 锚点前缀 → `localStorage` 上次选择 → `navigator.language` → 兜底英文。
- **没有 JS 时 `<html>` 不带 class，于是两种语言全部显示**（中文在前，英文在后，中间有分隔线）。法律文档在脚本挂掉时必须依然完整可读，这条优先于「不重复」。同理，切换按钮默认带 `hidden`，由 JS 摘掉——没有 JS 就不该出现一个点了没反应的按钮。
- 想直接给出某一语言，在 URL 后加 `?lang=en` 或 `?lang=zh`。

## 推送与启用 GitHub Pages

远程仓库还没建。建好之后：

```bash
git remote add origin https://github.com/gwongsam/keel.git
git push -u origin main
```

然后在 GitHub 仓库页面 → **Settings → Pages** → Source 选 **Deploy from a branch** → 分支 `main`、目录 `/ (root)` → Save。等待约 1 分钟部署完成。

## 上线后的地址

| 页面 | URL |
|---|---|
| 首页（产品介绍） | `https://gwongsam.github.io/keel/` |
| 支持与常见问题 | `https://gwongsam.github.io/keel/support/` |
| 服务条款 / Terms of Service | `https://gwongsam.github.io/keel/terms/` |
| 隐私政策 / Privacy Policy | `https://gwongsam.github.io/keel/privacy/` |

## App Store Connect 四个字段怎么填

| 字段 | 必填？ | 填什么 |
|---|---|---|
| Support URL（支持 URL） | **必填** | `.../support/` |
| Marketing URL（营销 URL） | 选填 | `.../` |
| Privacy Policy URL（隐私政策 URL） | **必填** | `.../privacy/` |
| EULA（App 信息 › 许可协议） | 选填，但订阅必须可达 | 选「自定 EULA」填 `.../terms/` |

不填自定 EULA 会套用 Apple 标准 EULA；但订阅类购买要求条款与隐私政策在购买页可达（审核指南 3.1.2），
所以这里填自定的更省事。

## 上线后还要同步 App 代码

`BalanceFlow/Sources/Shared/Legal/LegalLinks.swift` 里目前是占位地址 `https://balanceflow.app/legal/...`，
替换成上表中的真实 URL（该文件是应用内两个消费方——设置页与付费页页脚——的唯一定义点）。
双语同页，所以**不需要按 App 语言分叉 URL**；真想跟随应用内语言，加 `?lang=zh|en` 后缀即可。

## 将来换自定义域名

1. 在仓库根目录新建 `CNAME` 文件，内容是裸域名（如 `keel.app`，不带 `https://` 和路径）；
2. 在域名商处配置 DNS（`A` 记录指向 GitHub Pages 的 IP，或 `CNAME` 指向 `gwongsam.github.io`）；
3. Settings → Pages → Custom domain 填入域名并勾选 Enforce HTTPS。

站内链接是相对路径，**不需要改动任何页面**；但上表中已经对外公布过的旧 URL 要记得同步更新（App 代码 + App Store Connect 两处）。

## 修改内容时

- 改动正文后，记得更新该语言 `.doc-meta` 里的「最后更新」日期，**中英两版一起改**。
- 中英两版是镜像关系，章节 id 为 `zh-s1`…`zh-s16` 与 `en-s1`…`en-s16`，**编号一一对应**：跨文档锚点（隐私政策引用条款 §3/§6）依赖它，切换语言时「停在同一条」也依赖它。调整章节顺序要两版同步改。
- 两份法律文档均声明「中英文歧义以中文版为准」。
- **块级元素**（`article` / `p` / `h2` / `div` / `table-wrap`）带 `data-lang` 时，无 JS 会两种语言都显示；
  **`<span>` 行内标签**带 `data-lang` 时只显示中文——因为 inline 元素会把两种语言黏成一串。加新内容时按这个区分选标签。
- 表格若要双语，**必须整张表复制两份**并各带 `data-lang`。在一行里塞 4 个 `<td>` 配 2 个 `<th>`，无 JS 时表会塌掉。
- 首页 `<body class="home">` 会把 `main` 放宽到 54rem 让三张卡排一行，正文段落仍限制在 43rem 阅读行宽。
- 首页与支持页的功能清单必须与 App 实际情况一致：会员功能的权威来源是代码里的 `paywall.context.*` 墙位，
  改了付费墙要回来同步这里。
