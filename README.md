# Codex 桌面端接入小米 MiMo 模型完整教程

> 通过 mimo2codex 本地代理，让最新版 OpenAI Codex 桌面端成功接入小米 MiMo 模型。

## 背景

Codex 新版强制使用 `/responses` 端点，而 MiMo 只支持 `/chat/completions`，两者协议不兼容，无法直接接入。

mimo2codex 是一个本地代理工具，专门解决这个问题：在本地运行一个代理服务，把 Codex 的 Responses API 请求实时转换成 Chat Completions 格式，再转发给 MiMo。

## 前置条件

- 已安装 OpenAI Codex 桌面端
- 拥有小米 MiMo 订阅套餐（Token Plan），并获取专属 API Key（`tp-` 开头）
- Windows 10/11 系统

---

## 第一步：安装 Node.js

1. 访问 [nodejs.org](https://nodejs.org) 下载 LTS 版本
2. 选择 **Windows 安装程序(.msi)**，一路默认安装
3. 安装完成后验证：

```powershell
node -v
```

输出版本号即安装成功。

---

## 第二步：解除 PowerShell 执行策略限制

```powershell
Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned
```

---

## 第三步：安装 mimo2codex

```powershell
npm install -g mimo2codex --ignore-scripts
```

> 加 `--ignore-scripts` 跳过 C++ 编译步骤，无需安装 Visual Studio Build Tools。

---

## 第四步：初始化配置

```powershell
mimo2codex init
```

打开配置文件：

```powershell
notepad "$env:USERPROFILE\.mimo2codex\.env"
```

找到以下这行并取消注释，填入你的 MiMo 订阅专属 Key：

```
MIMO_API_KEY=tp-你的订阅专属Key
```

保存关闭。

---

## 第五步：启动代理服务

```powershell
mimo2codex
```

启动成功后会看到以下信息：

```
mimo2codex v0.x.x listening on http://127.0.0.1:8788
provider:  mimo
upstream:  https://token-plan-cn.xiaomimimo.com/v1
api key:   tp-c…awzi
```

**保持此窗口开启，不要关闭。**

---

## 第六步：配置 Codex config.toml

打开文件 `C:\Users\你的用户名\.codex\config.toml`，添加或修改以下内容：

```toml
model_provider = "custom"
model = "mimo-v2.5-pro"

[model_providers.custom]
name = "custom"
wire_api = "responses"
requires_openai_auth = false
base_url = "http://127.0.0.1:8788/v1"
```

保存后重启 Codex。

---

## 第七步：验证

在 Codex 中发送任意消息，底部显示 `custom`，且能正常回复即接入成功。

去 [MiMo 订阅管理](https://platform.xiaomimimo.com) 页面刷新用量，有消耗说明100%接入成功。

---

## 日常使用

每次使用 Codex 前，需要先启动代理：

```powershell
mimo2codex
```

保持终端窗口开启，然后正常使用 Codex 即可。

---

## 相关链接

- [mimo2codex GitHub](https://github.com/7as0nch/mimo2codex)
- [小米 MiMo 开放平台](https://platform.xiaomimimo.com)
- [OpenAI Codex](https://openai.com/codex)

---

## License

MIT
