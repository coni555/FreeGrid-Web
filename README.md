# FreeGrid Web · Windows 桌面版 + 在线 demo

> FreeGrid 的 **Windows 桌面版**，外加一个**在线 demo**（这个仓库也是网页版的源码）。把「财富自由」折算成你每天看得见的「自由天数」——别的记账软件告诉你花了多少，FreeGrid 让你看见**代价**。
>
> *The Windows desktop build of FreeGrid, plus a no-backend web demo.*

![Svelte](https://img.shields.io/badge/Svelte-5-FF3E00.svg?logo=svelte)
![Vite](https://img.shields.io/badge/Vite-8-646CFF.svg?logo=vite)
![Tauri](https://img.shields.io/badge/Tauri-2-24C8DB.svg?logo=tauri)
![PWA](https://img.shields.io/badge/PWA-installable·offline-5a0fc8.svg)
![Offline](https://img.shields.io/badge/100%25-本地·零网络-2ea44f.svg)

> 🍎 **产品主页 · 完整理念 · iOS / macOS 原生版 → [FreeGrid-Freedom](https://github.com/coni555/FreeGrid-Freedom)**
> 本仓库是 FreeGrid 的 Windows 桌面端 + 在线 demo 实现

## 🌐 在线 demo（零安装）

打开即用，无需注册或安装：**[freegrid-web.pages.dev](https://freegrid-web.pages.dev)**
它是个**纯前端体验站**——没有后端、不保存账号，**纯粹给你打开试一下产品长什么样**，不建议拿它当日常工具。要真正用起来，请装下面的 Windows 桌面版，或主仓的 iOS / macOS 原生版。

## 这是什么

FreeGrid Web 是 iOS 版 FreeGrid 的**纯前端实现**：同一套「自由天数 / 自由网格」核心概念，用 Svelte 5 重写、为桌面宽屏重排（侧栏导航替代手机底 tab），再用 Tauri 打包成轻量 Windows 桌面版。**零后端、零网络、零账号**——数据只存在你浏览器的 localStorage 里。

四屏：**Dashboard**（自由天数 + 自由网格 + 趋势）/ **Assets**（双桶净值 + 被动收入 + 调拨 + 导入导出 + 检查更新）/ **History**（流水 + 分类筛选 + 月度汇总）/ **Check**（8 项财富自由自检）。

> 产品理念（看见代价 → 长期主义 → 拿回掌握权）完整版在 [主仓 README](https://github.com/coni555/FreeGrid-Freedom)。

## 📥 获取

| 平台 | 怎么用 | 说明 |
|---|---|---|
| 🪟 **Windows**（正式版） | [Releases](https://github.com/coni555/FreeGrid-Web/releases/latest) → `.exe` / `.msi` | Tauri 打包，**自带自动更新**。未签名，首次运行 SmartScreen 拦截点「更多信息 → 仍要运行」 |
| 🍎 **iPhone / Mac**（原生） | [FreeGrid-Freedom v1.1](https://github.com/coni555/FreeGrid-Freedom/releases/tag/v1.1) | 原生 SwiftUI 版，住在主仓。数据格式与本版完全互通 |
| 🤖 **Android**（原生） | [FreeGrid-Freedom android-v1.0.0](https://github.com/coni555/FreeGrid-Freedom/releases/tag/android-v1.0.0) → `.apk` | 原生 Flutter 版，零联网。源码在 [FreeGrid-Android](https://github.com/coni555/FreeGrid-Android)，数据格式与本版完全互通 |
| 🌐 **在线 demo** | **[freegrid-web.pages.dev](https://freegrid-web.pages.dev)** | 纯前端体验站，打开即用、零安装（无后端、不保存账号）。本仓库即它的源码 |

> 🔁 **与 iOS 版数据互通**：序列化格式与 iOS 版的备份 JSON 完全一致——iOS 版导出 JSON，这里直接导入，账目无缝迁移到桌面/网页。

## 给开发者：技术细节

这部分是本仓相对原生仓的差异化重点。

- **技术栈**：Svelte 5（runes，`$state` ≈ SwiftUI `@State`）+ Vite + TypeScript，客户端纯静态（无 SSR）；桌面壳 Tauri v2（Rust）。业务逻辑零第三方运行时依赖。
- **真引擎**：自由天数 / 自由网格 / 被动覆盖等算法从 iOS 版 **1:1 移植**并附带 vitest 单元测试，与原生版结果一致。
- **桌面端禁用 PWA service worker**：网页版用 vite-plugin-pwa（离线 + 可安装）；但桌面构建（`vite build --mode tauri`）**不打包 SW**——否则 WebView2 会缓存旧前端、导致更新后仍显示旧界面。桌面端用 Tauri 本地资源，本就不需要 SW。
- **桌面自动更新**：`tauri-plugin-updater`，启动静默检查 + 资产页「检查更新」按钮。endpoints = **Cloudflare 镜像优先 + GitHub 兜底**，让墙内用户也能更新。
- **Cloudflare 更新代理**：`functions/updater/[[path]].js` 是一个 Pages Function——CF 边缘直连 GitHub 取 `latest.json` 并改写下载地址走代理，字节级转发（验签不变）。绕开国内对 GitHub Release 下载的封锁。

## 构建与运行

```bash
npm install
npm run dev                 # 本地开发 http://localhost:5173
npm run build               # 网页生产构建（含 PWA）→ dist/
npm run build -- --mode tauri   # 桌面构建（不含 SW）
npm test                    # 算法单元测试（vitest）
npm run tauri build         # 本地打桌面包（需 Rust 工具链）
```

构建产物是静态文件，部署在 Cloudflare Pages（push `main` 自动部署）。Windows 安装包由 GitHub Actions 在 `windows-latest` 上用 `tauri-action` 云构建（macOS 不能交叉编译 Windows）。

## 隐私

- ✅ **零网络层**——没有任何业务 `fetch` / 网络请求，数据从不离开设备（唯一对外请求是桌面版可选的「检查更新」）。
- ✅ 无账号、无云、无埋点、无分析；自托管系统字体。
- ⚠️ 数据存浏览器 localStorage：清浏览器数据会清掉它，导出 JSON 是你的备份通道。

## 后续计划

- ~~**安卓**：暂无安卓版~~ → **已发布！** 原生 Flutter 版 [android-v1.0.0](https://github.com/coni555/FreeGrid-Freedom/releases/tag/android-v1.0.0)，源码在 [FreeGrid-Android](https://github.com/coni555/FreeGrid-Android)。
- **代码签名**：去掉 Windows SmartScreen 警告（视情况，需付费证书）。
- 想法 / bug 欢迎 issue / PR / discussion。

## 许可

**MIT License + [Commons Clause](https://commonsclause.com/)**——源码公开，允许自由使用、修改、学习、非商业分发，但不得出售本软件。详见 [LICENSE](LICENSE)。与 [原生主仓](https://github.com/coni555/FreeGrid-Freedom) 一致。

---

🍎 喜欢 FreeGrid？请到 **[主仓 FreeGrid-Freedom](https://github.com/coni555/FreeGrid-Freedom) 点个 Star ⭐**——这对一个独立开发者真的很重要。感谢你。
