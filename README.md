# A2H Market skill · Claude plugin 版

「A2H Market」闲置集市的 agent skill：**买卖两侧都管**——想卖闲置就识图建档、定价、
上架、接待买家、代笔议价；想买就搜寻、问询、砍价。人只做拍照、确认、收钱、交货。

这一份是 **Claude plugin**：一次安装带三样东西——剧本（`skills/a2hmarket/`）、集市工具面
（`.mcp.json` 里那个远端 MCP 服务器，浏览器点一次授权）、会话开场巡查（SessionStart 钩子）。
本仓同时就是一个 **plugin marketplace**，清单在仓根 `.claude-plugin/marketplace.json`。

只读 `SKILL.md` 的宿主（WorkBuddy 等）装
[a2h-skill-generic](https://github.com/keman-ai/a2h-skill-generic)；
用 Codex 的装 [a2h-skill-codex](https://github.com/keman-ai/a2h-skill-codex)；
用 ChatGPT 的看 [a2h-skill-chatgpt](https://github.com/keman-ai/a2h-skill-chatgpt)。

## 安装

**Claude Code**——会话里两条斜杠命令：

```
/plugin marketplace add keman-ai/a2h-skill-claude-plugin
/plugin install a2hmarket@a2hmarket
```

> 前一个 `a2hmarket` 是 plugin 名，后一个是本 marketplace 的名字。

**Cowork**——Plugins 页选 **Add marketplace**，填仓地址
`https://github.com/keman-ai/a2h-skill-claude-plugin`（简写 `keman-ai/a2h-skill-claude-plugin`
也认），再在列表里装 **a2hmarket**。

> 🔴 **安装过程里那一步连接器登录必须做完**：装 plugin 时会让你连 `a2hmarket` 连接器
> （浏览器点一下「同意授权」）。**跳过它，plugin 装上了、会话里却一个集市工具都没有**——
> 表现是「它说自己能上架，可什么也做不了」，很容易被当成插件坏了。
>
> **已经装了、但当时跳过了怎么办**（三步，不用重装）：
> 1. 打开 **Plugins** → 点进 **a2hmarket** 的详情页；
> 2. 找到 **Connectors** → **Connect**，在浏览器里完成登录；
> 3. **新开一个会话**——工具面是会话启动时装配的，当前这个会话里不会凭空出现。
>
> **云端会话请用连接器的工具**（`a2hmarket_*`）：它从服务端出网，什么都不缺。
> 而在云端沙箱里直接跑剧本自带的命令行连不上集市**是正常的**——平台对沙箱有出网域名
> 白名单，`a2hmarket.ai` 不在里面。这不是你的账号或插件出了问题，也不用反复重新授权。

装完在会话里说一句「**逛逛 A2H Market**」，它会带你走开箱：介绍产品 → 浏览器点一下
授权 → 建档。全程自助，不需要谁给你开通。

工具面若没自动连上，按 `plugins/a2hmarket/SETUP.md` 走一遍兜底——那份文档就是为这一步写的。

## 更新

```
/plugin marketplace update a2hmarket
/plugin update a2hmarket
```

## 目录里有什么

| 路径 | 是什么 |
|------|--------|
| `.claude-plugin/marketplace.json` | marketplace 清单——宿主靠它知道本仓提供哪些 plugin |
| `plugins/a2hmarket/.claude-plugin/plugin.json` | plugin 清单：名字与版本 |
| `plugins/a2hmarket/skills/a2hmarket/` | 剧本本体：`SKILL.md` + `references/` + `scripts/` |
| `plugins/a2hmarket/.mcp.json` | 集市工具面：远端 MCP 服务器声明（带 OAuth） |
| `plugins/a2hmarket/hooks/hooks.json` | SessionStart 巡查：开场自动看一眼有没有人找你 |
| `plugins/a2hmarket/SETUP.md` | 连接与授权的引导，连不上时的兜底 |

## 运行前提

`python3` + 能发 HTTPS 出网请求 + 一个能安全写入的状态目录（凭证要落在 0700 目录里的
0600 文件上）。装不上 / 连不上 / 登录不了，让 agent 跑一次剧本自带的
`scripts/a2hmarket.py doctor`——它只读、输出一个 JSON，一次说清这台机器缺哪一条。

## 说明

- 本仓由 CI 从内部源仓构建后**整体覆盖**，请不要直接提 PR 改这里的文件——会在下次
  同步时被冲掉。有问题提 issue。
- 内容对应**正式环境**（`a2hmarket.ai`）。
