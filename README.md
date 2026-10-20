# JX · 小红书自动化发布专家

接管已登录浏览器，把做好的视频和文案批量、可核对地送进小红书创作服务平台——存草稿、定时发布、全量覆盖核对、故障排查。

## 类型

Agent 型（单个 AI 专家）

## 功能

**1. CDP 接管真实 Chrome**
用 puppeteer-core 连 9222 调试端口，操作真实已登录会话。不走非官方接口、不伪造签名。含登录态归属判断（常在 `Profile 1` 而非 `Default`）、macOS 沙箱拦 `SingletonLock` 时的启动绕法、node 路径漂移探测。

**2. 视频笔记全流程填稿**
上传（`input.upload-input`）→ 标题（React native setter）→ 正文（tiptap 整段 `execCommand`）→ 话题（必须下拉点选，键盘 ArrowDown + Enter）→ 原创声明 → 合集 → 智能章节（批量时默认跳过，会长时间占用主线程）。

**3. 草稿与定时发布**
发布按钮在闭包 shadow DOM 里，文本搜不到，只能 CDP 坐标扫描。`ce-btn.white` = 暂存离开，`ce-btn.bg-red` = 发布（脚本永不点击）。定时窗口 2 小时后 ~ 14 天内。

**4. 全量覆盖核对**
服务端笔记走 `note/user/posted` 接口（需页面自带 `_webmsxyw` 签名），配合草稿箱 DOM，与本地文案做模糊比对，输出「已发布 / 草稿 / 未通过 / 缺失」四分类矩阵。

**5. 故障诊断**
登录失效、`Runtime.callFunctionOn timed out`、草稿全量消失、按钮点不动、笔记被判未通过——都有固定排查顺序和已知根因。

## 使用示例

- 把这 15 期视频批量发到小红书草稿箱
- 查一下已发布和草稿箱，找出还没发的内容
- 登录失效或草稿不见了，帮我排查

## 三条必须知道的铁律

1. **草稿是浏览器本地存储，不在服务端**（官方原文：「草稿存储于当前使用的浏览器本地，在清除浏览器数据时会被删除」）。所以草稿**不跨浏览器**，也不能随便 kill 调试 Chrome——2026-10-17 强杀重启后 25 条草稿全部归零。
2. **脚本只存草稿，发布由真人点**。平台能识别脚本化批量发布，曾有 5 条笔记被判「利用 AI 托管进行发文/互动」。
3. **引流红线全场景生效**：微信 / QQ / 手机号 / 二维码 / 外链、「加v」「vx」「私信领资料」都违规。唯一官方许可入口是把小红书号设成微信号。

## 头像

头像已自动生成在 `avatars/expert.png`。如需替换：PNG/JPG，512×512 px，≤500KB。

## 安装

```
~/.workbuddy/plugins/marketplaces/my-experts/plugins/xhs-publisher/
```

```bash
python3 scripts/register_expert.py <expert-dir>
```

## 打包分享

```bash
python3 scripts/package_expert.py <expert-dir> [output-dir]
```
