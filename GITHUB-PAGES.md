# 把 agent-chat 发布成 GitHub Pages 网页

## 先说结论

GitHub 仓库里的 `.html` 文件，直接点开只会显示源码，不会自动变成网页。

要得到可访问的网址，必须开启 **GitHub Pages**，并且发布目录的根目录里必须有一个 **`index.html`**。

这个工作区的根目录原本已有一个 `index.html`，那是另一个「字卡交流」页面。所以：

- 把整个工作区上传后直接选 `/(root)`，打开的是旧页面，不是 `agent-chat.html`。
- 只上传 `agent-chat.html`，根目录没有 `index.html`，打开会 404。
- 只访问 `/agent-chat.html` 可以打开文件，但仓库首页不会自动跳到它。

我已经准备了独立的发布目录：

```text
docs/
├─ index.html   ← agent-chat.html 的完整副本
└─ .nojekyll    ← 告诉 GitHub Pages 不要用 Jekyll 处理
```

原来的 `index.html` 和 `agent-chat.html` 都没有改动。

---

## 推荐：新建一个专门放这个网页的仓库

这样最干净，不会和「字卡交流」混在一起。

1. 在 GitHub 新建一个 **Public** 仓库，例如 `agent-chat`。
   - 免费账号的 GitHub Pages 只支持公开仓库；私有仓库需要 GitHub Pro/Team 等付费方案。
2. 打开本机的 `docs` 文件夹。
3. 把 `docs` 里的 **`index.html`** 和 **`.nojekyll`** 上传到新仓库的 **根目录**。
   - 注意：要上传这两个文件的**内容**，不是把 `docs` 文件夹本身套在仓库里。
   - Windows 资源管理器默认可能隐藏以点开头的文件。先在「查看」里勾选「隐藏的项目」，确认 `.nojekyll` 也被上传。
   - 如果漏掉 `.nojekyll` 通常也能打开，但建议一起上传。
4. 进入仓库的 **Settings → Pages**。
5. 在 **Build and deployment** 里：
   - Source 选 **Deploy from a branch**
   - Branch 选 **main**（或你的默认分支）
   - Folder 选 **/(root)**
   - 点 **Save**
6. 等待 1–3 分钟。页面顶部会出现站点地址：

   `https://你的用户名.github.io/agent-chat/`

7. 打开这个地址。如果仍是 404，先刷新、清缓存，再看仓库的 **Actions** 或 **Settings → Pages** 是否还在部署。

---

## 如果一定要用现在这个仓库

因为仓库根目录已有旧的 `index.html`，不要把 Pages 指向根目录，否则会打开旧页面。

把本机的 `docs` 文件夹整体上传到仓库后：

1. **Settings → Pages**
2. Source：**Deploy from a branch**
3. Branch：**main**（或你的默认分支）
4. Folder：**/docs**
5. Save

之后访问：

`https://你的用户名.github.io/仓库名/`

GitHub Pages 会读取 `docs/index.html`，不会再用仓库根目录的旧 `index.html`。

---

## 已经上传了 agent-chat.html 的情况

三种修法，任选一种：

1. 在仓库根目录新建 `index.html`，内容复制 `agent-chat.html` 的完整内容。
2. 把仓库里的 `agent-chat.html` 重命名为 `index.html`。
   - 如果根目录已有 `index.html`，会覆盖它；如果不想动旧页面，用第 3 种。
3. 上传本机的 `docs` 文件夹，Pages 选择 **/docs**。

**不要**只建一个空的 `index.html`，也不要用一段“跳到 agent-chat.html”的脚本代替完整内容；直接放完整页面最稳。

---

## 为什么页面在 GitHub 上可能仍然不对

### 1. 打开的是代码页，不是网页

`github.com/.../blob/main/index.html` 显示的是源码。

网页地址应该是：

`https://用户名.github.io/仓库名/`

注意是 `github.io`，不是 `github.com`。

### 2. 仓库是私有的

免费账号无法给私有仓库开启 Pages。把仓库改成 Public，或升级账户。

### 3. Pages 指向了错误的目录

仓库根目录有旧 `index.html` 时，选 `/(root)` 会显示旧页面。

选 **/docs**，并把新页面放到 `docs/index.html`。

### 4. 部署还没有完成

刚点 Save 时站点不会立刻可用。等 1–3 分钟，刷新 Pages 设置页；如果失败，看 **Actions** 里的部署记录。

### 5. 404 / 空白页

检查：

- 发布目录里是否真的有 `index.html`
- 文件名是否被系统改成了 `index.HTML` 或 `index.html.txt`
- Pages 的 Branch / Folder 是否与实际目录一致
- 是否访问了正确的仓库路径

### 6. 页面能打开，但模型不回复

这不是 GitHub Pages 的问题。页面本身是纯静态的，API 请求是从浏览器直接发出的：

- 检查 API 地址、Key、模型名
- 检查接口是否允许浏览器跨域请求（CORS）
- 有些接口不允许网页直连，需要自己的后端代理；这个页面没有后端

### 7. 旧聊天记录没有跟过来

聊天记录、API Key、主题和表情包都存在浏览器的 `localStorage` 里，并且按域名隔离。

从本机 `file://` 换成 `https://用户名.github.io/...` 后，是新的域名，旧记录不会自动迁移。需要在新页面重新填写 API，旧聊天也不会自动出现。

---

## 以后更新网页

本机的源文件仍然是：

`agent-chat.html`

发布用的副本是：

`docs/index.html`

改完源文件后，把新版本复制过去：

```powershell
Copy-Item -LiteralPath .\agent-chat.html -Destination .\docs\index.html -Force
```

然后重新上传 `docs/index.html` 到 GitHub。

---

## 这个页面为什么适合 GitHub Pages

- 只有一个 HTML 文件，没有构建步骤、没有依赖包、没有外部 CDN
- 头像、表情包、主题背景都压缩后存在浏览器本地
- 不需要服务器端代码
- 直接打开 `index.html` 就能运行

唯一的外部依赖是你在页面里填写的模型 API；GitHub Pages 只负责托管这个静态页面。
