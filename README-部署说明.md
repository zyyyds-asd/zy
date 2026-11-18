# zyyyds-ASD 静态站 · 上传说明

这个文件夹就是可以直接上传到仓库根目录的全部文件。

| 文件 | 必须上传 | 作用 |
|---|---|---|
| `index.html` | ✅ | 网站首页（Cloudflare 靠它响应根路径 `/`） |
| `wrangler.jsonc` | ✅ | 告诉 wrangler 静态文件在 `./`，修复部署报错 |
| `.assetsignore` | 建议 | 发布时排除配置和说明，避免配置文件被公开访问 |
| `404.html` | 建议 | 访问不存在的地址时显示（wrangler 已配置 404-page） |
| `README-部署说明.md` | ❌ | 就是本文件，不用上传 |

## 一、上传到 GitHub

1. 打开仓库 `zyyyds-ASD`。
2. **先删掉根目录里旧的「我的网页.html」**（如果不删，会有两个入口，`/` 仍然 404）。
3. 把 `index.html`、`wrangler.jsonc`、`.assetsignore`、`404.html` 传到**仓库根目录**（不要放进子文件夹）。
4. `.assetsignore` 是隐藏文件，网页上传时如果选不到，可以跳过——不影响能不能起来，只是配置可能被当成静态文件访问。

## 二、Cloudflare 重新部署（Workers 构建）

Dashboard → Workers & Pages → `zyyyds-ASD` → 点「重试部署」。

构建设置保持默认，不要改：

- 构建命令：留空
- 部署命令：`npx wrangler deploy`
- 根目录：`/`

成功后访问：`https://zyyyds-asd.<你的子域>.workers.dev`

> 注意：`wrangler.jsonc` 里的 `"name": "zyyyds-asd"` 要和**你已有的 Worker 名字一致**。
> 如果你现有的 Worker 叫别的名字，把这个 name 改成它，否则会新建一个 Worker，老地址不会更新。

## 三、备选：改成 Pages（没有这个坑）

Workers & Pages → 创建 → Pages → 连接到 Git，选同一个仓库：

- 框架预设：`None`
- 构建命令：留空
- 构建输出目录：`/`

Pages 不走 `wrangler deploy`，天生不会报"检测不到静态文件目录"。

## 四、本地预览

手动双击 `index.html` 就能看效果，不需要起服务器。

但注意：**本地 `file://` 打开时，Chrome 会禁用 IndexedDB**，发图片/改头像会失败；
上传到 http(s) 站点后就正常。所以别用本地打开来验证"能不能发图"。

## 五、上线后要知道的

1. 页面数据（动态、点赞、图片）存在**访问者自己的浏览器**里（localStorage + IndexedDB），
   不在服务器上：换设备/清缓存会丢，别人打开你的网址看到的是空白朋友圈。
2. 页面样式依赖 `cdn.tailwindcss.com`。如果所在网络打不开它，页面会变成无样式白页（功能仍在）。
   想彻底离线：把它下载成 `tailwind.js`，把 `index.html` 里那行 `<script src="https://cdn.tailwindcss.com"></script>`
   改成 `<script src="./tailwind.js"></script>`。
