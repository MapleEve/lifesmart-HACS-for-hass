# LifeSmart IoT Integration for Home Assistant

[![hacs_badge](https://img.shields.io/badge/HACS-Default-orange.svg)](https://github.com/hacs/integration)
![GitHub License](https://img.shields.io/github/license/MapleEve/lifesmart-for-homeassistant)
[![version](https://img.shields.io/github/manifest-json/v/MapleEve/lifesmart-for-homeassistant?filename=custom_components%2Flifesmart%2Fmanifest.json)](https://github.com/MapleEve/lifesmart-for-homeassistant/releases/latest)
[![stars](https://img.shields.io/github/stars/MapleEve/lifesmart-for-homeassistant)](https://github.com/MapleEve/lifesmart-for-homeassistant/stargazers)
[![issues](https://img.shields.io/github/issues/MapleEve/lifesmart-for-homeassistant)](https://github.com/MapleEve/lifesmart-for-homeassistant/issues)
![haasfestworkflow](https://github.com/MapleEve/lifesmart-for-homeassistant/actions/workflows/haas-vali.yml/badge.svg)
![hacsworkflow](https://github.com/MapleEve/lifesmart-for-homeassistant/actions/workflows/hacs-vali.yml/badge.svg)


使用说明 Instructions
====
让 Home Assistant 支持 Lifesmart 设备的 HACS 插件 Lifesmart devices for Home Assistant

HACS
一键安装：[![通过HACS添加集成](https://my.home-assistant.io/badges/hacs_repository.svg)](https://my.home-assistant.io/redirect/hacs_repository/?owner=MapleEve&repository=lifesmart-for-homeassistant&category=integration)

安装完成后一键集成：[![添加集成](https://my.home-assistant.io/badges/config_flow_start.svg)](https://my.home-assistant.io/redirect/config_flow_start?domain=lifesmart)

准备步骤 Prerequisites：
---

1. 请根据您所在的国家，确认您所在区域对应的 LifeSmart
   服务器，必须使用 `API 授权后返回的地址`。[查看详细的区域服务器列表](./docs/api-regions.md)。 WebSocket
   URL地址的选择，必须根据⽤户授权成功后返回的 `svrrgnid` 保持⼀致，否则不会 正常⼯作，WebSocket 不⽀持跨区使⽤。 Find current
   LifeSmart region for your country America, Europe, Asia Pacific, China.

2. 在 LifeSmart 开放平台上创建一个新应用，以便获取应用密钥（`App Key`
   ）和应用令牌（`App Token`）。[访问申请页面](http://www.ilifesmart.com/open/login)（注意：该链接不是 HTTS 连接，请检查浏览器地址栏是否使用
   HTTP 访问）。
   New Application from LifeSmart Open Platform to obtain `App Key` and `App Token`

3. 使用您的 LifeSmart 账户登录上一步创建的应用，授权第三方应用访问，从而获取用户令牌（`User Token`）。请确保您使用的 API
   地址与您所在的区域相匹配。[点击直达如何通过 Python 代码获取](#usertoken)
   Login to application created in previous bullet with LifeSmart user to grant 3rd party application access to
   get `User Token`, please ensure you use the api address with correct region.

**特别提示：LifeSmart
开放平台的默认应用不会包含锁设备的类型信息，若无实体权限则无法生成门锁实体，而只会在门锁有开关动作后生成 **二元传感器**
。如需门锁实体功能，需联系
LifeSmart 官方获取授权。** [点击直达如何获取门锁设备的授权](#lock)
**Please note that, by default application from LifeSmart Open Platform won't return you Lock devices type. You have to
contact them to get it granted to your application.**

插件如何运作 How it works：
---

- 该插件需要联网。在首次加载插件时，它会调用 LifeSmart API 来获取所有设备信息，并在 Home Assistant 中进行设置。之后，插件将通过
  websocket 从 LifeSmart 获取设备更新信息。目前，Home Assistant 与 LifeSmart 中枢之间没有直接通信。

安装指南 How to install：
---

### 推荐使用 HACS 安装自定义仓库

1. 进入 HACS > 集成 > 点击右上角的三个点 > 选择自定义仓库。Go to HACS > Integration > 3 dots menu at the top right >
   choose Custom Repository
   已经安装了HACS，可以点击按钮快速安装


2. 在自定义仓库对话框中输入以下信息 In custom repository dialog enter ：

仓库地址：`https://github.com/MapleEve/lifesmart-for-homeassistant`

类别选择：`集成` Category: `Integration`

3. 点击添加按钮。

4. 添加完成后，通过右下角添加集成进行安装，再进入集成页面添加集成，搜索`Lifesmart`就可以添加集成并进行设置。

使用 HACS 可以方便您在新版本发布后及时更新。 2024 年内，我将把它加入 HACS 默认仓库列表，并计划将来将其纳入 Home Assistant
官方集成。Via HACS should allow you to get new version when it ready. I plan to add it to HACS default repository list in
2024 and
plan to include it in Home Assistant official integration in future.

### 手动安装方法 Manual

不建议使用手动安装方法，因为它需要手动更新插件，且可能与最新的 Home Assistant 版本不兼容。NOT RECOMMENDED！

```yaml
lifesmart:
  appkey: # 你应用的 appkey
  apptoken: # 你应用的 apptoken
  usertoken: # 你获取到的 usertoken
  userid: # 你获取到的 userid
  url:  # 你使用的 api 地址，建议使用 api.cn0.ilifesmart.com 或者 api.cn2.ilifesmart.com
```

示例配置 Example：
---
![示例配置屏幕截图](./docs/example-configuration.png)
![示例配置屏幕截图 2](https://private-user-images.githubusercontent.com/1845053/326725027-7d23f8bb-8810-43fa-95e7-6d373d576821.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MTQ0NjU1MzUsIm5iZiI6MTcxNDQ2NTIzNSwicGF0aCI6Ii8xODQ1MDUzLzMyNjcyNTAyNy03ZDIzZjhiYi04ODEwLTQzZmEtOTVlNy02ZDM3M2Q1NzY4MjEucG5nP1gtQW16LUFsZ29yaXRobT1BV1M0LUhNQUMtU0hBMjU2JlgtQW16LUNyZWRlbnRpYWw9QUtJQVZDT0RZTFNBNTNQUUs0WkElMkYyMDI0MDQzMCUyRnVzLWVhc3QtMSUyRnMzJTJGYXdzNF9yZXF1ZXN0JlgtQW16LURhdGU9MjAyNDA0MzBUMDgyMDM1WiZYLUFtei1FeHBpcmVzPTMwMCZYLUFtei1TaWduYXR1cmU9N2U0NGFjOTkzYzgxMzlmMWZkNDgzYjc2NjkxOTkwNGFjN2RjYTA3ODczOWIyNjEyMzQyNDhiZTY0NWM1OGNjYiZYLUFtei1TaWduZWRIZWFkZXJzPWhvc3QmYWN0b3JfaWQ9MCZrZXlfaWQ9MCZyZXBvX2lkPTAifQ.K2jjcmycDoBrTJRRT9MoWLoEXDZ1D8YrsJ8y4JB0yLk
)
![示例图片](./docs/example-image.png)
![示例图片 4](./docs/example-image-4.png)
![示例图片 2](./docs/example-image-2.png)
![示例图片 3](./docs/example-image-3.png)

支持的设备列表 Supported devices：
---
由于代码重构和更新，可能会有旧设备从支持列表中移除，移除的旧设备加上了~~删除线~~。

- 开关 Switch
- 智能门锁 Door Lock
- 智能插座 Plugs
- 动态传感器、门禁传感器、环境传感器、甲醛/气体传感器 Dynamic Sensor, Door Sensor, Environmental Sensor, Formaldehyde/Gas
  Sensor
- 照明：目前仅支持超级碗夜灯 Super Bowl
- 通用遥控器 IR Remote
- 窗帘电机和窗帘电机的更新状态 Curtain Motor and Curtain Motor Update
- 空调控制面板 Air Conditioner Control Panel

### 支持设备详细列表 List of supported devices

#### 开关类：

| 型号        | 备注       |
|-----------|----------|
| OD_WE_OT1 |          |
| SL_MC_ND1 |          |
| SL_MC_ND2 |          |
| SL_MC_ND3 |          |
| SL_NATURE |          |
| SL_OL     |          |
| SL_OL_3C  |          |
| SL_OL_DE  |          |
| SL_OL_UK  |          |
| SL_OL_UL  |          |
| SL_OL_W   |          |
| SL_P_SW   |          |
| SL_S      |          |
| SL_SF_IF1 |          |
| SL_SF_IF2 |          |
| SL_SF_IF3 |          |
| SL_SF_RC  |          |
| SL_SPWM   |          |
| SL_SW_CP1 |          |
| SL_SW_CP2 |          |
| SL_SW_CP3 |          |
| SL_SW_DM1 |          |
| SL_SW_FE1 |          |
| SL_SW_FE2 |          |
| SL_SW_IF1 |          |
| SL_SW_IF2 |          |
| SL_SW_IF3 |          |
| SL_SW_MJ1 | 经过真实设备测试 |
| SL_SW_MJ2 | 经过真实设备测试 |
| SL_SW_MJ3 |          |
| SL_SW_ND1 |          |
| SL_SW_ND2 |          |
| SL_SW_ND3 |          |
| SL_SW_NS3 |          |
| SL_SW_RC  |          |
| SL_SW_RC1 |          |
| SL_SW_RC2 |          |
| SL_SW_RC3 |          |
| SL_SW_NS1 |          |
| SL_SW_NS2 |          |
| SL_SW_NS3 |          |
| V_IND_S   |          |

#### 门锁类：

| 型号                | 备注       |
|-------------------|----------|
| SL_LK_LS          | 经过真实设备测试 |
| SL_LK_GTM         |          |
| SL_LK_AG          |          |
| SL_LK_SG          |          |
| SL_LK_YL          | 经过真实设备测试 |
| SL_P_BDLK         |          |
| OD_JIUWANLI_LOCK1 |          |

#### 中枢控制器：

| 型号   | 备注       |
|------|----------|
| SL_P | 经过真实设备测试 |

#### 智能插座类：

| 型号            | 备注            |
|---------------|---------------|
| SL_OE_DE      | 支持计量，经过真实设备测试 |
| SL_OE_3C      | 支持计量          |
| SL_OL_W       | 支持计量          |
| OD_WE_OT1     | 此设备只有开关       |
| ~~SL_OL_UL~~  | 真有这个设备？       |
| ~~SL_OL_UK~~  | 真有这个设备        |
| ~~SL_OL_THE~~ | 真有这个设备？       |
| ~~SL_OL_3C~~  | 真有这个设备？       |
| ~~SL_O~~L     | 真有这个设备？       |

#### 窗帘电机类：

| 型号        | 备注       |
|-----------|----------|
| SL_SW_WIN | 窗帘控制开关   |
| SL_CN_IF  | 窗帘控制开关   |
| SL_CN_FE  | 窗帘控制开关   |
| SL_DOOYA  | DOOYA 电机 |
| SL_P_V2   | 智界窗帘电机   |

感谢以下项目 Thanks：
---
该项目参考了以下项目的数据和结构

- [@MapleEve 本人的重构 LifeSmart 的原始项目](https://github.com/MapleEve/hass-lifesmart)
- [@skyzhishui 提供的 custom_components](https://github.com/skyzhishui/custom_components)
- [@Blankdlh 提供的 hass-lifesmart](https://github.com/Blankdlh/hass-lifesmart)
- [@iKew 的重构](https://github.com/iKaew)
- [@likso 提供的 hass-lifesmart](https://github.com/likso/hass-lifesmart)

<h3 id="usertoken"> 如何获取 User Token 和 User ID </h3>

通过iLifeSmart后台的小工具拼接appkey,apptoken,回调地址、时间戳、did（可以为空）并在页面里面生成sign来访问用户页面进行授权
[访问小工具页面](http://www.ilifesmart.com/open/login#/open/document/tool)
点击`获取用户授权签名验证`然后参考下面 Python 脚本拼接，或者直接执行 Python 脚本

``` python
import time
import hashlib
tick = int(time.time())
appkey = "你的应用 APPKEY"
callbackurl = "http://localhost"
apptoken = "你的应用 APPK TOKEN"
sdata = "appkey=" + appkey
sdata += "&auth_callback=" + callbackurl
sdata += "&time=" + str(tick)
sdata += "&apptoken=" + apptoken
sign = hashlib.md5(sdata.encode(encoding='UTF-8')).hexdigest()
url = "https://api.ilifesmart.com/app/auth.authorize?id=001&"
url += "&appkey=" + appkey
url += "&time=" + str(tick)
url += "&auth_callback=" + callbackurl
url += "&sign=" + sign
url += "&lang=zh"
print(url)
```

脚本运行之后会打印一个地址，浏览器访问这个地址，用你的 `Lifesmart APP` 帐号密码登录即可从浏览器跳转到空页面 URI 中获取到
User ID、User Token、Token 过期时间、和优选的 API 域名地址 **官方文档中提到必须使用这个区域，否则 Websocket 请求有问题**

#### 注意：每次通过这个方法授权得到的 User Token 有效期为一年，你需要在到期前重新构建方法获取新的 Token

后面会考虑用用户名和密码模式，不过用户名和密码模式不是很稳定

<h3 id="lock"> 如何获取门锁设备的授权 </h3>

1. 请使用应用的注册邮箱相同发邮件联系 LifeSmart 的官方邮箱 service(at)ilifesmart.com
2. 注明需要修改的应用名称，应用 AppKey，或者在应用详情的 URI 地址内提供应用的 AppID
3. 说明需要增加的权限 `DoorLock` 和 `DoorLock Control`

你就可以在近期收到回复，回复中会告诉你应用增加了权限

```text
亲爱的用户:
开发平台于 XXXX-XX-XX XX:XX:XX 收到消息:
您的应用(XXXXXXXXX)于 XXXX-XX-XX XX:XX:XX：Basic；DoorLock；DoorLock Control；通过
请及时前往平台进行处理。

此致
LifeSmart研发团队
```