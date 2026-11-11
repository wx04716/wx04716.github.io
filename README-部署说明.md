# AI 自习室 · 作品集站点：结构与部署说明

这个文件夹就是完整的网站根目录，2.2 MB，纯静态，没有任何构建步骤，
双击 `index.html` 就能本地看，也可以原样拖到任何静态托管上。

---

## 一、为什么不能只放这两个文件

`.rp` 是 Axure 的**工程二进制**（LZ4 压缩的对象图），不是网页。
浏览器没有能解析它的渲染器，双击只会变成下载，所以「把 .rp 当前端页面」这条路本身不成立。

Axure 里真正能上网页的，是「发布 → 生成 HTML 文件」产出的那一堆
`start.html + resources/ + data/`。而用户下载 `.rp` 的需求又确实存在（HR 或面试官想自己改）。

所以拆成三层，各自解决一件事：

| 层 | 文件 | 解决什么 |
|---|---|---|
| 作品集 | `index.html` | 讲清楚「为什么这么设计」，18 章文档 |
| 可交互演示 | `prototype.html` | 让评审在浏览器里直接点，不用装 Axure |
| 源文件下载 | `download/*.rp` | 给需要二次编辑的人 |

`prototype.html` 不是录屏、不是 GIF，是 8 张 375×812 的真实截图 + 43 个按原坐标摆的透明热区，
点「开始专注」会真的切到 P2，点「记一笔」会真的弹出提示条——效果和 `.rp` 里那套交互一致。

---

## 二、目录结构

```
AI自习室-作品集-站点/
├── index.html            作品集（18 章，单文件自包含，无外部依赖）
├── prototype.html        8 屏可交互原型演示
├── assets/
│   ├── screens/p1..p8.png   8 屏 750×1624（2 倍图）
│   └── overview.png         8 屏全览长图
└── download/
    ├── ai-studyroom-prototype-ix.rp       可交互版（8 页 / 529 元件 / 48 交互）
    └── ai-studyroom-prototype-static.rp   纯静态版（8 页 / 463 元件 / 0 交互）
```

文件名用英文是刻意的：中文文件名在部分 CDN / 服务器上会被二次编码，下载链接容易 404。
但页面上给的是 `download="AI自习室-原型-可交互-8屏.rp"`，**用户存到本地还是中文名**。

---

## 三、两个入口怎么走

```
index.html（作品集）
   └─ 十六、关键页面原型  →  [打开可交互原型 →]  →  prototype.html
   └─ 右下角常驻           →  [▶ 在线原型]      →  prototype.html
   └─ 原型章节 / 页脚      →  [下载 Axure 源文件] →  download/*.rp

prototype.html（演示）
   └─ 左上 [← 返回作品集]  →  index.html#sec-proto（回到原型那一章）
```

作品集原型章节顶部加了一条浅蓝提示条（说明网页版能点，下面是静态版面图），
右下角加了一个常驻圆形按钮。这两块是**唯一**对原作品集做过的改动，
样式写在 `</style>` 前最后一段，删掉那一段就恢复成纯静态作品集。

---

## 四、以后怎么更新

### 只改作品集文字 / 章节

直接改 `index.html`。它是单文件，CSS 和 JS 全在内部，不依赖任何外链。
注意：原型章节里那条提示条和右下角按钮是人工加的，改结构时别误删。

### 改了 8 屏原型（重画了 HTML 里的手机）

1. 重新导出 8 张 375×812 的 2 倍图，覆盖 `assets/screens/p1..p8.png`
2. 如果某块热区位置变了，改 `prototype.html` 里 `SCREENS` 数组对应那条的 `x / y / w / h`
   （坐标系就是 375×812 的屏幕坐标，和 `.rp` 里用的是同一套数字）
3. 重新上传

### 想换成 Axure 原生版（交互 100% 一致）

在 Axure 里打开 `.rp`，「发布 → 生成 HTML 文件」，输出到本目录下新建的 `axure/` 文件夹，
然后把 `prototype.html` 顶部按钮改成指向 `axure/start.html` 即可。
`prototype.html` 现在这版的好处是不用等 Axure 加载、手机上也能点，适合初筛；
Axure 原生版适合进了面试之后再让人细看。

---

## 五、部署方式

### 方式 A：WorkBuddy 托管（备线）

```
https://ai-studyroom-portfolio.app.workbuddy.host/?sharecode=KAXOW6FQ2R
```

改动后重新发布即可覆盖同一个链接。注意**必须带完整的 `?sharecode=` 参数**，
只复制域名会返回 403。这条作为备线保留：国内访问稳定，但地址较长且带参数，
简历上建议用方式 B 的短地址。

### 方式 B：GitHub Pages（已上线）

作品集已发布在：

```
https://wx04716.github.io/
```

- 仓库：`https://github.com/wx04716/wx04716.github.io`
- 分支 `main`，发布目录 `/ (root)`
- 仓库名与账号名一致（`wx04716.github.io`），属于用户主页站点，所以地址没有子路径

内容改动后这样更新，1-2 分钟自动重新发布，地址不变：

```bash
cd AI自习室-作品集-站点
git add .
git commit -m "更新说明"
git push
```

**推送走 SSH，不依赖网页。** 本机访问 `github.com` 的 443 端口时通时断，但 22 端口稳定，
所以仓库已配置 `core.sshCommand` 指向 `C:\Users\Public\github-ssh\` 下的密钥
（放在这里是因为中文用户名会让 SSH 按 GBK 解析路径、读不到 `~/.ssh`）。
网页打不开时推送照常可用。

两个注意点：

1. **`.rp` 单文件 180 KB，GitHub 完全放得下**，但如果以后换成几百 MB 的视频，建议改用 Release 附件。
2. 仓库根目录不要再放 `.nojekyll` 之外的私有文件，Pages 是公开的，`_备份` 那类文件会被别人翻到。

### 方式 C：本地看

直接双击 `index.html`。`prototype.html` 里的热区是相对路径，`file://` 协议下同样能用，
不需要起服务。

---

## 六、当前线上状态

已实测（2026-11-11 09:48，`https://wx04716.github.io/`）：

| 检查项 | 结果 |
|---|---|
| 首页 | 200，228,445 字节 |
| `/prototype.html` | 200，21,721 字节 |
| `/assets/overview.png` + 8 张屏幕图 | 全部 200，字节数与本地逐一一致 |
| `/download/*.rp` 两个工程 | 200，`application/octet-stream`，可正常下载 |
| 中文文件名 `README-部署说明.md` | 200，未被 Jekyll 处理掉（`.nojekyll` 生效） |
| 390px 手机宽度 | 文档宽度 = 视口宽度，无横向溢出 |

线上 HTML 比本地小 3,002 / 504 字节，是 `core.autocrlf=true` 把 CRLF 转成 LF 造成的，
差值正好等于行数，不影响渲染。

- 首页：作品集 18 章，8 屏原型图
- `/prototype.html`：8 屏可交互演示，43 个热区、7 个提示态
- `/download/*.rp`：两个 Axure 工程可下载
