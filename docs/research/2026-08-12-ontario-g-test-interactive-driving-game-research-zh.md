# Research: Ontario G Test 真实路况互动驾驶游戏

**日期**: 2026-08-12
**Owner**: nieyuanyuan
**状态**: Draft
**源项目 / 分支**: `my-ai-brain / main`
**源 commit / 版本**: `64df3aa`
**相关请求 / 问题**: 基于个人历次 G 牌考试经历与 Newmarket DriveTest Centre（320 Harry Walker Parkway S）周边真实道路，设计可发布到 `https://nieyy.github.io/` 的第一视角互动式考前训练游戏。

## 修订记录

| 版本 | 日期 | 作者 | 摘要 |
|---|---|---|---|
| v0.1 | 2026-08-12 | nieyuanyuan | 初版调研：需求提炼、真实路线证据、玩法选择、技术方案与分阶段落地建议。 |

## 1. 摘要

- 调研问题: 能否用 Newmarket 实际考点周边道路，做一个约 15–20 分钟、第一视角、有考官指令和结果复盘的网页训练游戏，并部署到 GitHub Pages？
- 简短结论: **可行，但 MVP 不应从完整 3D 驾驶模拟器起步。** 推荐先做“Google Street View 真实街景 + 脚本化场景/评分引擎 + MapLibre 小地图”的互动考试。玩家处于第一视角，听取副驾驶考官指令，在关键时刻完成观察、减速、停车、选道、并线和变道判断。它能优先解决“文字总结容易遗漏、死记规则不能覆盖动态情境”的痛点，也能在纯静态站点上运行。
- 建议下一步: 先建立 Newmarket 路线与场景数据集，完成一个 5–8 分钟垂直切片：红灯右转完整停车、多车道左转入正确车道、404 高速并入三类场景。验证街景连续性、交互是否能训练判断、手机/桌面性能和 Google API 成本后，再扩展成 15–20 分钟完整模拟考试。
- 置信度: **Medium-High**。网页、部署和脚本化玩法技术可行性高；Street View 覆盖连续性、精确考点路线、个人七次考试的完整评分表仍需人工补证。

## 2. 范围

**范围内**:

- Newmarket G 牌考试相关道路、考试任务和个人高频错误的需求提炼。
- 第一视角驾驶训练的玩法、反馈方式、内容模型和评分模型。
- Google Street View、MapLibre/Three.js、预录视频/360° 素材等实现路线比较。
- 可在 GitHub Pages 部署的前端架构、数据与 API 安全边界。
- MVP 范围、阶段计划、验证方法、法律/许可和安全风险。

**范围外**:

- 本文不宣称掌握 DriveTest 的官方固定考试路线；实际路线与考官指令可能变化。
- 不实现游戏、不创建 GitHub Pages 仓库、不采购地图服务。
- 不替代 MTO 官方手册、合格教练或真实道路练习，也不保证通过考试。
- 不根据尚未纳入调研的原始图片/PDF，猜测每次考试的完整扣分项。
- 不在首版实现方向盘级车辆动力学、碰撞物理、多人模式或真实交通实时同步。

**假设**:

- 用户估计 G 牌考试失败约 7 次；准确次数和逐次扣分项尚待结合原始评分表确认。
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
  - 检查 `https://nieyy.github.io/` HTTP 状态和 `nieyy` 账号可访问仓库。
  - 检索 Ontario MTO、DriveTest、Town of Newmarket、GitHub Pages、Google Maps Platform、MapLibre 和 OpenStreetMap 官方资料。
  - 检索 Newmarket 路线的近期用户报告；此类来源仅用于发现候选道路，不视为官方路线证明。
  - 检索驾驶危险感知训练的系统综述和实验研究。
- 已检查的外部参考:
  - Ontario MTO 官方驾驶手册、Newmarket 市政府限制教学区域资料。
  - Google Maps JavaScript API / Street View、MapLibre GL JS、OSM tile policy、GitHub Pages 官方文档。
  - 2024 年危险感知训练系统综述与北美视频危险感知研究。
- 未验证的内容:
  - 早期考试图片和评分表未逐份纳入本次调研。
  - DriveTest 不公开保证某条固定路线；第三方和 Reddit 路线报告只能证明“曾有人走过”。
  - 每个候选路段当前 Street View 的拍摄日期、行驶方向、连接完整性和临时施工状态尚未逐点检查。
  - `nieyy.github.io` 用户站点仓库尚不存在；目标 URL 当前返回 404。

## 4. 调研内容

### 4.1 当前状态

| 区域 / 模块 | 当前行为 | 证据 |
|---|---|---|
| 个人复盘 | 用户估计失败约 7 次；文字复盘分散，容易被概括成过度简单的“死规则” | 个人历次考试复盘摘要；原始记录不随本文公开，准确次数待人工确认 |
| 近期典型问题 | 包括高速并入速度与空间判断、多车道转弯车道选择、跟随慢车时的决策 | 个人考试复盘提炼；逐项结论仍应以原始评分表和官方规则为准 |
| 其他典型问题 | 包括红灯右转的完整停车，以及黄灯时停车或继续通过的情境判断 | 个人考试复盘提炼；本文仅用于形成训练场景，不公开原始记录 |
| 官方 G Test 范围 | 目前仍测试主要道路/高速、汇入驶离、合理速度和空间、转弯、变道、路口和商业区；暂不包含平行停车、路边停车、三点掉头和住宅区驾驶 | [MTO Level Two Road Test](https://www.ontario.ca/document/official-mto-drivers-handbook/level-two-road-test)（更新于 2025-09-08） |
| Newmarket 考点 | DriveTest 地址为 320 Harry Walker Parkway S，并提供 G 测试 | [DriveTest Centre List](https://drivetest.ca/find-a-drivetest-centre/alphabetical_list/) |
| 候选道路范围 | 市政府明确称限制教学区域覆盖 Newmarket DriveTest Centre 的各种考试路线，边界涉及 Gorham、Prospect、Bayview、Traviss、Leslie Valley、Leslie；Davis Drive 是主要通行道路 | [Town of Newmarket Restricted Area](https://www.newmarket.ca/resident-services/by-law-enforcement/restricted-area-driving-instructors-driving-schools) |
| 候选高速链路 | 近期和历史用户报告多次出现 Harry Walker / Davis / Leslie、404 North、Green Lane、404 South，但也明确存在路线变化 | [2025 用户报告](https://www.reddit.com/r/Ontariodrivetest/comments/1mcz55n)、[2022 用户报告](https://www.reddit.com/r/Ontariodrivetest/comments/zzgwu1)；仅作线索，不作为官方路线 |
| 教学限制 | Newmarket 的指定区域禁止驾驶教练/驾校为教学或备考而运营；私人车辆和正式考试不在该项禁止范围内 | [Town of Newmarket Restricted Area](https://www.newmarket.ca/resident-services/by-law-enforcement/restricted-area-driving-instructors-driving-schools)；网页模拟不在道路上运营，但产品应醒目提示当地限制 |
| 发布目标 | `https://nieyy.github.io/` 当前返回 GitHub Pages 404，账号下没有可访问的 `nieyy.github.io` 仓库 | 2026-08-12 HTTP 检查及 GitHub CLI 只读仓库查询 |

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
  - Street View 是按全景节点跳转，不是连续视频，不能自然提供方向盘级驾驶物理；需要通过渐进移动、过渡动画和事件点降低跳跃感。
  - GitHub Pages 只能托管静态资源。排行榜、跨设备账号、服务端密钥和动态内容审核需要另加后端。
  - 浏览器中的地图 key 无法成为真正秘密；必须限制到 `https://nieyy.github.io/*`、只授权 Maps JavaScript API，并设置配额/预算告警。

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

#### Finding 2: “真实街景 + 脚本场景”是最短路径，但不是完整驾驶模拟

- 证据: Google `StreetViewPanorama` 可按位置/全景 ID 显示真实街景，提供导航 links、位置、视角和变化事件，适合做第一视角节点式路线；但它不提供连续车辆动力学、实时交通或可编辑道路对象。MapLibre/Three.js 能连续控制相机和 3D 对象，但真实车道、灯控、坡度、标牌和建筑细节需要自行制作。
- 为什么重要: 街景方案能最快验证“在真实考点看见路口后做判断”是否有价值；若一开始追求 GTA 式自由驾驶，会把主要成本花在画面和物理上，反而延迟个性化复盘。
- 置信度: High。

#### Finding 3: Newmarket 有可信的候选走廊，但没有可保证的固定考试路线

- 证据: Newmarket 市政府明确说明限制区域涵盖该中心的各种考试路线，证明 Harry Walker 周边确实存在多条路线；多份不同年份的用户报告反复出现 Davis、Leslie、Highway 404 和 Green Lane，但 2025 用户也明确表示实际路线与 YouTube 不同。
- 为什么重要: 产品命名应使用“Newmarket 风格模拟考试”或“基于社区报告的路线”，而不是“官方原题路线”。训练目标应覆盖道路类型和技能迁移，不能让用户误以为背路线即可通过。
- 置信度: High（存在候选走廊）；Low（任何一条具体路线会在下次考试出现）。

#### Finding 4: GitHub Pages 足够托管 MVP，但地图数据与密钥决定持续成本

- 证据: GitHub Pages 可通过自定义 Actions 工作流部署静态构建产物，站点软带宽上限为每月 100 GB、发布站点不超过 1 GB；Google Dynamic Street View 按成功加载全景对象计费；OSM 数据开放，但公共 `tile.openstreetmap.org` 不是无限免费 CDN，禁止预取/离线抓取并要求清晰署名。
- 为什么重要: 游戏逻辑和小型资源可以零服务器部署，但真实街景仍需要 Google Cloud billing、域名/API 限制和成本监控；不能把大量地图瓦片打包进仓库或滥用公共 OSM tile 服务。
- 置信度: High。

#### Finding 5: 首版应同时提供“考试模式”和“复盘模式”

- 证据: Ontario 官方说明考官会下指令但不允许在考试过程中 coaching；个人痛点则是事后文字总结遗漏具体上下文。
- 为什么重要: 真实感要求考试中保持沉默和压力，学习效果要求考试后能逐帧解释。两者混在一起会既不像考试，也难形成记忆。
- 置信度: High。

### 4.4 GAP 和风险

| GAP / 风险 | 影响 | 证据 | 严重程度 |
|---|---|---|---|
| 早期考试报告尚未完整整理 | 无法把约 7 次失败完整编码成个人错误谱系 | 当前仅汇总个人复盘要点，仍需逐份核对原始评分表 | High |
| 路线并非官方固定 | 若宣传“真实官方路线”，会误导用户并快速过时 | 市政府称存在 various routes；社区报告相互有差异 | High |
| Street View 非连续驾驶 | 方向盘/油门手感和精细车道保持训练有限 | API 提供全景节点和 POV，而非车辆物理世界 | High |
| 静态照片不能生成动态交通 | 照片里的车不能代表本次模拟的 gap、速度和意图 | 动态交通必须由自有 overlay/场景引擎建立 | High |
| Google 成本与条款 | 公开站点可能被刷量；缓存/抓取街景受限 | Dynamic Street View 按成功 panorama load 计费；政策限制缓存，要求条款、隐私和署名 | High |
| API key 暴露 | GitHub Pages 前端 key 可见，若不限制会产生费用 | Google 官方要求 website + API restrictions，并建议配额/监控 | High |
| Newmarket 教学限制区 | 产品文案若鼓励驾校在限制区带练，会造成合规问题 | Town of Newmarket Restricted Area By-law | Medium |
| 地图/路况变化 | 施工、限速、标线和 Street View 拍摄时间会造成训练偏差 | 地图和影像有日期，现实道路会变化 | Medium |
| 训练迁移不等于通过保证 | 游戏改善决策，不等于验证真实车控、观察动作幅度和压力表现 | 模拟器/视频研究支持训练价值，但真实道路仍有差异 | High |
| 晕动与无障碍 | 快速全景跳转和 3D 相机可能让部分用户不适 | 浏览器第一视角常见风险；需实测 | Medium |
| 当前发布地址不可用 | 不能直接把构建产物推到目标 URL | `nieyy.github.io` 当前 404，用户站点仓库不存在 | Medium |

## 5. 可选方向

| 方案 | 描述 | 适合场景 | 成本 / 风险 | 建议 |
|---|---|---|---|---|
| A. 真实街景脚本考试 | Google Street View 第一视角；玩家按节点前进，在关键帧操作车辆控件、观察和选道；自有 overlay 模拟信号与车辆 | 最快覆盖真实地点识别、路口预判、考官指令和个性化复盘 | 需要 billing/key；全景移动不连续；不能真正评估方向盘控制 | **MVP 主方案** |
| B. MapLibre + Three.js 3D 驾驶 | 用 OSM/自制路线几何，Three.js 渲染车辆、交通灯、车道和碰撞，支持连续油门/转向 | 需要连续操控、可重复动态交通、无街景依赖 | 制作真实道路细节成本高；车道拓扑和标线需人工；容易“能开但不像 Newmarket” | **第二阶段只做关键路段 PoC** |
| C. 自采第一视角/360° 分支视频 | 合法、安全地录制候选走廊，视频播放到决策点时分支或评分 | 画面连续、真实路况强、适合危险感知训练 | 采集与剪辑成本高；隐私/车牌/人脸处理；路线更新昂贵；录制时不能干扰驾驶 | **中期增强高价值场景** |
| D. 完整云端驾驶模拟器 | Unity/Unreal WebGL 或云串流，完整车辆物理、AI 交通和城市模型 | 商业级训练产品、多设备/方向盘支持 | 体积、性能、建模、托管和运维成本远超个人 MVP；GitHub Pages 不适合云串流 | **当前不建议** |

### 方案 A 的建议界面

```text
┌──────────────────────────────────────────────────────────┐
│                 真实街景 / 第一视角                      │
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

采用 **A 为主、B/C 可插拔** 的分阶段路线：

1. **内容底座**：先把官方规则、个人错误和 Newmarket 候选路段编码成结构化场景；没有内容底座，任何 3D 画面都会退化成无针对性的开车玩具。
2. **垂直切片**：用 Google Street View + TypeScript 场景状态机做 5–8 分钟第一视角考试，覆盖红灯右转、多车道左转和 404 并入。
3. **完整 MVP**：扩展到 15–20 分钟，加入考试模式、练习模式、考官英文语音/中文字幕、结束报告、弱项训练和本地历史。
4. **连续驾驶 PoC**：只为一个最值得连续操作的路段（建议 404 加速车道）试做 MapLibre/Three.js 版本，与街景节点版 A/B 测试；确认训练价值后再扩大。
5. **自采素材增强**：若街景跳跃严重影响体验，再为关键场景录制自有第一视角/360° 视频，不必重建整条路线。

**原因**:

- 直接针对“文字总结会漏”和“规则被记成绝对句”的根因：让用户在变化的情境中做决策，并得到逐事件证据。
- 保留真实地点的视觉记忆，同时把动态车流掌握在自己的场景引擎中。
- MVP 可以是纯静态前端，适配 GitHub Pages；不需要先建设账号、数据库和服务器。
- 先验证训练闭环，再决定是否值得投入完整 3D 路面与车辆物理。

**建议技术栈**:

| 层 | 建议 | 说明 |
|---|---|---|
| 构建 | Vite + TypeScript | 适合静态部署、资源路径和代码分包 |
| UI | React（或 Preact） | 场景 HUD、报告、设置；如追求更小体积可选 Preact |
| 状态 | 纯 TypeScript 有限状态机；复杂后再引入 XState | 保证每个指令、输入、扣分和回放可重现 |
| 真实画面 | Google Maps JavaScript API `StreetViewPanorama` | 第一视角真实道路、位置/POV/节点事件 |
| 小地图 | MapLibre GL JS + 合规 tile provider | 展示路线与进度；不直接依赖 OSM 公共 tile 做高流量生产服务 |
| 动态场景 | Canvas/WebGL overlay；第二阶段可接 Three.js | 交通灯、虚拟车辆、车道目标、镜子提示 |
| 音频 | 预录考官语音优先，Web Speech API 作为后备 | 预录能保证措辞、节奏和跨浏览器一致性 |
| 内容 | 版本化 JSON/GeoJSON | 路线、场景、rubric 与证据等级可独立审核 |
| 本地进度 | IndexedDB 或 localStorage | 首版不收集个人信息、不需要后端 |
| 测试 | Vitest + Playwright | 评分状态机单测、完整考试流程与响应式 E2E |
| 发布 | GitHub Actions → GitHub Pages | 新建 `nieyy/nieyy.github.io` 可占用根域；独立项目仓库则部署到子路径 |

**计分建议**:

- 五个维度：`Observation`、`Speed`、`Space`、`Signals`、`Lane/Right-of-way`。
- 错误分三级：普通错误、影响其他道路使用者的严重错误、考官干预/碰撞级危险行为。
- 分数不应只有总分；报告先显示前三个“下一次最值得改”的行为，再提供完整时间线，避免再次淹没在文字里。
- 每个结论包含四项：`时间/位置`、`当时情境`、`玩家动作`、`官方依据`。
- 考试结束后允许点击“只练这类 Bug”，自动生成相同规则、不同交通参数的短练习。

**应该进入设计文档的内容**:

- 产品信息架构和考试/练习/复盘三种模式的交互流程。
- `Route`、`Segment`、`Scenario`、`Prompt`、`ActionEvent`、`Rubric`、`Attempt` 的数据契约。
- Street View 节点导航、相机方向、镜子/肩检和动态车辆 overlay 的技术验证。
- 严重错误判定、复盘时间线和个性化弱项权重算法。
- Google API key 限制、配额、隐私政策、地图署名和不可缓存内容边界。
- GitHub Pages 用户站点/项目站点选择和 base path 处理。

**不应该进入设计文档的内容**:

- 把任何社区路线写成 DriveTest 官方保证路线。
- 未从原始评分表验证的七次考试逐次扣分结论。
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
  - Street View panorama 不可用、节点断裂、方向相反、影像过旧时，能够切换到备用节点或解释性场景。
  - 一次考试的所有输入事件能重放，并生成同样评分。
  - 中英文指令、字幕、语音和路线事件保持同步。
  - API key 仅允许目标域名和需要的 API；超额时显示可理解的降级页面。
- 端到端 / 运维:
  - Chrome、Safari、Firefox 桌面端完成 20 分钟流程；移动端完成触控流程。
  - 首屏和路线资源按需加载，发布物控制在 GitHub Pages 限额内。
  - 建立 Google Cloud 每日配额、预算告警和用量监控；公开发布前做滥用测试。
  - `https://nieyy.github.io/` 或项目子路径刷新任意前端路由不返回 404。
- 回归:
  - 路线数据更新不能改变既有 attempt 的评分；attempt 保存内容版本号。
  - 官方规则引用含 `checkedAt` 日期；定期检查失效链接和规则变化。
  - 地图 attribution、免责声明和隐私链接在桌面/手机上始终可见。
- 负向 / 失败场景:
  - 断网、Street View 无覆盖、API 配额耗尽、浏览器禁音、WebGL 不可用、存储空间不足。
  - 用户连续乱按肩检/信号、长时间不操作、逆向选择节点、错过出口或拒绝所有 gap。
  - 晕动模式：关闭全景过渡、降低视角变化、允许键盘逐帧推进。
  - 严重错误发生后提供“结束考试”与“标记失败但继续练完”两种行为，报告必须保留首次危险事件。

## 8. Open Questions

- [ ] 用户准确失败次数是多少？能否提供每次考试日期、考点和原始评分表，以建立可验证的错误时间线？
- [ ] 第一版是“只服务用户个人弱项”，还是一开始就面向所有 Ontario G 考生？这决定内容编辑器和隐私边界。
- [ ] 是否接受 MVP 是第一视角节点式互动，而非方向盘级连续驾驶？
- [ ] 是否愿意启用 Google Cloud billing，并为 Street View 设置小额预算/每日 quota？
- [ ] 发布选择根域 `nieyy.github.io`（需新建 `nieyy/nieyy.github.io`）还是项目子路径（例如 `nieyy.github.io/ontario-g-test/`）？
- [ ] 游戏项目是否单独建仓库，还是放入现有 `my-ai-brain`？建议单独仓库，记忆库只保留调研/设计/复盘。
- [ ] 是否需要收集匿名使用数据？MVP 建议不收集；若以后加入，需独立隐私设计和用户同意。
- [ ] 是否有条件安全、合法地录制 Newmarket 路段的第一视角/360° 素材？
- [ ] 哪三次失败最有代表性，除当前已知五类错误外还有哪些反复出现？
- [ ] 是否需要考官指令完全使用 Ontario 考试常见英文措辞，并由本地教练/近期考生审核？

## 9. 来源记录

| 来源 | 日期 / 版本 | 备注 |
|---|---|---|
| 个人历次 G 牌考试复盘 | 汇总于 2026-08-12 | 私有记录，不随本文公开；仅用于提炼需求和训练场景，事实以原始评分表及官方规则为准 |
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
| [Prabhakharan et al., Hazard perception training meta-analysis](https://pubmed.ncbi.nlm.nih.gov/38701558/) | 2024 | 57 篇研究；主动训练对驾驶员危险感知改善更稳定 |
| [Scialfa et al., A hazard perception test for novice drivers](https://doi.org/10.1016/j.aap.2010.08.010) | 2011 | 北美视频危险感知任务能区分新手与有经验驾驶员 |

## 10. 结论

- 是否进入设计: **Yes**
- 要创建的设计文档: `docs/designs/2026-08-xx-ontario-g-test-interactive-game-mvp-design-zh.md`
- 实现前还需要补充的调研:
  - 逐点检查候选路线的 Street View 覆盖、拍摄日期、节点方向和可用性。
  - 获取并结构化个人各次考试的原始评分表，形成“错误—官方规则—游戏场景”的可追溯矩阵。
  - 用 Google Cloud pricing calculator/实际控制台确认预算，并验证单个 `StreetViewPanorama` 实例沿路线切换节点时的实际计费行为。
  - 找 1 名 Ontario 合格驾驶教练或近期 Newmarket G 考生审核考官措辞、路线候选和评分 rubric。
  - 制作低保真交互原型，验证节点式第一视角是否足够有沉浸感；若不足，再决定 Three.js 连续驾驶或自采视频的投入。
