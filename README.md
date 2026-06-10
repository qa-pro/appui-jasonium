# AppUI Automated Testing Platform & Agent Skill · User Handbook

> This handbook is for testers using the platform day-to-day, covering everything from first login to completing a full test execution cycle.

---

## 1. Environment Requirements

| Role | Requirement | Details |
|------|-------------|---------|
| All users | Modern browser (Chrome / Edge / Firefox) | Web-based platform, no local installation needed |
| Flow test execution | Appium Server running | Run `appium server --port 4723 --address 127.0.0.1` on the deployment machine; flow tests rely on Appium to drive device operations |
| Local device connection | USB cable + USB debugging enabled | Android devices require Developer Options > USB Debugging |
| iOS device | macOS + Xcode Command Line Tools | WebDriverAgent signing must be completed |
| HarmonyOS device | Huawei HDC tool | Developer mode must be enabled on device |
| AI-powered testing | Valid Anthropic API Key or OpenAI Compatible API | Configure in the LLM Settings page |

---

## 2. Account & Login

1. Open the platform URL to reach the login page
2. **Register**: Click the "Register" tab, fill in username and password; you are logged in automatically after registration
3. **Log in**: Enter your registered username and password
4. **Change password**: Go to "User Center" after logging in
5. Login sessions last 24 hours by default; re-login is required after expiration

---

## 3. Page Navigation

After logging in, the left sidebar provides access to all features:

| Page | Path | Purpose |
|------|------|---------|
| Flow Editor | `/flow` | Visual drag-and-drop test flow creation and editing (default landing page) |
| Device Manager | `/device` | Scan, connect, manage, and monitor test devices |
| Scenario Recorder | `/recorder` | Record real-device operations and auto-generate test flows |
| Execution Monitor | `/execution` | View flow execution status, real-time logs, and screenshots |
| AI Tasks | `/autoglm` | Create and manage natural-language-driven AI test tasks |
| Scheduled Tasks | `/scheduled-tasks` | Configure automated execution of flow and AI tasks on a schedule |
| Test Reports | `/report` | View execution statistics and test reports |
| LLM Settings | `/settings/llm` | Configure AI engine parameters (API Key, model, etc.) |
| Database Settings | `/settings/database` | Switch database connections at runtime |
| User Center | `/user-center` | Change password and manage personal info |

---

## 4. Core Workflows

### 4.1 Connect a Device

1. Go to "Device Manager"
2. **Wired connection**: Connect the device via USB, click "Scan Devices"
3. **WiFi connection**: Click "WiFi Connect", enter the device IP and port (Android 11+ supports pairing code)
4. Once the device is online, click "Lease" to claim it; click "Release" when done
5. Supports device renaming, live screen preview, and UI element hierarchy inspection

### 4.2 Create a Flow Test Case

1. Go to "Flow Editor" (default landing page)
2. Drag step nodes from the left panel onto the canvas:
   - **Launch App** → specify app package name
   - **Tap / Input / Swipe** → specify element locator info
   - **Wait / Wait for Element** → set wait conditions
   - **Assert** → verify element existence or attribute values
   - **Terminate App** → close the app
3. Connect nodes to form a complete test flow
4. Click "Save" to name and store the flow

### 4.3 Record a Scenario

1. First connect and lease a device in "Device Manager"
2. Go to "Scenario Recorder", select the target device
3. Click "Start Recording" and perform operations on the device
4. The platform automatically captures taps, swipes, inputs, and app launches
5. Click "Stop Recording" when done; a reusable test flow is generated automatically
6. Fine-tune the recorded flow in the Flow Editor

### 4.4 Execute Tests

**Flow execution:**
1. **Ensure Appium Server is running** (run `appium server --port 4723 --address 127.0.0.1` on the deployment machine)
2. Ensure the target device is connected and leased
3. In "Flow Editor", select a flow and click "Execute"
4. Switch to "Execution Monitor" to view real-time progress, logs, and auto-captured screenshots
5. Supports stopping execution mid-way

**AI-powered testing:**
1. Go to "AI Tasks"
2. Describe your test intent in natural language (e.g., "Open Settings, check if Bluetooth is off by default")
3. Select the engine: Mobile MCP or OpenAutoGLM
4. Click Execute, then go to the AI execution monitor to watch step-by-step operations

### 4.5 Scheduled Tasks

1. Go to "Scheduled Tasks"
2. Click "New Task", choose type (Flow Task / AI Task)
3. Configure a Cron expression for execution time
4. Specify target device and flow/test case
5. Save; the task runs automatically on schedule
6. Supports immediate execution, enable/disable, and batch creation (AI tasks)

### 4.6 View Reports

1. Go to "Test Reports"
2. Filter by time range, device, flow, etc.
3. View pass rate statistics and execution trends
4. Click any execution to review per-step screenshots and detailed logs

---

## 5. AI Configuration

Before using AI-powered testing, configure the engine in "LLM Settings":

### Mobile MCP Engine (Recommended)

| Setting | Description |
|---------|-------------|
| Model Provider | Select `anthropic` or `openai_compatible` |
| Model Base URL | API endpoint (Anthropic official or compatible) |
| Model API Key | API key |
| Model Name | Model identifier, e.g. `claude-opus-4-5` |

### OpenAutoGLM Engine

| Setting | Description |
|---------|-------------|
| Service URL | AutoGLM service address, default `http://localhost:8400` |
| Model Base URL | Model inference service address |
| Model API Key | Model API key |
| Model Name | Model identifier, default `autoglm-phone-9b` |

> After configuration, click "Test Connection" to verify connectivity.

---

## 6. Important Notes

### Devices

- Ensure the device screen is unlocked and not in sleep mode before executing tests
- Devices left idle for a long time may enter sleep mode, causing execution timeouts
- For unstable WiFi connections, switch to wired (USB) connection
- A device can only be leased by one user at a time
- Other users cannot lease a device until it is released

### Flow Test Cases

- Place the "Launch App" step at the beginning of the flow
- Add "Wait for Element" steps after key operations to avoid failures due to pages not fully loading
- Use Assert steps to verify expected results; add them at critical checkpoints
- Flows support multiple rounds of editing and iteration

### AI Testing

- AI testing depends on LLM inference and typically takes longer than flow-based testing
- More specific and detailed test intent descriptions yield higher AI accuracy
- Start with simple scenarios to verify AI connectivity before running complex tests
- Real-time step-by-step operation logs are available during AI execution

### Scheduled Tasks

- Cron expression format: `second minute hour day month weekday` (e.g., `0 0 9 * * ?` for daily at 9 AM)
- Ensure the target device is online and idle when scheduled tasks trigger
- Execution failure details and error info are available in task history

### Network & Services

- Ensure your browser can access the platform URL
- The Execution Monitor uses WebSocket for real-time updates; refresh the page if connection drops
- If the page is unresponsive for an extended period, check that the backend service is running

---

## 7. Quick Troubleshooting

| Problem | Solution |
|---------|----------|
| Device not found | Check USB cable, ensure USB debugging is enabled, run `adb devices` to verify |
| Flow execution stuck | Check if device is locked or sleeping, review execution logs to locate the failing step |
| AI task unresponsive | Verify API Key in "LLM Settings", click "Test Connection" to check |
| Screenshots not loading | Check if MinIO service is running, verify bucket configuration |
| WebSocket disconnected | Refresh the page to reconnect, check network stability |
| Scheduled task not triggered | Confirm task status is "Enabled", verify Cron expression is correct |
| Blank page | Clear browser cache, confirm frontend (port 3000) and backend (port 8030) services are running |

---

## 8. AI-Driven Testing (appui-test Skill)

The platform supports driving tests entirely through the `appui-test` skill in Claude Code — no manual Web UI interaction needed. Just describe your intent and the skill handles everything from device leasing to result reporting.

### How to Invoke

Use the `/appui-test` command in Claude Code followed by your test description:

```
# Create a new AI test case (default mode)
/appui-test Open WeChat, search for contact "John", verify search results

# Re-run an existing test case (ai: or 用例: prefix)
/appui-test ai: WeChat contact search test
/appui-test 用例: #42

# Execute an existing flow (flow: or 流程: prefix)
/appui-test flow: Login regression test
/appui-test 流程: Payment flow

# Scheduled task management (定时: or schedule: prefix)
/appui-test 定时: List all scheduled tasks
```

### Workflow

The skill automatically completes three phases with no manual intervention:

```
┌─────────────────────────────────────────┐
│ PHASE 1: SETUP                          │
│   Login → Scan devices → Lease device   │
├─────────────────────────────────────────┤
│ PHASE 2: TEST                           │
│   Create task → Poll status → Get result│
├─────────────────────────────────────────┤
│ PHASE 3: TEARDOWN                       │
│   Release device → Summary report       │
└─────────────────────────────────────────┘
```

### AI Engine Selection

| Engine | Characteristics | Use Case |
|--------|----------------|----------|
| Mobile MCP (recommended) | Claude/OpenAI API, auto-executes after creation | Complex scenarios, intelligent decision-making |
| OpenAutoGLM | AutoGLM vision model, requires manual trigger | Lightweight validation, visual understanding |

Specify an engine:
```
/appui-test engine: autoglm, open settings and check Bluetooth status
```

### Batch & Parallel Execution

**Batch serial** — run multiple test cases sequentially on the same device:
```
/appui-test
1. Open WeChat, verify homepage loads
2. Open Alipay, verify homepage loads
3. Open Douyin, verify recommendation feed loads
```

**Multi-device parallel** — different devices execute different cases simultaneously:
```
/appui-test parallel execution:
device Pixel 7:
1. Open WeChat, verify homepage loads
2. Search for contact "John"
device Galaxy S24:
1. Open Alipay, verify homepage loads
2. Verify QR code scanning
```

### Credential Configuration

The skill needs platform login credentials, resolved in this priority order:

1. **Explicit pass-in**: `/appui-test account: admin/123456, open WeChat`
2. **Environment variables**: Set `APPUI_USERNAME` and `APPUI_PASSWORD`
3. **Interactive prompt**: If neither is available, the skill prompts for input

The platform URL is configured via the `APPUI_API_BASE` environment variable, defaulting to `http://localhost:8030`.

### Important Notes

- **Devices must be leased first**: The skill handles this automatically, but devices must be online and available
- **Flow cases must be created in the UI**: The skill cannot replace visual drag-and-drop editing; create flows in FlowEditor first, then execute via the skill
- **Re-running clears history**: `ai:` re-execution clears all previous steps and screenshots and starts from scratch
- **Execution timeout**: AI tasks running longer than 10 minutes will prompt for manual stop
- **Early assertion failure**: Automatically stops after 3 consecutive verification failures to avoid pointless waiting
- **Credential safety**: The skill never outputs tokens, passwords, or other credentials in conversation; personal info in screenshots is redacted

### Result Interpretation

| Status | Meaning |
|--------|---------|
| ✅ COMPLETED / SUCCESS | Test passed |
| ❌ FAILED | Test failed; check error message and screenshots for root cause |
| ⏹️ STOPPED | Manually terminated |
| ⚠️ COMPLETED but result empty or contains "failed" | Review step details for confirmation |

---

## 9. Browser Compatibility

The following browsers (latest version) are recommended:

- Google Chrome (recommended)
- Microsoft Edge
- Mozilla Firefox

> Internet Explorer is not supported.


----------------------------------------------------------------------------------------------------------------------


# AppUI 自动化测试平台 & Agent Skill · 使用手册

> 本文档面向日常使用平台的测试人员，覆盖从首次登录到完成一轮测试执行的完整流程。

---

## 一、环境要求

| 角色 | 必备条件 | 说明 |
|------|----------|------|
| 所有用户 | 现代浏览器（Chrome / Edge / Firefox） | 平台为 Web 应用，无需本地安装 |
| 流程用例执行 | Appium Server 已启动 | 部署机器上运行 `appium server --port 4723 --address 127.0.0.1`，流程用例依赖 Appium 驱动设备操作 |
| 本地设备连接 | USB 数据线 + 开启 USB 调试 | Android 设备需在开发者选项中开启 |
| iOS 设备 | macOS + Xcode Command Line Tools | 需完成 WebDriverAgent 签名 |
| HarmonyOS 设备 | 华为 HDC 工具 | 设备需开启开发者模式 |
| AI 智能测试 | 有效的 Anthropic API Key 或 OpenAI Compatible API | 在「大模型设置」页面配置 |

---

## 二、账号与登录

1. 打开平台地址，进入登录页面
2. **注册**：点击「注册」标签，填写用户名和密码完成注册，注册后自动登录
3. **登录**：输入已注册的用户名和密码
4. **修改密码**：登录后进入「个人中心」页面操作
5. 登录状态默认保持 24 小时，过期后需重新登录

---

## 三、页面导航

登录后，平台左侧为导航菜单，功能页面如下：

| 页面 | 路径 | 用途 |
|------|------|------|
| 流程用例 | `/flow` | 可视化拖拽创建和编辑测试流程（默认首页） |
| 设备管理 | `/device` | 扫描、连接、管理和监控测试设备 |
| 录制场景 | `/recorder` | 在真机上录制操作，自动生成测试流程 |
| 执行监控 | `/execution` | 查看流程执行状态、实时日志和截图 |
| AI 用例 | `/autoglm` | 创建和管理自然语言驱动的 AI 测试任务 |
| 定时任务 | `/scheduled-tasks` | 配置流程任务和 AI 任务的定时自动执行 |
| 测试报告 | `/report` | 查看执行统计和测试报告 |
| 大模型设置 | `/settings/llm` | 配置 AI 引擎参数（API Key、模型等） |
| 数据库设置 | `/settings/database` | 运行时切换数据库连接 |
| 个人中心 | `/user-center` | 修改密码等个人信息 |

---

## 四、核心操作流程

### 4.1 连接设备

1. 进入「设备管理」页面
2. **有线连接**：用 USB 线连接设备到电脑，点击「扫描设备」
3. **WiFi 连接**：点击「WiFi 连接」，输入设备 IP 和端口（Android 11+ 支持配对码配对）
4. 设备在线后点击「租借」获取使用权，使用完毕后「释放」归还
5. 支持设备重命名、实时画面预览和 UI 元素层级检查

### 4.2 创建流程用例

1. 进入「流程用例」页面（默认首页）
2. 从左侧面板拖拽步骤节点到画布：
   - **启动 APP** → 指定应用包名
   - **点击 / 输入 / 滑动** → 指定元素定位信息
   - **等待 / 等待元素** → 设置等待条件
   - **断言** → 验证元素存在或属性值
   - **终止 APP** → 结束应用
3. 连接节点形成完整测试流程
4. 点击「保存」命名并存储流程

### 4.3 录制场景

1. 先在「设备管理」中连接并租借一台设备
2. 进入「录制场景」页面，选择目标设备
3. 点击「开始录制」，在设备上进行操作
4. 平台自动捕获点击、滑动、输入、启动应用等操作
5. 录制完成后点击「停止录制」，自动生成可复用的测试流程
6. 可在流程编辑器中对录制结果进行微调

### 4.4 执行测试

**流程执行：**
1. **确保 Appium Server 已启动**（部署机器上执行 `appium server --port 4723 --address 127.0.0.1`）
2. 确保目标设备已连接并租借
3. 在「流程用例」页面选择流程，点击「执行」
4. 切换到「执行监控」页面查看实时进度、日志和自动截图
5. 支持中途停止执行

**AI 智能测试：**
1. 进入「AI 用例」页面
2. 用自然语言描述测试意图（如"打开设置，检查蓝牙是否默认关闭"）
3. 选择执行引擎：Mobile MCP 或 OpenAutoGLM
4. 点击执行，进入 AI 执行监控页面查看逐步操作过程

### 4.5 定时任务

1. 进入「定时任务」页面
2. 点击「新建任务」，选择类型（流程任务 / AI 任务）
3. 配置 Cron 表达式指定执行时间
4. 指定目标设备和流程/用例
5. 保存后任务自动按计划执行
6. 支持立即执行、启用/禁用、批量创建（AI 任务）

### 4.6 查看报告

1. 进入「测试报告」页面
2. 按时间范围、设备、流程等维度筛选
3. 查看通过率统计、执行趋势
4. 点击单次执行可回看每步截图和详细日志

---

## 五、AI 功能配置

使用 AI 智能测试前，需先在「大模型设置」页面完成配置：

### Mobile MCP 引擎（推荐）

| 配置项 | 说明 |
|--------|------|
| Model Provider | 选择 `anthropic` 或 `openai_compatible` |
| Model Base URL | API 地址（Anthropic 官方或兼容端点） |
| Model API Key | API 密钥 |
| Model Name | 模型名称，如 `claude-opus-4-5` |

### OpenAutoGLM 引擎

| 配置项 | 说明 |
|--------|------|
| Service URL | AutoGLM 服务地址，默认 `http://localhost:8400` |
| Model Base URL | 模型推理服务地址 |
| Model API Key | 模型 API 密钥 |
| Model Name | 模型名称，默认 `autoglm-phone-9b` |

> 配置完成后点击「测试连接」验证连通性。

---

## 六、使用注意事项

### 设备相关

- 执行测试前确认设备屏幕已解锁、未锁屏
- 长时间未操作的设备可能进入休眠，导致执行超时
- WiFi 连接的设备网络不稳定时建议改用有线连接
- 一台设备同时只能被一个用户租借
- 释放设备后其他用户才能租借使用

### 流程用例相关

- 「启动 APP」步骤应放在流程最前面
- 建议在关键操作后添加「等待元素」步骤，避免因页面未加载完成导致步骤失败
- 断言步骤用于验证预期结果，建议在关键节点添加
- 流程支持多次编辑和版本迭代

### AI 测试相关

- AI 测试依赖大模型推理能力，执行时间通常比流程测试长
- 测试意图描述越清晰具体，AI 执行准确率越高
- 建议先用简单场景验证 AI 连通性，再执行复杂场景
- AI 执行过程中可实时查看每步操作和决策日志

### 定时任务相关

- Cron 表达式格式：`秒 分 时 日 月 周`（如 `0 0 9 * * ?` 表示每天 9 点）
- 确保定时执行时目标设备处于在线且空闲状态
- 任务执行失败可在历史记录中查看详情和错误信息

### 网络与服务

- 确保浏览器能正常访问平台地址
- 执行监控页面通过 WebSocket 实时推送，网络断开后需刷新页面
- 如遇页面长时间无响应，检查后端服务是否正常运行

---

## 七、常见问题速查

| 问题 | 排查方法 |
|------|----------|
| 设备扫描不到 | 检查 USB 线连接、USB 调试是否开启，运行 `adb devices` 确认 |
| 流程执行卡住 | 检查设备是否锁屏或休眠，查看执行日志定位失败步骤 |
| AI 任务无响应 | 检查「大模型设置」中 API Key 是否有效，点击「测试连接」验证 |
| 截图不显示 | 检查 MinIO 服务是否正常运行，确认存储桶配置正确 |
| WebSocket 断连 | 刷新页面重新连接，检查网络稳定性 |
| 定时任务未触发 | 确认任务状态为「已启用」，检查 Cron 表达式是否正确 |
| 前端页面空白 | 清除浏览器缓存，确认前端服务（端口 3000）和后端服务（端口 8030）均正常 |

---

## 八、AI 驱动测试（appui-test 技能）

平台支持通过 `appui-test` 技能，用自然语言直接驱动平台执行测试——无需手动操作 Web 界面，只需描述意图即可完成从设备租借到结果报告的全流程。

### 触发方式

在 Claude Code 中使用 `/appui-test` 命令，后跟测试描述：

```
# AI 新建用例（默认模式）
/appui-test 打开微信，搜索联系人张三，验证搜索结果

# 重跑已有用例（ai: 或 用例: 前缀）
/appui-test ai: 微信搜索联系人测试
/appui-test 用例: #42

# 执行已有流程（flow: 或 流程: 前缀）
/appui-test flow: 登录流程回归测试
/appui-test 流程: 支付流程

# 定时任务管理（定时: 或 schedule: 前缀）
/appui-test 定时: 查看所有定时任务
```

### 工作流程

技能会自动完成三个阶段，无需手动干预：

```
┌─────────────────────────────────────────┐
│ PHASE 1: SETUP                          │
│   登录 → 扫描设备 → 租借设备              │
├─────────────────────────────────────────┤
│ PHASE 2: TEST                           │
│   创建任务 → 轮询状态 → 获取结果           │
├─────────────────────────────────────────┤
│ PHASE 3: TEARDOWN                       │
│   释放设备 → 汇总报告                     │
└─────────────────────────────────────────┘
```

### AI 引擎选择

| 引擎 | 特点 | 适用场景 |
|------|------|----------|
| Mobile MCP（推荐） | 基于 Claude/OpenAI API，创建后自动执行 | 复杂场景、需要智能决策 |
| OpenAutoGLM | 基于 AutoGLM 视觉模型，需手动触发执行 | 轻量验证、视觉理解场景 |

指定引擎：
```
/appui-test 引擎: autoglm, 打开设置检查蓝牙状态
```

### 批量与并行执行

**批量串行**——在同一设备上逐条执行多条用例：
```
/appui-test
1. 打开微信，验证首页加载
2. 打开支付宝，验证首页加载
3. 打开抖音，验证推荐页加载
```

**多设备并行**——不同设备同时执行不同用例：
```
/appui-test 并行执行：
设备 Pixel 7:
1. 打开微信，验证首页加载
2. 搜索联系人张三
设备 Galaxy S24:
1. 打开支付宝，验证首页加载
2. 扫码功能验证
```

### 凭据配置

技能需要平台登录凭据，按以下优先级获取：

1. **用户显式传入**：`/appui-test 账号: admin/123456, 打开微信`
2. **环境变量**：设置 `APPUI_USERNAME` 和 `APPUI_PASSWORD`
3. **交互输入**：以上均无时提示用户输入

平台地址通过 `APPUI_API_BASE` 环境变量配置，默认 `http://localhost:8030`。

### 使用注意事项

- **设备必须先租借**：技能会自动完成，但需确保设备在线可用
- **流程用例必须在前端创建**：技能无法替代可视化拖拽编排，需先在 FlowEditor 页面创建流程，再通过技能执行
- **重跑用例会清除历史**：`ai:` 重跑会清除上次执行的步骤和截图，从头开始
- **执行超时**：AI 任务超过 10 分钟未完成会建议手动停止
- **断言早期失败**：连续 3 步验证未满足预期时自动判定失败并停止，避免无效等待
- **凭据安全**：技能不会在对话中输出 token 明文、密码等凭据；截图中的个人信息会被脱敏

### 执行结果解读

| 状态 | 含义 |
|------|------|
| ✅ COMPLETED / SUCCESS | 测试通过 |
| ❌ FAILED | 测试失败，查看错误信息和截图定位原因 |
| ⏹️ STOPPED | 手动终止 |
| ⚠️ COMPLETED 但 result 为空或含"失败" | 需查看步骤详情确认 |

---

## 九、浏览器兼容性

推荐使用以下浏览器的最新版本：

- Google Chrome（推荐）
- Microsoft Edge
- Mozilla Firefox

> 不支持 IE 浏览器。
