# Keel 司元 · 法律条款站点

Keel（中文名「司元」）iOS 记账应用的**服务条款**与**隐私政策**，通过 GitHub Pages 托管。

**每篇文档一个 URL，中英双语同页可切换**——App Store Connect 的隐私政策 URL、自定 EULA、
App 内入口都只有一个地址槽位，分成两套语言地址只会逼出「该填哪一版」的问题。

纯静态 HTML + 一份 CSS + 一小段内联 JS，无构建步骤、无外部依赖、无字体请求。

## 目录结构

```
.
├── index.html            落地页：两篇文档入口
├── assets/
│   ├── legal.css         全站唯一样式表（含深色模式与打印样式）
│   └── favicon.svg
├── terms/index.html      服务条款 / Terms of Service（中英同页）
└── privacy/index.html    隐私政策 / Privacy Policy（中英同页）
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

| 文档 | URL |
|---|---|
| 落地页 | `https://gwongsam.github.io/keel/` |
| 服务条款 / Terms of Service | `https://gwongsam.github.io/keel/terms/` |
| 隐私政策 / Privacy Policy | `https://gwongsam.github.io/keel/privacy/` |

## 上线后必须同步的三处

1. **App 代码**：`BalanceFlow/Sources/Shared/Legal/LegalLinks.swift` 里目前是占位地址 `https://balanceflow.app/legal/...`，替换成上表中的真实 URL（该文件是应用内两个消费方——设置页与付费页页脚——的唯一定义点）。双语同页，所以**不需要按 App 语言分叉 URL**；真想跟随应用内语言，加 `?lang=` 后缀即可。
2. **App Store Connect → App 隐私**：隐私政策 URL 填 `.../privacy/`。
3. **App Store Connect → App 信息**：EULA 选「自定 EULA」并填 `.../terms/`（订阅类购买必须两者都可达，否则触发 3.1.2 拒审）。

## 将来换自定义域名

1. 在仓库根目录新建 `CNAME` 文件，内容是裸域名（如 `keel.app`，不带 `https://` 和路径）；
2. 在域名商处配置 DNS（`A` 记录指向 GitHub Pages 的 IP，或 `CNAME` 指向 `gwongsam.github.io`）；
3. Settings → Pages → Custom domain 填入域名并勾选 Enforce HTTPS。

站内链接是相对路径，**不需要改动任何页面**；但上表中已经对外公布过的旧 URL 要记得同步更新（App 代码 + App Store Connect 两处）。

## 修改内容时

- 改动正文后，记得更新该语言 `.doc-meta` 里的「最后更新」日期，**中英两版一起改**。
- 中英两版是镜像关系，章节 id 为 `zh-s1`…`zh-s16` 与 `en-s1`…`en-s16`，**编号一一对应**：跨文档锚点（隐私政策引用条款 §3/§6）依赖它，切换语言时「停在同一条」也依赖它。调整章节顺序要两版同步改。
- 两份文档均声明「中英文歧义以中文版为准」。
