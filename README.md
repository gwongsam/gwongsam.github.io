# Keel 司元 · 法律条款站点

Keel（中文名「司元」）iOS 记账应用的**服务条款**与**隐私政策**，中英双语，通过 GitHub Pages 托管。

纯静态 HTML + 一份 CSS，无构建步骤、无外部依赖、无字体/脚本请求。

## 目录结构

```
.
├── index.html            落地页：四份文档的入口（中/英 × 条款/隐私）
├── assets/
│   ├── legal.css         全站唯一样式表（含深色模式与打印样式）
│   └── favicon.svg
├── zh/
│   ├── terms/index.html  服务条款（EULA）
│   └── privacy/index.html 隐私政策
└── en/
    ├── terms/index.html  Terms of Service
    └── privacy/index.html Privacy Policy
```

**页面之间全部使用相对路径**，所以站点放在 `user.github.io/keel/` 子路径下、或将来挂到自定义域名根路径下，都无需修改任何链接。

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
| 服务条款（中） | `https://gwongsam.github.io/keel/zh/terms/` |
| 隐私政策（中） | `https://gwongsam.github.io/keel/zh/privacy/` |
| Terms of Service | `https://gwongsam.github.io/keel/en/terms/` |
| Privacy Policy | `https://gwongsam.github.io/keel/en/privacy/` |

## 上线后必须同步的三处

1. **App 代码**：`BalanceFlow/Sources/Shared/Legal/LegalLinks.swift` 里目前是占位地址 `https://balanceflow.app/legal/...`，替换成上表中的真实 URL（该文件是应用内两个消费方——设置页与付费页页脚——的唯一定义点）。
2. **App Store Connect → App 隐私**：隐私政策 URL 填英文版地址。
3. **App Store Connect → App 信息**：EULA 选「自定 EULA」并填服务条款地址（订阅类购买必须两者都可达，否则触发 3.1.2 拒审）。

## 将来换自定义域名

1. 在仓库根目录新建 `CNAME` 文件，内容是裸域名（如 `keel.app`，不带 `https://` 和路径）；
2. 在域名商处配置 DNS（`A` 记录指向 GitHub Pages 的 IP，或 `CNAME` 指向 `gwongsam.github.io`）；
3. Settings → Pages → Custom domain 填入域名并勾选 Enforce HTTPS。

站内链接是相对路径，**不需要改动任何页面**；但上表中已经对外公布过的旧 URL 要记得同步更新（App 代码 + App Store Connect 两处）。

## 修改内容时

- 改动正文后，记得更新页面顶部 `.doc-meta` 里的「最后更新」日期，中英两版一起改。
- 中英文两版是镜像关系，章节编号（`#s1`…`#s16`）一一对应，跨文档锚点（如隐私政策引用条款第 6 条）依赖这套编号，调整章节顺序时要一并检查。
- 两份文档均声明「中英文歧义以中文版为准」。
