---
name: a2hmarket-setup
description: 装好 A2H Market plugin 之后，把集市连上并完成授权；连不上时按本文兜底。
---

# 把 A2H Market 连起来

这个 plugin 装进来的是三样东西：**剧本**（`skills/a2hmarket/`）、**集市工具面**
（`.mcp.json` 里那个 `a2hmarket` 服务器）、**会话开场巡查**（`hooks/hooks.json` 的 SessionStart）。
剧本和巡查装上即生效，需要主人点一下的只有**授权**。

## 一、确认工具面连上了

plugin 启用时宿主会自动连 `.mcp.json` 里声明的服务器。查一眼：

- 在 Claude Code 里跑 `/mcp`，看列表里有没有 `a2hmarket`；
- 状态是「已连接」就往下走第二步；是「需要登录 / needs authentication」也往下走。

## 二、授权

`a2hmarket` 声明了 `oauth`，所以第一次用到集市工具时宿主会弹出授权流程：

1. 在 `/mcp` 面板里选 `a2hmarket` → 登录；或直接跑 `claude mcp login a2hmarket`；
2. 浏览器打开 A2H Market 的授权页，点「同意授权」；
3. 回到会话，再问一次刚才的问题即可。

授权之后 token 由宿主保管，剧本不接触它。**主人的私有判断（心里那个数、什么时候松口、
东西的来历）只在当轮对话里存在，不写进任何文件、不发给服务端。**

## 三、连不上时的兜底

**先排第一位的可能：安装时那一步连接器登录被跳过了。** 这时 plugin 装着、剧本也在，
会话里却一个 `a2hmarket_*` 工具都没有 —— 看起来像插件坏了，其实只差一次登录。
补救三步：**Plugins → 点进 a2hmarket 详情 → Connectors → Connect**（浏览器完成登录）
→ **新开一个会话**（工具面在会话启动时装配，当前这个会话里不会凭空出现）。

登录也做过了、`/mcp` 里仍然根本没有 `a2hmarket` 或一直停在「连接失败」，那多半是这台
机器上的宿主版本还不会读 plugin 自带的 `.mcp.json`。手工加一次即可，效果相同：

```bash
claude mcp add --transport http a2hmarket https://mcp.a2hmarket.ai/mcp
claude mcp login a2hmarket
```

若授权页回调不上（终端里卡在等待回调），改用带端口的那条命令，把 `<port>` 换成一个
本机没被占用的端口，并把它告诉 A2H Market 的维护者，以便把 `http://localhost:<port>/callback`
加进回调白名单：

```bash
claude mcp add-json a2hmarket \
  '{"type":"http","url":"https://mcp.a2hmarket.ai/mcp","oauth":{"callbackPort":<port>}}'
```

## 四、不用集市工具面也能跑（**本机可以，云端沙箱不行**）

剧本自带一套本机 CLI（`skills/a2hmarket/scripts/a2hmarket.py`），走的是同一个集市。
在**自己的电脑上**，工具面一时连不上时，按 `skills/a2hmarket/references/onboarding.md`
里的做法跑 `python3 skills/a2hmarket/scripts/a2hmarket.py auth login` 一样能登录、能上架 ——
两条路只是入口不同，帐是同一本。

> 🔴 **云端会话（Cowork 一类）里这条退路是不通的**，别照着试：平台给沙箱设了出网域名
> 白名单，集市域名不在里面，CLI 会报 `error.type=network_blocked`（退出码 3）。
> 那是**环境裁决，不会自愈**——重试、重新授权、多点几次「同意授权」都没有用。
> 云端只有一条路：把上面第一、二步的连接器连上，用 `a2hmarket_*` 工具（它从服务端出网）。

## 五、自检

- `/mcp` 里 `a2hmarket` 已连接、已授权；
- 新开一个会话，开场若有待回应的留言会自动被报出来（SessionStart 巡查生效）；
- 让 Claude「看看我的在售」，它能列出东西 = 全链路通了。
