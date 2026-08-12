# Research: Ontario G Test 真实路况互动驾驶游戏

**日期**: 2026-08-12
**Owner**: nieyuanyuan
**状态**: Draft
**源项目 / 分支**: `my-ai-brain / main`
**源 commit / 版本**: `fcc5245`
**相关请求 / 问题**: 基于个人历次 G 牌考试经历与 Newmarket DriveTest Centre（320 Harry Walker Parkway S）周边真实道路，设计可发布到 `https://nieyy.github.io/` 的第一视角互动式考前训练游戏。

## 修订记录

| 版本 | 日期 | 作者 | 摘要 |
|---|---|---|---|
| v0.1 | 2026-08-12 | nieyuanyuan | 初版调研：需求提炼、真实路线证据、玩法选择、技术方案与分阶段落地建议。 |
| v0.2 | 2026-08-12 | nieyuanyuan | 确认 7 次失败及考点分布，核对现存 3 份原始评分表并补充去身份化时间线。 |
| v0.3 | 2026-08-12 | nieyuanyuan | 明确静态互动网页足以支持核心备考目标，取消实时 3D 和连续驾驶作为主路线。 |

## 1. 摘要

- 调研问题: 能否用 Newmarket 实际考点周边道路，做一个约 15–20 分钟、第一视角、有考官指令和结果复盘的网页训练游戏，并部署到 GitHub Pages？
- 简短结论: **完全可行，而且不需要实时 3D。** 推荐把产品定义为部署在 GitHub Pages 上的“第一视角场景化互动备考应用”：用实景照片、道路插画、短视频或可选的 360° 全景呈现情境；由浏览器端脚本负责考官语音、计时、分支决策、动作记录、评分、回放和弱项训练。它与普通静态网页使用相同的发布方式，但加载后可以运行完整的交互逻辑。
- 建议下一步: 先建立 Newmarket 路线与场景数据集，完成一个 5–8 分钟垂直切片：红灯右转完整停车、多车道左转入正确车道、404 高速并入三类场景。验证互动流程是否能训练判断、复盘是否减少遗漏、手机/桌面体验和素材可读性后，再扩展成 15–20 分钟完整模拟考试。
- 置信度: **High**（静态互动网页可承载核心产品）；**Medium**（训练内容完整性）。7 次失败及考点分布已确认，现有原始评分表覆盖其中 3 次；精确考点路线和其余 4 次考试细节仍未完整验证。

## 2. 范围

**范围内**:

- Newmarket G 牌考试相关道路、考试任务和个人高频错误的需求提炼。
- 第一视角驾驶训练的玩法、反馈方式、内容模型和评分模型。
- 静态图片/插画、预录视频、360° 全景和可选地图服务等素材路线比较。
- 可在 GitHub Pages 部署的前端架构、数据与 API 安全边界。
- MVP 范围、阶段计划、验证方法、法律/许可和安全风险。

**范围外**:

- 本文不宣称掌握 DriveTest 的官方固定考试路线；实际路线与考官指令可能变化。
- 不实现游戏、不创建 GitHub Pages 仓库、不采购地图服务。
- 不替代 MTO 官方手册、合格教练或真实道路练习，也不保证通过考试。
- 不根据尚未纳入调研的原始图片/PDF，猜测每次考试的完整扣分项。
- 不在首版实现方向盘级车辆动力学、碰撞物理、多人模式或真实交通实时同步。
- 不把实时 3D 渲染或自由驾驶作为产品成立的前提。

**假设**:

- 用户已确认 G 牌考试共失败 7 次，其中 Lindsay 4 次、Newmarket 3 次；目前仅保留 3 份原始评分表。
- 首要目标是训练观察和决策，而不是复刻车辆操控手感。
- 首版面向桌面键盘/鼠标，并兼容手机触控；方向盘外设不属于 MVP。
- 游戏以中文为主，考官语音建议同时支持英文原句和中文字幕。
- 首版不登录、不上传成绩，进度仅保存在本地浏览器。

## 3. 调研方法

- 已查看的代码 / 文档:
  - `docs/templates/research-doc-template-zh.md`
  - `CLAUDE.md`
  - `scripts/validate_structure.py`
- 使用的命令或查询:
  - 汇总用户提供的个人历次考试复盘要点；原始记录不随本文公开。
  - 使用 Poppler 将现存 3 份 DriveTest `Record of G Examination` PDF 共 9 页渲染为图片，逐页核对勾选项、未通过原因和考官备注；同时检查 PDF 页数及表单属性。
  - 检查 `https://nieyy.github.io/` HTTP 状态和 `nieyy` 账号可访问仓库。
  - 检索 Ontario MTO、DriveTest、Town of Newmarket、GitHub Pages、Google Maps Platform、MapLibre 和 OpenStreetMap 官方资料。
  - 检索 Newmarket 路线的近期用户报告；此类来源仅用于发现候选道路，不视为官方路线证明。
  - 检索驾驶危险感知训练的系统综述和实验研究。
- 已检查的外部参考:
  - Ontario MTO 官方驾驶手册、Newmarket 市政府限制教学区域资料。
  - GitHub Pages、Vite 静态部署、浏览器本地存储/PWA，以及可选的 Google Street View、MapLibre 和 OSM 官方文档。
  - 2024 年危险感知训练系统综述与北美视频危险感知研究。
- 未验证的内容:
  - 其余 4 次失败的日期、逐项扣分和考官备注缺少原始评分表，不能从记忆反推。
  - DriveTest 不公开保证某条固定路线；第三方和 Reddit 路线报告只能证明“曾有人走过”。
  - 每个候选路段当前 Street View 的拍摄日期、行驶方向、连接完整性和临时施工状态尚未逐点检查。
  - 根地址 `nieyy.github.io` 当前没有用户站点，但不影响通过独立项目仓库发布到 `nieyy.github.io/<项目名>/`。

## 4. 调研内容

### 4.1 当前状态

| 区域 / 模块 | 当前行为 | 证据 |
|---|---|---|
| 失败次数与分布 | 共失败 7 次：Lindsay 4 次、Newmarket 3 次 | 用户确认；现存 3 份原始评分表可核验其中 Lindsay 2 次、Newmarket 1 次 |
| 近期典型问题 | 包括高速并入速度与空间判断、多车道转弯车道选择、跟随慢车时的决策 | 个人考试复盘提炼；逐项结论仍应以原始评分表和官方规则为准 |
| 其他典型问题 | 包括红灯右转的完整停车，以及黄灯时停车或继续通过的情境判断 | 个人考试复盘提炼；本文仅用于形成训练场景，不公开原始记录 |
| 官方 G Test 范围 | 目前仍测试主要道路/高速、汇入驶离、合理速度和空间、转弯、变道、路口和商业区；暂不包含平行停车、路边停车、三点掉头和住宅区驾驶 | [MTO Level Two Road Test](https://www.ontario.ca/document/official-mto-drivers-handbook/level-two-road-test)（更新于 2025-09-08） |
| Newmarket 考点 | DriveTest 地址为 320 Harry Walker Parkway S，并提供 G 测试 | [DriveTest Centre List](https://drivetest.ca/find-a-drivetest-centre/alphabetical_list/) |
| 候选道路范围 | 市政府明确称限制教学区域覆盖 Newmarket DriveTest Centre 的各种考试路线，边界涉及 Gorham、Prospect、Bayview、Traviss、Leslie Valley、Leslie；Davis Drive 是主要通行道路 | [Town of Newmarket Restricted Area](https://www.newmarket.ca/resident-services/by-law-enforcement/restricted-area-driving-instructors-driving-schools) |
| 候选高速链路 | 近期和历史用户报告多次出现 Harry Walker / Davis / Leslie、404 North、Green Lane、404 South，但也明确存在路线变化 | [2025 用户报告](https://www.reddit.com/r/Ontariodrivetest/comments/1mcz55n)、[2022 用户报告](https://www.reddit.com/r/Ontariodrivetest/comments/zzgwu1)；仅作线索，不作为官方路线 |
| 教学限制 | Newmarket 的指定区域禁止驾驶教练/驾校为教学或备考而运营；私人车辆和正式考试不在该项禁止范围内 | [Town of Newmarket Restricted Area](https://www.newmarket.ca/resident-services/by-law-enforcement/restricted-area-driving-instructors-driving-schools)；网页模拟不在道路上运营，但产品应醒目提示当地限制 |
| 发布目标 | 可直接使用独立项目站点，例如 `https://nieyy.github.io/ontario-g-test/`；不要求先建立根站点 | GitHub Pages 支持项目站点；Vite 官方说明项目站点只需设置对应 `base` 路径 |

#### 可验证的失败时间线（现存原始评分表）

为保护隐私，本文只保留与训练设计有关的日期、考点和去身份化评分摘要，不记录姓名、车牌、考官 ID 或签名。考点名称由用户确认，评分表中的 Location code 用于交叉核对。

| 日期 | 考点 | 原始结果 | 可验证的主要问题 | 对游戏场景的启示 |
|---|---|---|---|---|
| 2026-02-10 | Lindsay（`D68`） | `Dangerous Action`；考官进行 verbal/steering intervention | 左转时未正确处理路权，并在红灯阶段未及时清空路口；另有转弯观察、车道/速度、高速观察和信号等扣分 | 强化“进入路口前能否完成左转”的动态判断，以及考官多次提醒后仍未纠正时的危险行为判定 |
| 2026-05-14 | Lindsay（`D68`） | `Too many driving errors` | 没有单一考官干预事件；扣分分散在转弯观察与速度、商业区危险观察、高速进入/行驶/驶离等项目 | 游戏不能只训练一两个致命点，还需要完整 15–20 分钟流程检测累计性普通错误 |
| 2026-08-12 | Newmarket（`D52`） | `Dangerous Action` + `Inadequate skill to complete test`；两次 verbal intervention | 一次转弯影响其他车辆；一次以约 60 km/h 并入 Highway 404，迫使主路车辆避让；评分表另标记转弯车道、进入高速前盲区检查和高速速度处理 | 把“速度匹配 + gap + 前车空间 + 肩检”作为一个整体场景评分，不能简化成“必须达到 110” |

当前原始证据覆盖率为 `3/7`。这三份报告已经显示两种不同失败机制：一类是单次危险行为直接导致失败，另一类是普通错误累积过多导致失败。游戏评分系统必须同时支持这两类路径。

#### 从个人复盘得到的训练题库种子

| 场景 | 不应教成的死规则 | 应训练的动态判断 | 可观测动作 |
|---|---|---|---|
| 红灯右转 | “红灯可以右转” | 无禁止标志时，也必须先完全停车、观察、让行，确认安全后才转 | 制动到 0、停留、左右观察、盲区检查、起步时机 |
| 黄灯 | “黄灯一定冲”或“黄灯一定急刹” | 根据停止线距离、速度、后车和制动安全判断能否安全停车 | 反应时间、制动强度、停止位置、是否越线 |
| 多车道左转 | “左转后找空车道即可” | 根据出发车道、地面导向线和标志保持对应转弯轨迹 | 进弯前选道、转弯轨迹、出弯所在车道 |
| 高速并入 | “必须到 110 才能并” | 在加速车道尽量匹配主路车流，同时保持前车空间并选择不会迫使主路车辆刹车的 gap | 前车间距、加速曲线、镜/肩检、gap、并入时速度差 |
| 高速跟慢车 | “前车低于 110 就必须超” | 先保持安全车距；只有持续明显偏慢、整体车流允许且左侧安全时才考虑正常超车 | 跟车距离、观察、是否做无意义/危险变道 |
| 高速驶离 | “看到出口先减速” | 先完整进入减速车道，再逐步降到匝道建议速度 | 信号、盲区检查、进入出口车道位置、减速时机 |

这些规则与 MTO 官方说明一致：红灯右转前须完全停车；高速加速车道用于匹配车流速度；不得低速切入快车前方；变道要检查镜子和盲区；驶离高速时不应在完全进入减速车道前降速。来源见[交通灯](https://www.ontario.ca/document/official-mto-drivers-handbook/traffic-lights)、[高速驾驶](https://www.ontario.ca/document/official-mto-drivers-handbook/freeway-driving)、[改变方向](https://www.ontario.ca/document/official-mto-drivers-handbook/changing-directions)和[Level Two Road Test](https://www.ontario.ca/document/official-mto-drivers-handbook/level-two-road-test)。

### 4.2 关键链路 / 机制

#### 建议的玩家体验

```text
[选择 Newmarket 模拟考试]
  -> [考官说明：不辅导、只下指令]
  -> [15–20 分钟第一视角路线]
  -> [沿途生成交通与信号情境]
  -> [记录观察、速度、空间、信号、车道、路权]
  -> [严重错误可结束考试，但允许玩家选择继续练完]
  -> [按时间线回放：当时看到什么、做了什么、正确判断依据]
  -> [把错误加入个人弱项训练]
```

![互动式 G Test 学习闭环](assets/2026-08-12-ontario-g-test-interactive-learning-loop-zh.png)

图 1：真实道路模拟、事件记录与评分、时间线复盘和个人弱项再训练构成闭环。考试模式只给考官指令，不提供即时答案；练习模式才允许暂停、提示和重做。

- 重要行为:
  - 考试模式中不给即时答案，避免“边开边教”破坏测评；练习模式才允许暂停、提示和重做。
  - 考官指令应像真实考试一样只说明方向或动作，例如 “At the next intersection, turn left”，不能提前透露正确操作。
  - 关键动作不只用方向键表达。首版可用明确、可评分的输入：油门/刹车、转向灯、镜子、左右肩检、选择目标车道、接受/拒绝 gap。
  - 每个场景都保存上下文和事件时间线，报告不能只写“并线不好”，而要写成“并入时 67 km/h，主路目标车约 103 km/h，迫使后车减速；并入前 4.2 秒未做左肩检”。
  - 路线与规则分离：路线负责位置和考官指令，场景负责交通状态和评分，因此同一路段可以随机生成不同前车速度、gap 和灯色。
- 边界 / 归属:
  - 地图/街景负责“真实地点感”，自有场景引擎负责考试逻辑；不能依赖街景照片中的静态车辆来判断动态交通。
  - 官方规则库与个人错误库分离。个人错误可提高出题权重，但不能改写法定规则。
  - 路线必须标注 `official / municipal-boundary / community-reported / authored` 证据等级；当前没有路线可标为 `official`。
- 运行时或运维注意点:
  - 核心流程由预定义场景和事件驱动，不依赖逐帧渲染或服务器计算；每个场景只需加载图片、短视频、音频和 JSON 数据。
  - 实景素材、360° 全景和地图都是可替换的表现层，不能决定评分结果；某类素材不可用时应降级为插画或文字场景。
  - GitHub Pages 只能托管静态资源。排行榜、跨设备账号、服务端密钥和动态内容审核需要另加后端。
  - 首版成绩保存在浏览器本地即可；若使用付费地图服务，浏览器中的 key 无法成为真正秘密，必须限制域名/API 并设置配额。

#### 建议的数据模型

```json
{
  "routeId": "newmarket-g-community-v1",
  "evidence": "community-reported",
  "durationMinutes": 18,
  "segments": [
    {
      "id": "hwy404-north-merge",
      "location": { "lat": 0, "lng": 0, "heading": 0 },
      "examinerPrompt": {
        "en": "Enter the highway when safe.",
        "zh": "安全时驶入高速。"
      },
      "scenario": "freeway-merge",
      "parameters": {
        "leadVehicleSpeedKph": [65, 95],
        "mainTrafficSpeedKph": [95, 110],
        "gapSeconds": [1.5, 4.5]
      },
      "rubric": [
        "following-space",
        "mirror-check",
        "blind-spot-check",
        "speed-match",
        "safe-gap"
      ]
    }
  ]
}
```

经纬度应在路线核验阶段填写；此处用 `0` 避免把未验证坐标写成事实。

### 4.3 关键发现

#### Finding 1: 最需要模拟的是“情境判断链”，不是背诵文字规则

- 证据: 个人复盘显示，问题集中在同一规则随上下文变化的判断：黄灯是否停、前车慢时是否超、何时接受高速 gap，也存在把“匹配车流”简化成“必须 110”的风险。2024 年 57 篇研究的系统综述/Meta-analysis 发现，危险感知训练对驾驶员有显著改善，主动训练比被动训练更稳定；北美视频研究也发现新手对危险反应更慢。
- 为什么重要: 游戏必须呈现变化的车流、信号时机和空间，而不是把文字总结换成选择题皮肤。相同知识点至少需要 3–5 个参数化变体，防止玩家只记答案。
- 置信度: High。

#### Finding 2: 静态托管不限制浏览器内交互，足以覆盖核心学习闭环

- 证据: GitHub Pages 可以发布任意静态构建产物；Vite 官方提供 GitHub Pages 构建部署方案。发布后的 HTML/CSS/JavaScript 可以在浏览器内运行状态机、音频、计时、动画、分支、评分和本地存储。PWA manifest 和可选 service worker 还能提供安装入口与部分离线能力。
- 为什么重要: 本项目需要的是“看见具体路况—听取指令—做判断—看到后果—复盘弱项”，不是车辆物理仿真。图片、短视频或 360° 场景配合确定性事件引擎已经足够；实时 3D 会增加成本，却不是验证学习价值的必要条件。
- 置信度: High。

| 需求 | 纯 GitHub Pages 静态应用 | 是否需要后端 |
|---|---|---|
| 15–20 分钟模拟考试、倒计时、暂停/继续 | 支持 | 否 |
| 考官英文语音、中文字幕、音效 | 支持预录音频；也可用浏览器语音能力作后备 | 否 |
| 第一视角图片、插画、短视频、360° 场景 | 支持 | 否；第三方素材服务可能需要 API |
| 转向灯、刹车、镜子、肩检、选道、gap 判断 | 支持 HTML/SVG/Canvas 控件和键盘/触控输入 | 否 |
| 分支剧情、严重错误、累计扣分、考试报告 | 支持浏览器端确定性状态机 | 否 |
| 本机历史、个人弱项、重练错题 | 支持 localStorage/IndexedDB | 否 |
| 分享某个公开场景或挑战 | 支持 URL 参数或静态路由 | 否 |
| 跨设备同步、登录、公开排行榜、多人实时考试 | GitHub Pages 本身不提供 | **是**；不属于 MVP |

#### Finding 3: Newmarket 有可信的候选走廊，但没有可保证的固定考试路线

- 证据: Newmarket 市政府明确说明限制区域涵盖该中心的各种考试路线，证明 Harry Walker 周边确实存在多条路线；多份不同年份的用户报告反复出现 Davis、Leslie、Highway 404 和 Green Lane，但 2025 用户也明确表示实际路线与 YouTube 不同。
- 为什么重要: 产品命名应使用“Newmarket 风格模拟考试”或“基于社区报告的路线”，而不是“官方原题路线”。训练目标应覆盖道路类型和技能迁移，不能让用户误以为背路线即可通过。
- 置信度: High（存在候选走廊）；Low（任何一条具体路线会在下次考试出现）。

#### Finding 4: GitHub Pages 足够托管 MVP，第三方地图服务只是可选成本

- 证据: GitHub Pages 可通过自定义 Actions 工作流部署静态构建产物，站点软带宽上限为每月 100 GB、发布站点不超过 1 GB；Google Dynamic Street View 按成功加载全景对象计费；OSM 数据开放，但公共 `tile.openstreetmap.org` 不是无限免费 CDN，禁止预取/离线抓取并要求清晰署名。
- 为什么重要: 游戏逻辑和自有静态素材可以零服务器部署。只有选择真实街景或在线地图时，才需要 Google Cloud billing、域名/API 限制和成本监控；不能把大量地图瓦片打包进仓库或滥用公共 OSM tile 服务。
- 置信度: High。

#### Finding 5: 首版应同时提供“考试模式”和“复盘模式”

- 证据: Ontario 官方说明考官会下指令但不允许在考试过程中 coaching；个人痛点则是事后文字总结遗漏具体上下文。
- 为什么重要: 真实感要求考试中保持沉默和压力，学习效果要求考试后能逐帧解释。两者混在一起会既不像考试，也难形成记忆。
- 置信度: High。

### 4.4 GAP 和风险

| GAP / 风险 | 影响 | 证据 | 严重程度 |
|---|---|---|---|
| 原始评分表仅覆盖 3/7 | 其余 4 次无法建立逐项可追溯错误时间线，也可能漏掉反复出现的问题 | 用户确认只找到 3 份原始评分表；三份均已逐页核对 | High |
| 路线并非官方固定 | 若宣传“真实官方路线”，会误导用户并快速过时 | 市政府称存在 various routes；社区报告相互有差异 | High |
| 静态场景不训练真实车控 | 无法验证方向盘力度、真实制动距离和车辆空间感 | 产品定位是判断与观察训练，不替代真实道路练习 | Medium |
| 静态照片不能生成动态交通 | 照片里的车不能代表本次模拟的 gap、速度和意图 | 动态交通必须由自有 overlay/场景引擎建立 | High |
| 可选 Google 服务的成本与条款 | 若后续启用，公开站点可能被刷量，缓存/抓取街景也受限 | Dynamic Street View 按成功 panorama load 计费；政策限制缓存，要求条款、隐私和署名 | Medium |
| 可选 API key 暴露 | 若使用付费地图服务，GitHub Pages 前端 key 可见 | Google 官方要求 website + API restrictions，并建议配额/监控 | Medium |
| Newmarket 教学限制区 | 产品文案若鼓励驾校在限制区带练，会造成合规问题 | Town of Newmarket Restricted Area By-law | Medium |
| 地图/路况变化 | 施工、限速、标线和 Street View 拍摄时间会造成训练偏差 | 地图和影像有日期，现实道路会变化 | Medium |
| 训练迁移不等于通过保证 | 游戏改善决策，不等于验证真实车控、观察动作幅度和压力表现 | 模拟器/视频研究支持训练价值，但真实道路仍有差异 | High |
| 素材与无障碍 | 图片中的标线/车辆在小屏幕上可能不清楚，视频也需字幕和替代说明 | 场景必须支持放大、键盘操作、字幕和文字替代 | Medium |
| 项目子路径配置错误 | 页面能打开但 JS、CSS、图片或前端路由 404 | Vite 项目站点需要把 `base` 设置为 `/<REPO>/`，并按 Pages 路径测试刷新 | Medium |

## 5. 可选方向

| 方案 | 描述 | 适合场景 | 成本 / 风险 | 建议 |
|---|---|---|---|---|
| A. 静态素材 + 场景引擎 | 第一视角照片/插画、局部动画和考官语音；在关键节点让玩家停车、观察、选道、接受/拒绝 gap，并按事件评分 | 个人弱项训练、15–20 分钟模拟考试、低成本公开分享 | 不训练连续车控；场景素材需要认真制作和审核 | **MVP 主方案** |
| B. 短视频/分支视频 | 第一视角视频到决策点暂停或分支，记录反应时间和选择 | 黄灯、行人、并线 gap 等动态危险感知 | 自采与剪辑成本较高；需处理车牌/人脸和素材更新 | **高价值动态场景增强** |
| C. 360° 全景/Street View | 用可旋转全景训练镜子、肩检、路口扫描和真实地点识别 | 少量需要空间观察的 Newmarket 场景 | 第三方 API 可能有费用/条款；自采全景也需处理隐私 | **可选增强，不阻塞 MVP** |
| D. 实时 3D 驾驶 | 连续油门、转向、车辆和交通物理 | 将来若要训练操控或支持方向盘外设 | 成本和复杂度远高于当前目标，且不能自动保证教学更有效 | **当前不做** |

### 方案 A 的建议界面

```text
┌──────────────────────────────────────────────────────────┐
│              第一视角照片 / 插画 / 短视频                │
│                                                          │
│  [左镜]        路口 / 车流 / 车道覆盖层        [右镜]    │
│                                                          │
├──────────────────────────────────────────────────────────┤
│ 考官：At the next intersection, turn left.               │
│        在下一个路口左转。                                 │
│                                                          │
│ 速度 82   [左灯] [右灯] [左肩检] [右肩检]  档位 D       │
│ [刹车]────────────[油门]       小地图 / 路线进度  06:42  │
└──────────────────────────────────────────────────────────┘
```

避免在考试模式显示“正确目标车道”或“安全 gap”高亮；这些只在复盘模式叠加。

## 6. 建议

**建议方向**:

采用 **A 为主、B/C 作为素材增强** 的分阶段路线：

1. **内容底座**：先把官方规则、个人错误和 Newmarket 候选路段编码成结构化场景；画面素材只负责呈现，评分逻辑来自可测试的规则和事件。
2. **垂直切片**：用第一视角静态素材 + TypeScript 场景状态机做 5–8 分钟互动考试，覆盖红灯右转、多车道左转和 404 并入。
3. **完整 MVP**：扩展到 15–20 分钟，加入考试模式、练习模式、考官英文语音/中文字幕、结束报告、弱项训练和本地历史。
4. **动态素材增强**：只为黄灯、行人冲突和 404 并入等确实依赖时间变化的场景加入短视频或轻量动画。
5. **空间观察增强**：如果镜子/肩检训练确有需要，再为少量关键场景加入 360° 全景或 Street View，不影响其他场景运行。

**原因**:

- 直接针对“文字总结会漏”和“规则被记成绝对句”的根因：让用户在变化的情境中做决策，并得到逐事件证据。
- 保留真实地点的视觉记忆，同时把动态车流掌握在自己的场景引擎中。
- MVP 可以是纯静态前端，适配 GitHub Pages；不需要先建设账号、数据库和服务器。
- 架构与素材解耦，未来可替换更真实的照片、视频或全景，而不重写评分与复盘逻辑。

**建议技术栈**:

| 层 | 建议 | 说明 |
|---|---|---|
| 构建 | Vite + TypeScript | 适合静态部署、资源路径和代码分包 |
| UI | React（或 Preact） | 场景 HUD、报告、设置；如追求更小体积可选 Preact |
| 状态 | 纯 TypeScript 有限状态机；复杂后再评估 XState | 保证每个指令、输入、扣分和回放可重现 |
| 场景画面 | 响应式图片、SVG、HTML overlay；按需加入 `<video>` 或 360° viewer | 静态素材优先，动态素材只服务必须观察时间变化的题目 |
| 小地图 | 简化 SVG 路线图；确有需要再引入 MapLibre | MVP 只需显示进度和道路关系，不必先接在线地图 |
| 轻量动画 | CSS/SVG/Canvas | 交通灯、车辆位置、目标车道和操作反馈；不需要 WebGL |
| 音频 | 预录考官语音优先，Web Speech API 作为后备 | 预录能保证措辞、节奏和跨浏览器一致性 |
| 内容 | 版本化 JSON/GeoJSON | 路线、场景、rubric 与证据等级可独立审核 |
| 本地进度 | IndexedDB 或 localStorage | 首版不收集个人信息、不需要后端；可导出/导入 JSON 备份 |
| 可安装/离线 | Web App Manifest；验证需求后再加 service worker | 可以像 App 一样添加到主屏幕；避免过早缓存受条款限制的第三方地图素材 |
| 测试 | Vitest + Playwright | 评分状态机单测、完整考试流程与响应式 E2E |
| 发布 | GitHub Actions → GitHub Pages | 建议独立项目仓库，部署到 `nieyy.github.io/<项目名>/`，Vite 设置匹配的 `base` |

**计分建议**:

- 五个维度：`Observation`、`Speed`、`Space`、`Signals`、`Lane/Right-of-way`。
- 错误分三级：普通错误、影响其他道路使用者的严重错误、考官干预/碰撞级危险行为。
- 分数不应只有总分；报告先显示前三个“下一次最值得改”的行为，再提供完整时间线，避免再次淹没在文字里。
- 每个结论包含四项：`时间/位置`、`当时情境`、`玩家动作`、`官方依据`。
- 考试结束后允许点击“只练这类 Bug”，自动生成相同规则、不同交通参数的短练习。

**应该进入设计文档的内容**:

- 产品信息架构和考试/练习/复盘三种模式的交互流程。
- `Route`、`Segment`、`Scenario`、`Prompt`、`ActionEvent`、`Rubric`、`Attempt` 的数据契约。
- 场景播放器、考官指令、镜子/肩检输入、分支决策和素材降级策略。
- 严重错误判定、复盘时间线和个性化弱项权重算法。
- Google API key 限制、配额、隐私政策、地图署名和不可缓存内容边界。
- GitHub Pages 用户站点/项目站点选择和 base path 处理。

**不应该进入设计文档的内容**:

- 把任何社区路线写成 DriveTest 官方保证路线。
- 其余 4 次缺少原始评分表的逐项扣分结论。
- 首版完整城市建模、真实车辆动力学、云账号、排行榜和多人系统。
- “到达某个固定速度就一定正确”之类脱离车流环境的硬编码规则。

## 7. 验证要求

- 单元 / 组件:
  - 状态机对每个考官指令只触发一次，暂停/恢复后不重复扣分。
  - 红灯右转必须检测 `speed = 0`、停车顺序、观察和让行；仅按“右转”不能通过。
  - 黄灯场景由可停车距离、当前速度和后车状态决定，测试“安全停”和“安全通过”两类正确答案。
  - 并入评分同时考虑速度差、前车距离、后车 gap、镜子/盲区和是否迫使主路车制动。
  - 多车道转弯按起始车道和路面导向线计算目标车道，不能固定写成“永远进最左车道”。
- 集成 / workflow:
  - 图片、视频或可选全景素材加载失败时，能够切换到备用图片和文字说明，考试状态不丢失。
  - 一次考试的所有输入事件能重放，并生成同样评分。
  - 中英文指令、字幕、语音和路线事件保持同步。
  - API key 仅允许目标域名和需要的 API；超额时显示可理解的降级页面。
- 端到端 / 运维:
  - Chrome、Safari、Firefox 桌面端完成 20 分钟流程；移动端完成触控流程。
  - 首屏和路线资源按需加载，发布物控制在 GitHub Pages 限额内。
  - 建立 Google Cloud 每日配额、预算告警和用量监控；公开发布前做滥用测试。
  - 项目子路径下首页、素材和可分享场景 URL 均不返回 404；优先用 hash 或显式静态入口避免 Pages 缺少 SPA fallback 的问题。
- 回归:
  - 路线数据更新不能改变既有 attempt 的评分；attempt 保存内容版本号。
  - 官方规则引用含 `checkedAt` 日期；定期检查失效链接和规则变化。
  - 地图 attribution、免责声明和隐私链接在桌面/手机上始终可见。
- 负向 / 失败场景:
  - 断网、素材加载失败、浏览器禁音、视频不能自动播放、本地存储空间不足。
  - 用户连续乱按肩检/信号、长时间不操作、逆向选择节点、错过出口或拒绝所有 gap。
  - 晕动模式：关闭全景过渡、降低视角变化、允许键盘逐帧推进。
  - 严重错误发生后提供“结束考试”与“标记失败但继续练完”两种行为，报告必须保留首次危险事件。

## 8. Open Questions

- [x] 失败次数和考点分布：共 7 次，Lindsay 4 次、Newmarket 3 次；现存 3 份原始评分表已建立部分可验证时间线，其余 4 次缺少原始材料。
- [ ] 第一版是“只服务用户个人弱项”，还是一开始就面向所有 Ontario G 考生？这决定内容编辑器和隐私边界。
- [x] 产品定位采用第一视角场景式互动备考，不要求方向盘级连续驾驶或实时 3D。
- [ ] MVP 是否完全使用自有/可授权静态素材，还是为少量场景启用 Google Street View？后者是可选增强，不影响核心产品。
- [x] MVP 采用独立项目仓库和项目子路径发布，例如 `nieyy.github.io/ontario-g-test/`；无需先创建根站点。
- [ ] 游戏项目是否单独建仓库，还是放入现有 `my-ai-brain`？建议单独仓库，记忆库只保留调研/设计/复盘。
- [ ] 是否需要收集匿名使用数据？MVP 建议不收集；若以后加入，需独立隐私设计和用户同意。
- [ ] 是否有条件安全、合法地录制 Newmarket 路段的第一视角/360° 素材？
- [ ] 哪三次失败最有代表性，除当前已知五类错误外还有哪些反复出现？
- [ ] 是否需要考官指令完全使用 Ontario 考试常见英文措辞，并由本地教练/近期考生审核？

## 9. 来源记录

| 来源 | 日期 / 版本 | 备注 |
|---|---|---|
| 个人历次 G 牌考试复盘 | 汇总于 2026-08-12 | 私有记录，不随本文公开；仅用于提炼需求和训练场景，事实以原始评分表及官方规则为准 |
| 3 份 DriveTest `Record of G Examination` 原始评分表 | 2026-02-10、2026-05-14、2026-08-12 | 私有 PDF，不入库；共 9 页已逐页渲染核对。覆盖 Lindsay 2 次、Newmarket 1 次，文中只保留去身份化摘要 |
| [MTO: The Level Two Road Test](https://www.ontario.ca/document/official-mto-drivers-handbook/level-two-road-test) | 更新于 2025-09-08，读取于 2026-08-12 | 当前 G Test 范围、考官行为、任务与评分动作 |
| [MTO: Freeway driving](https://www.ontario.ca/document/official-mto-drivers-handbook/freeway-driving) | 更新于 2026-06-01，读取于 2026-08-12 | 加速车道、匹配车流、避免 cut-off、驶离高速 |
| [MTO: Traffic lights](https://www.ontario.ca/document/official-mto-drivers-handbook/traffic-lights) | 更新于 2026-03-31，读取于 2026-08-12 | 红灯右转前完整停车与让行 |
| [MTO: Changing directions](https://www.ontario.ca/document/official-mto-drivers-handbook/changing-directions) | 更新于 2026-03-31，读取于 2026-08-12 | 左右转、车道、观察与路权 |
| [MTO: Stopping](https://www.ontario.ca/document/official-mto-drivers-handbook/stopping) | 读取于 2026-08-12 | 红灯/停牌必须完整停车 |
| [Ontario: Raising speed limits](https://www.ontario.ca/page/raising-speed-limits-ontario-highways) | 读取于 2026-08-12 | Highway 404 Newmarket 至 Woodbine 的 110 km/h 路段；实际以现场标志为准 |
| [DriveTest Centre List](https://drivetest.ca/find-a-drivetest-centre/alphabetical_list/) | 读取于 2026-08-12 | Newmarket 地址与可用考试类型 |
| [Town of Newmarket: Restricted Area](https://www.newmarket.ca/resident-services/by-law-enforcement/restricted-area-driving-instructors-driving-schools) | 读取于 2026-08-12 | 考试路线大致区域与驾驶教学限制 |
| [Newmarket 2025 route report](https://www.reddit.com/r/Ontariodrivetest/comments/1mcz55n) | 2025-07-30 | 非官方个案；指出路线可能不同于 YouTube，并提供一条 404 往返经历 |
| [Newmarket 2022 route report](https://www.reddit.com/r/Ontariodrivetest/comments/zzgwu1) | 2022-12-31 | 非官方个案；用于交叉发现 Harry Walker/Gorham/Leslie/Davis/404/Green Lane 候选走廊 |
| [Google StreetViewPanorama reference](https://developers.google.com/maps/documentation/javascript/reference/street-view) | 读取于 2026-08-12 | 全景位置、POV、links 和事件能力 |
| [Google Street View service](https://developers.google.com/maps/documentation/javascript/streetview) | 读取于 2026-08-12 | JavaScript Street View 行为与计费触发说明 |
| [Google Maps API usage details](https://developers.google.com/maps/billing-and-pricing/sku-details) | 读取于 2026-08-12 | Dynamic Street View 按成功加载全景对象计费 |
| [Google Maps security guidance](https://developers.google.com/maps/api-security-best-practices) | 读取于 2026-08-12 | 网站/API key 限制、配额与监控 |
| [Google Maps JavaScript API policies](https://developers.google.com/maps/documentation/javascript/policies) | 读取于 2026-08-12 | 缓存、署名、Terms/Privacy 要求 |
| [MapLibre GL JS](https://maplibre.org/projects/gl-js/) | 读取于 2026-08-12 | WebGL、3D、Three.js/custom layer 能力 |
| [MapLibre Three.js example](https://maplibre.org/maplibre-gl-js/docs/examples/add-a-3d-model-using-threejs/) | 读取于 2026-08-12 | 地理定位 3D 模型和共享 WebGL canvas 示例 |
| [OpenStreetMap Tile Usage Policy](https://operations.osmfoundation.org/policies/tiles/) | 读取于 2026-08-12 | attribution、禁止预取、公共 tile 无 SLA |
| [GitHub Pages limits](https://docs.github.com/en/pages/getting-started-with-github-pages/github-pages-limits) | 读取于 2026-08-12 | 站点大小、带宽和构建限制 |
| [GitHub Pages custom workflows](https://docs.github.com/en/pages/getting-started-with-github-pages/using-custom-workflows-with-github-pages) | 读取于 2026-08-12 | Actions 部署静态构建产物 |
| [GitHub Pages: Creating a site](https://docs.github.com/en/pages/getting-started-with-github-pages/creating-a-github-pages-site) | 读取于 2026-08-12 | Pages 可发布静态文件，也可用 Actions 发布自定义构建产物 |
| [Vite: Deploying a Static Site](https://vite.dev/guide/static-deploy.html) | 读取于 2026-08-12 | Vite 构建输出和 GitHub Pages 项目子路径部署方式 |
| [MDN: What is a PWA](https://developer.mozilla.org/en-US/docs/Web/Progressive_web_apps/Guides/What_is_a_progressive_web_app) | 读取于 2026-08-12 | 静态 Web App 可安装，service worker 可选用于离线能力 |
| [MDN: Client-side storage](https://developer.mozilla.org/en-US/docs/Learn_web_development/Extensions/Client-side_APIs/Client-side_storage) | 读取于 2026-08-12 | 浏览器端保存练习历史和较大本地数据的能力 |
| [Prabhakharan et al., Hazard perception training meta-analysis](https://pubmed.ncbi.nlm.nih.gov/38701558/) | 2024 | 57 篇研究；主动训练对驾驶员危险感知改善更稳定 |
| [Scialfa et al., A hazard perception test for novice drivers](https://doi.org/10.1016/j.aap.2010.08.010) | 2011 | 北美视频危险感知任务能区分新手与有经验驾驶员 |

## 10. 结论

- 是否进入设计: **Yes**
- 要创建的设计文档: `docs/designs/2026-08-xx-ontario-g-test-interactive-game-mvp-design-zh.md`
- 实现前还需要补充的调研:
  - 建立候选路线的场景清单，并为每个场景确定可授权的照片、插画、短视频或可选全景素材。
  - 以现存 3 份评分表建立“错误—官方规则—游戏场景”的可追溯矩阵；其余 4 份若以后找回，再按同一数据模型补录，不从记忆猜测具体扣分。
  - 若决定为少量场景使用 Google Street View，再单独确认预算、许可、域名限制和实际计费行为；不把它设为 MVP 前置条件。
  - 找 1 名 Ontario 合格驾驶教练或近期 Newmarket G 考生审核考官措辞、路线候选和评分 rubric。
  - 制作低保真交互原型，验证“第一视角素材 + 考官语音 + 操作决策 + 时间线复盘”是否足够有效；只对确实依赖动态变化的场景追加短视频或轻量动画。
