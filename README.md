# 山传助农 · 完整 App + 云控后台

山传助农是一个面向山东原产地好物的助农购物 App：Android 端是本地 H5 + WebView 壳，后端同时提供 **App 内容 API**、**订单 / 反馈 API** 和 **云控管理网站**。开屏、宣传片、商品、分类、农场、直播、公告、优惠券、搜索热词、会员权益、客服与交易设置，全部可以在后台修改并发布，App 重新打开或回到前台时自动同步。

> 当前版本 **v1.0.0**，公网访问：<http://frp-cup.com:54260>  
> 管理后台：<http://frp-cup.com:54260/admin>  
> 管理账号：`admin`　初始密码：`ShanChuan@2026`（登录后请在「系统设置」立即修改）

## 交付内容

| 内容 | 位置 |
| --- | --- |
| 可安装 APK | [`dist/shanchuan-zhunong-v1.0.apk`](dist/shanchuan-zhunong-v1.0.apk) |
| APK SHA-256 | `ED15D797C89044AFB7C2D757C1278D11F363DC8349D496C51FC3B2D3614A39CF` |
| Android 壳 | `app/`（WebView、网络权限、原生开屏、返回键、视频暂停、外链处理） |
| App 前端 | `h5/`（数据驱动，全部素材与默认内容已就位） |
| 云控后端 | `server/server.js`（零第三方依赖 Node.js） |
| 管理网站 | `server/admin/`（登录、内容编辑、媒体库、订单、反馈、发布历史） |
| 默认内容源 | `shared/config.json` → 同步生成 `h5/config.js` |
| 运行数据 | `server/data/db.json`、`server/data/media/` |

## 公网部署

服务监听 `0.0.0.0:80`，由本机已安装的 **SakuraFrp** 服务把 80 端口映射到：

- App：<http://frp-cup.com:54260>
- 管理后台：<http://frp-cup.com:54260/admin>
- 健康检查：<http://frp-cup.com:54260/api/health>

本机预览：<http://127.0.0.1/>；管理后台：<http://127.0.0.1/admin>。

> SakuraFrp 隧道由系统服务自动启动，节点为 `frp-cup.com`，远程端口 `54260`，本地端口 `80`。如果公网打不开，先在 SakuraFrp 客户端确认隧道 `app` 已启动，再执行 `tools\check-online.ps1`。

### 启动 / 停止服务

```powershell
powershell -NoProfile -ExecutionPolicy Bypass -File tools\start-server.ps1
powershell -NoProfile -ExecutionPolicy Bypass -File tools\start-server.ps1 -Restart
powershell -NoProfile -ExecutionPolicy Bypass -File tools\stop-server.ps1
powershell -NoProfile -ExecutionPolicy Bypass -File tools\check-online.ps1
```

- 启动脚本会后台运行 `node server/server.js`，日志写入 `logs/server.out.log` / `server.err.log`，PID 写入 `logs/server.pid`。
- `start-server.ps1` 只会在端口被 **node** 占用时停止旧进程；不会误杀其他服务。
- 端口 80 需要本机没有被 IIS 等程序占用；当前已验证可用。

## App 功能（全部可用）

### 首页
- 云端开屏：支持图片 / 视频、倒计时、跳过、背景色、可选按钮与跳转；原生启动页 + H5 开屏双层兜底。
- 宣传片：自定义视频 + 海报、播放 / 暂停、拖动进度、静音、详情面板；切换 Tab 或退到后台自动暂停。
- 公告条：后台公告自动轮播，未读红点，点开消息面板。
- 助农计划、今日好物、农户故事、快捷分类、合作农场。

### 分类 / 搜索
- 6 个分类（含“全部好物”），左侧导航 + 商品网格。
- 筛选排序：综合推荐、销量优先、价格升 / 降。
- 搜索：实时结果、最近搜索持久化、热搜词、空结果引导；支持商品名 / 产地 / 规格 / 分类 / 描述匹配。

### 商品详情 / 购物车 / 下单
- 商品详情：大图、价格 / 原价、产地、规格、销量、库存、详情、关联农场、收藏、分享、数量选择、加入购物车 / 立即购买。
- 购物车：全选、单选、数量加减（1–99）、删除、批量删除、清空、角标联动、满包邮提示、优惠券入口。
- 结算：地址簿、收货人 / 手机号校验、支付方式、优惠券抵扣、备注、运费 / 包邮、提交订单。
- 订单：待付款 / 待发货 / 待收货 / 待评价 / 已完成 / 已取消；模拟支付、取消、确认收货、评价；状态由后台同步。
- 离线兜底：网络不可用时订单先保存在本机，联网后自动同步到服务器。

### 我的 / 服务
- 个人资料编辑（昵称、手机号、头像 URL）、助农值、收藏、足迹、优惠券统计。
- 地址管理（新增 / 删除 / 设为默认）、优惠券领取与使用、收藏夹、浏览足迹、会员权益、关于页。
- 客服中心：电话、微信复制、工作时间、FAQ 折叠、意见反馈表单。
- 设置：消息通知、自动播放、省流模式、检查内容更新、清除本地数据。
- 农场页：农场数据、直播回放 / 直播卡片、关注、农场详情与农场好物。

## 云控后台

后台为独立的响应式管理网站（`server/admin/`），与 App 共用同一个 Node 服务：

- **概览**：内容版本、商品 / 分类 / 农场 / 订单 / 媒体统计，最近订单、快速入口、服务状态。
- **开屏管理**：开关、倒计时、图片 / 视频、封面、标题 / 副标题 / 标语、跳过按钮、背景色、按钮与链接、手机实时预览。
- **首页内容**：首屏文案、搜索占位、宣传视频、助农计划、农户故事、首页快捷分类。
- **商品管理**：12 个字段全量编辑（名称、分类、价格、原价、单位、规格、产地、角标、图片、图标、色调、农场、销量、库存、排序、上架、推荐、简介、详情），支持新增、删除、上下移动、筛选。
- **分类 / 农场 / 直播**：分类 Banner、农场标签与供应商品、直播封面 / 状态 / 主播 / 观看人数。
- **公告 / 优惠券**：公告类型与日期；优惠券金额、门槛、有效期、启停。
- **搜索 / 我的页面**：热搜词、最近搜索、会员权益、常用服务。
- **系统设置**：品牌名、主题色、交易设置、客服信息、FAQ、通知 / 自动播放 / 省流 / 维护模式、管理员密码、导入导出、一键恢复默认。
- **订单管理**：按状态筛选、查看订单详情与时间线、直接修改订单状态。
- **意见反馈**：查看、标记已读、填写处理回复。
- **媒体库**：拖拽 / 点击上传图片、视频（单文件 300MB），复制地址、删除；默认素材与上传素材统一浏览。
- **发布记录**：最近 30 个版本，一键恢复；恢复前自动备份当前版本。
- **高级 JSON**：直接编辑完整配置，校验后应用到表单，再发布。

### 发布与同步机制

1. 后台点击「保存并发布」→ 配置写入 `server/data/db.json`，内容版本 +1，旧版本进入发布记录。
2. App 启动时请求 `/api/app/bootstrap`；回到前台 / 获得焦点时请求 `/api/app/version` 比对版本。
3. 若 WebView 限制跨域 `fetch`（例如 `file://` 加载），App 自动改用 `<script>` JSONP 兜底（`/api/app/bootstrap.js`），因此 **APK 内也能实时云控**。
4. 内容缓存保存在 `localStorage`，断网时使用上一次缓存或 APK 内置默认内容，不会白屏。
5. 本地未同步订单会在联网后自动补传（按 `clientId` 去重）。

## API 一览

### App 公开接口

| 方法 | 路径 | 说明 |
| --- | --- | --- |
| GET | `/api/health` | 服务健康、内容版本、公网地址 |
| GET | `/api/app/bootstrap` | App 完整配置（JSON） |
| GET | `/api/app/bootstrap.js` | 同一配置的 JSONP 兜底 |
| GET | `/api/app/version` | 轻量版本检查 |
| GET | `/api/app/version.js` | 版本检查 JSONP 兜底 |
| GET | `/api/app/orders?deviceId=…` | 本设备订单 |
| POST | `/api/app/orders` | 创建订单（服务端按当前商品价格重算金额） |
| POST | `/api/app/orders/:id/action` | `pay` / `cancel` / `confirm` / `review` |
| POST | `/api/app/feedback` | 提交意见反馈 |

### 管理接口（需要 Bearer Token）

`/api/admin/login`、`/api/admin/logout`、`/api/admin/session`、`/api/admin/config`、`/api/admin/orders`、`/api/admin/feedback`、`/api/admin/stats`、`/api/admin/media`、`/api/admin/library`、`/api/admin/history`、`/api/admin/password`、`/api/admin/export`、`/api/admin/import`、`/api/admin/reset` 等，详见 `server/server.js`。

- 登录失败限流：同一 IP 10 分钟内最多 10 次。
- 管理会话 12 小时，保存在内存中；重启服务后需重新登录。
- 媒体上传 `PUT /api/admin/media?name=…`，请求体为文件二进制。
- 删除媒体：`DELETE /api/admin/media/:name`。

## 目录结构

```text
.
├─ app/                          # Android WebView 壳
│  └─ src/main/
│     ├─ java/.../MainActivity.java
│     ├─ AndroidManifest.xml      # INTERNET + 明文流量 + 单任务
│     └─ res/                     # 图标、主题、原生开屏图
├─ h5/                           # App 前端（平铺素材，见下方说明）
│  ├─ index.html                  # 页面骨架 + SVG 图标库
│  ├─ app.js                      # 数据驱动渲染、购物车、订单、全部面板
│  ├─ styles.css                  # 原设计系统
│  ├─ features.css                # 开屏、面板、详情、结算等扩展样式
│  ├─ config.js                   # 由 shared/config.json 自动生成
│  ├─ runtime.js                  # file:// 时使用的公网 API 地址
│  ├─ img-*.jpg                   # 开屏 / 商品 / 农场 / 故事 / 直播图
│  ├─ promo.mp4 / promo-poster.jpg
│  ├─ splash.mp4
│  ├─ icon-*.png / apple-touch-icon-180.png
│  ├─ manifest.webmanifest / sw.js / pwa.js
├─ server/
│  ├─ server.js                   # 零依赖后端 + 静态站点 + 媒体流
│  ├─ admin/                      # 管理网站（HTML/CSS/JS）
│  └─ data/                       # db.json、admin.json、media/
├─ shared/config.json             # 默认内容源
├─ tools/
│  ├─ start-server.ps1 / stop-server.ps1 / check-online.ps1
│  ├─ build-apk.ps1               # APK 构建（暂存 ASCII 临时目录）
│  ├─ sync-config.js              # shared/config.json → h5/config.js
│  ├─ generate-assets.py          # 程序化生成默认视觉素材
│  └─ cdp.js                      # headless Chrome 验收工具
├─ variants/ h5-v2/               # 早期并装版本（v2，保留但当前不更新）
└─ dist/shanchuan-zhunong-v1.0.apk
```

> **素材为什么平铺？** Windows 下 `aapt2 -A` 打包多级子目录素材时，部分 Android WebView 会无法读取 `assets/images/...`；本项目把图片统一放在 `h5/` 根目录（`img-*.jpg`），确保 APK 内图片、开屏都能稳定加载。后台「媒体库」会自动扫描根目录。

## 构建 APK

环境：Windows、Java 17+（当前使用 JDK 25）、Android SDK Platform 36、Build-Tools 36.0.0、adb。构建脚本使用本机 debug keystore，产物可直接安装，但**不可直接上架应用商店**。

```powershell
powershell -NoProfile -ExecutionPolicy Bypass -File tools\build-apk.ps1
powershell -NoProfile -ExecutionPolicy Bypass -File tools\build-apk.ps1 -SkipInstall
```

产物：`dist/shanchuan-zhunong-v1.0.apk`（v1.0.0 / versionCode 10）。

安装：手机开启“USB 安装”，`adb install -r dist\shanchuan-zhunong-v1.0.apk`，或在文件管理器中点击 APK。

构建脚本会：

1. 把 `h5/` 与 Android 资源复制到 `%TEMP%` 的 ASCII 暂存目录，规避中文路径导致 aapt2 失败；
2. aapt2 编译资源、打包 H5 assets；
3. javac 编译壳、d8 生成 dex；
4. zipalign + apksigner（debug keystore）；
5. 若检测到已连接设备，自动安装并启动。

## 数据、备份与恢复

- 内容、订单、反馈：`server/data/db.json`（原子写入）。
- 管理员密码：`server/data/admin.json`（scrypt 加盐哈希，不存明文）。
- 媒体：`server/data/media/`，通过 `/media/<file>` 访问。
- 备份：后台「系统设置 → 导出当前配置」；或直接复制 `server/data/`。
- 恢复：后台导入 JSON，或在「发布记录」恢复历史版本。
- 忘记密码：停止服务 → 删除 `server/data/admin.json` → 启动服务，恢复初始密码 `ShanChuan@2026`。

## 验收记录

- **Android 真机 / 模拟器**：`Medium_Phone_API_35`（Android 15，1080×2340）实际安装运行；开屏、首页、视频、分类、农场、购物车、结算、订单、我的页面均正常；`INTERNET` + 明文 HTTP 访问公网 API 正常；后台改内容后 App 可同步。
- **桌面 Chrome**：H5 直接访问、管理后台登录、内容编辑与发布、媒体上传、订单 / 反馈、发布记录恢复均已验证。
- **公网**：`http://frp-cup.com:54260` 的 App、`/admin`、`/api/health` 均返回 200。

| 开屏 | 首页 | 宣传片 |
| --- | --- | --- |
| <img src="docs/screenshots/13-splash.png" width="220"> | <img src="docs/screenshots/01-home.png" width="220"> | <img src="docs/screenshots/02-video-playing.png" width="220"> |

| 分类 | 农场 | 我的 |
| --- | --- | --- |
| <img src="docs/screenshots/04-category.png" width="220"> | <img src="docs/screenshots/05-farm.png" width="220"> | <img src="docs/screenshots/07-mine.png" width="220"> |

| 购物车 | 结算 | 搜索 |
| --- | --- | --- |
| <img src="docs/screenshots/06-cart.png" width="220"> | <img src="docs/screenshots/08-checkout.png" width="220"> | <img src="docs/screenshots/03-search.png" width="220"> |

管理后台：

| 概览 | 开屏 | 商品 |
| --- | --- | --- |
| <img src="docs/screenshots/09-admin.png" width="320"> | <img src="docs/screenshots/10-admin-splash.png" width="320"> | <img src="docs/screenshots/11-admin-products.png" width="320"> |

| 订单 | 媒体库 | 手机预览 |
| --- | --- | --- |
| <img src="docs/screenshots/12-admin-orders.png" width="320"> | <img src="docs/screenshots/14-admin-media.png" width="320"> | <img src="docs/screenshots/13-splash.png" width="320"> |

## 替换内容

- **开屏 / 宣传片**：后台 → 开屏管理 / 首页内容，直接上传新图或视频。
- **商品 / 农场 / 故事**：后台对应页面修改，发布后 App 同步。
- **默认内置内容**：修改 `shared/config.json` 后执行 `node tools/sync-config.js`，再重新构建 APK；这样离线首启也是新内容。
- **重新生成默认素材**：`tools/generate-assets.py`（需要 Pillow、numpy、ffmpeg；脚本内配置了本机 ffmpeg 路径，可按环境调整）。生成后会覆盖 `h5/img-*.jpg`、`h5/promo.*`、`h5/icon-*.png` 和原生开屏 drawable。

## 注意事项

1. 公网地址为 **HTTP**（frp TCP 隧道），管理密码在传输中不加密。请勿在不可信网络登录后台，并尽快修改初始密码。
2. APK 使用 debug keystore 签名，仅用于比赛演示 / 内测，**不能直接上架**；正式发布请替换 release keystore 并关闭 `debuggable`。
3. 商品、订单、评价、支付均为演示数据；App 内“支付”是模拟支付，不会产生真实扣款。
4. SakuraFrp 免费隧道无可用性承诺；长期演示请保持电脑不休眠、网络稳定。
5. `h5-v2/` 与 `variants/v2.json` 是早期并装版本，当前主版本功能与素材已更新，v2 未同步；如需 v2，请先把 `h5/` 同步到 `h5-v2/` 再构建。
6. 内容变更后，已安装的 App 下次启动 / 回到前台会拉取新版本；也可在「我的 → 设置 → 检查内容更新」立即刷新。

## 常用命令

```powershell
# 启动 / 重启 / 停止后端
powershell -NoProfile -ExecutionPolicy Bypass -File tools\start-server.ps1
powershell -NoProfile -ExecutionPolicy Bypass -File tools\start-server.ps1 -Restart
powershell -NoProfile -ExecutionPolicy Bypass -File tools\stop-server.ps1

# 检查本机 + 公网
powershell -NoProfile -ExecutionPolicy Bypass -File tools\check-online.ps1

# 同步默认配置并重新构建 APK
node tools\sync-config.js
powershell -NoProfile -ExecutionPolicy Bypass -File tools\build-apk.ps1 -SkipInstall

# 直接运行后端（前台，调试用）
node server\server.js
```



