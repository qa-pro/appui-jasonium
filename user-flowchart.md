# AppUI 自动化测试平台 - 用户流程图

## 整体用户旅程

```mermaid
graph TD
    Start([用户访问平台]) --> Login{是否已登录?}
    Login -->|否| Auth[登录/注册页面]
    Login -->|是| Dashboard[主控制台]
    
    Auth --> LoginAction[输入用户名/密码]
    LoginAction --> LoginCheck{登录成功?}
    LoginCheck -->|否| Auth
    LoginCheck -->|是| Dashboard
    
    Dashboard --> DeviceMgmt[设备管理]
    Dashboard --> FlowEdit[流程编排]
    Dashboard --> AITest[AI 智能测试]
    Dashboard --> ScheduledTask[定时任务]
    Dashboard --> Report[测试报告]
    Dashboard --> Settings[系统设置]
    Dashboard --> UserCenter[用户中心]
```

## 1. 设备管理流程

```mermaid
graph TD
    DM[设备管理] --> Scan[扫描设备]
    DM --> DeviceList[设备列表]
    
    Scan --> ADB[ADB 扫描 Android]
    Scan --> HDC[HDC 扫描鸿蒙]
    Scan --> STF[STF 扫描远程]
    
    ADB --> Sync[同步到数据库]
    HDC --> Sync
    STF --> Sync
    Sync --> DeviceList
    
    DeviceList --> DeviceAction{设备操作}
    
    DeviceAction -->|租借| Lease[租借设备]
    DeviceAction -->|释放| Release[释放设备]
    DeviceAction -->|重命名| Rename[修改设备名称]
    DeviceAction -->|删除| Delete[删除设备]
    DeviceAction -->|实时画面| Screen[查看设备画面]
    DeviceAction -->|UI 元素| Elements[检查 UI 层级]
    DeviceAction -->|配对| Pair[设备配对]
    DeviceAction -->|WiFi 连接| WiFi[WiFi 连接]
    
    Lease --> Status[状态: ONLINE → IN_USE]
    Release --> Status[状态: IN_USE → ONLINE]
    Screen --> ScreenView[实时画面弹窗]
    Elements --> ElementInspector[元素检查器]
```

## 2. 流程编排流程

```mermaid
graph TD
    FE[流程编排] --> NewFlow[新建流程]
    FE --> FlowList[流程列表]
    
    NewFlow --> Editor[LogicFlow 编辑器]
    
    Editor --> AddStep[添加步骤]
    
    AddStep --> StepType{选择步骤类型}
    
    StepType -->|启动 APP| StartApp[配置包名]
    StepType -->|点击| Click[配置元素定位]
    StepType -->|输入| Input[配置输入内容]
    StepType -->|滑动| Swipe[配置滑动参数]
    StepType -->|等待| Wait[配置等待时间]
    StepType -->|等待元素| WaitElement[配置元素定位]
    StepType -->|断言| Assert[配置断言条件]
    StepType -->|终止 APP| TerminateApp[配置包名]
    
    StartApp --> ConnectStep[连接步骤]
    Click --> ConnectStep
    Input --> ConnectStep
    Swipe --> ConnectStep
    Wait --> ConnectStep
    WaitElement --> ConnectStep
    Assert --> ConnectStep
    TerminateApp --> ConnectStep
    
    ConnectStep --> SaveFlow[保存流程]
    SaveFlow --> FlowList
```

## 3. 流程执行流程

```mermaid
graph TD
    Exec[执行流程] --> SelectDevice[选择设备]
    SelectDevice --> DeviceCheck{设备状态?}
    
    DeviceCheck -->|在线| StartExec[开始执行]
    DeviceCheck -->|使用中| Error1[设备被占用]
    DeviceCheck -->|离线| Error2[设备离线]
    
    StartExec --> AsyncExec[异步执行]
    AsyncExec --> ConnectDevice[连接设备 - Appium]
    ConnectDevice --> FlowExecutor[FlowExecutor 执行]
    
    FlowExecutor --> StepLoop[遍历步骤]
    StepLoop --> GetExecutor[获取 StepExecutor]
    GetExecutor --> ExecStep[执行步骤]
    ExecStep --> Screenshot[自动截图]
    Screenshot --> WebSocket[WebSocket 推送进度]
    WebSocket --> NextStep{还有步骤?}
    
    NextStep -->|是| StepLoop
    NextStep -->|否| ExecDone[执行完成]
    
    ExecDone --> SaveRecord[保存执行记录]
    SaveRecord --> ViewReport[查看报告]
    
    ExecStep -->|异常| ErrorScreenshot[失败截图]
    ErrorScreenshot --> ErrorLog[记录错误日志]
    ErrorLog --> SaveRecord
```

## 4. AI 智能测试流程

```mermaid
graph TD
    AI[AI 智能测试] --> CreateTask[创建 AI 任务]
    AI --> TaskList[任务列表]
    
    CreateTask --> InputDesc[输入测试描述]
    InputDesc --> SelectEngine{选择引擎}
    
    SelectEngine -->|Mobile MCP| MCP[Mobile MCP 引擎]
    SelectEngine -->|OpenAutoGLM| OpenAutoGLM[OpenAutoGLM 引擎]
    
    MCP --> SelectModel[选择模型]
    OpenAutoGLM --> SelectModel
    
    SelectModel --> ExecAI[执行 AI 任务]
    
    ExecAI --> StartMCP[启动 MCP Server]
    StartMCP --> AILoop[AI 循环]
    
    AILoop --> ModelChat[调用大模型]
    ModelChat --> ParseAction[解析动作]
    ParseAction --> ExecAction[执行设备操作]
    ExecAction --> SaveStep[保存步骤]
    SaveStep --> WSPush[WebSocket 推送]
    WSPush --> CheckFinish{任务完成?}
    
    CheckFinish -->|否| AILoop
    CheckFinish -->|是| AIDone[AI 任务完成]
    
    AIDone --> SaveAIRecord[保存 AI 执行记录]
    SaveAIRecord --> ViewAIMonitor[查看 AI 监控]
    
    ExecAction -->|隐私屏| BlankDetect[隐私屏检测]
    BlankDetect --> ElementExplore[元素探索]
    ElementExplore --> AILoop
    
    ExecAction -->|失败| FailCheck{失败判定}
    FailCheck -->|连续≥3次| AIFail[AI 任务失败]
    FailCheck -->|thinking 含失败关键词| AIFail
    FailCheck -->|页面等待10s| AIFail
    FailCheck -->|其他| AILoop
```

## 5. 场景录制流程

```mermaid
graph TD
    Record[场景录制] --> SelectDevice[选择设备]
    SelectDevice --> StartRecord[开始录制]
    
    StartRecord --> DeviceScreen[实时设备画面]
    
    DeviceScreen --> UserAction{用户操作}
    
    UserAction -->|点击| TapAction[录制点击]
    UserAction -->|滑动| SwipeAction[录制滑动]
    UserAction -->|输入| InputAction[录制输入]
    UserAction -->|启动应用| LaunchAction[录制启动]
    
    TapAction --> UIHierarchy[解析 UI 层级]
    SwipeAction --> UIHierarchy
    InputAction --> UIHierarchy
    LaunchAction --> UIHierarchy
    
    UIHierarchy --> GenerateSelector[生成 Selector]
    GenerateSelector --> ActionList[添加到动作列表]
    
    ActionList --> EditAction{编辑动作?}
    EditAction -->|是| ModifyAction[修改参数]
    EditAction -->|否| ContinueRecord[继续录制]
    
    ModifyAction --> ActionList
    ContinueRecord --> DeviceScreen
    
    ActionList -->|结束录制| StopRecord[停止录制]
    StopRecord --> GenerateFlow[生成测试流程]
    GenerateFlow --> SaveFlow[保存流程]
```

## 6. 定时任务流程

```mermaid
graph TD
    ST[定时任务] --> TaskType{任务类型}
    
    TaskType -->|流程任务| FlowTask[流程定时任务]
    TaskType -->|AI 任务| AITask[AI 定时任务]
    TaskType -->|测试集| TestSuiteTask[测试集定时任务]
    
    FlowTask --> CreateFlowTask[创建流程定时任务]
    AITask --> CreateAITask[创建 AI 定时任务]
    TestSuiteTask --> CreateTestSuiteTask[创建测试集定时任务]
    
    CreateFlowTask --> ConfigCron[配置 Cron 表达式]
    CreateAITask --> ConfigCron
    CreateTestSuiteTask --> ConfigCron
    
    ConfigCron --> SelectFlow[选择流程/AI 任务/测试集]
    SelectFlow --> SelectDevice[选择设备]
    SelectDevice --> SaveTask[保存定时任务]
    
    SaveTask --> TaskList[任务列表]
    
    TaskList --> TaskAction{任务操作}
    
    TaskAction -->|启用/禁用| ToggleTask[Toggle 状态]
    TaskAction -->|立即执行| RunNow[立即执行]
    TaskAction -->|编辑| EditTask[修改配置]
    TaskAction -->|删除| DeleteTask[删除任务]
    
    ToggleTask --> TaskList
    RunNow --> ExecTask[执行任务]
    
    ExecTask --> CronTrigger{Cron 触发}
    CronTrigger --> AutoExec[自动执行]
    AutoExec --> ExecRecord[保存执行记录]
```

## 7. 测试集执行流程

```mermaid
graph TD
    TS[测试集管理] --> CreateSuite[创建测试集]
    TS --> SuiteList[测试集列表]
    
    CreateSuite --> SuiteName[设置名称]
    SuiteName --> AddItems[添加用例]
    
    AddItems --> ItemType{用例类型}
    ItemType -->|流程用例| FlowItem[选择流程]
    ItemType -->|AI 用例| AIItem[选择 AI 任务]
    
    FlowItem --> SetOrder[设置执行顺序]
    AIItem --> SetOrder
    
    SetOrder --> SaveSuite[保存测试集]
    SaveSuite --> SuiteList
    
    SuiteList --> ExecSuite[执行测试集]
    ExecSuite --> ExecMode{执行模式}
    
    ExecMode -->|串行| Sequential[顺序执行]
    ExecMode -->|并行| Parallel[并发执行]
    
    Sequential --> ExecItems[逐个执行用例]
    Parallel --> ExecItems
    
    ExecItems --> ItemResult{用例结果}
    ItemResult -->|成功| NextItem{还有用例?}
    ItemResult -->|失败| FailureStrategy{失败策略}
    
    FailureStrategy -->|STOP| StopExec[停止执行]
    FailureStrategy -->|CONTINUE| NextItem
    
    NextItem -->|是| ExecItems
    NextItem -->|否| SuiteDone[测试集完成]
    
    StopExec --> SuiteDone
    SuiteDone --> SaveRun[保存执行记录]
    SaveRun --> ViewRun[查看详情]
```

## 8. 测试报告流程

```mermaid
graph TD
    Report[测试报告] --> FilterReport[筛选报告]
    
    FilterReport --> FilterFlow[按流程筛选]
    FilterReport --> FilterDevice[按设备筛选]
    FilterReport --> FilterUser[按用户筛选]
    FilterReport --> FilterStatus[按状态筛选]
    FilterReport --> FilterTime[按时间筛选]
    
    FilterFlow --> ReportList[报告列表]
    FilterDevice --> ReportList
    FilterUser --> ReportList
    FilterStatus --> ReportList
    FilterTime --> ReportList
    
    ReportList --> ViewDetail[查看详情]
    
    ViewDetail --> ExecInfo[执行信息]
    ViewDetail --> StepLog[步骤日志]
    ViewDetail --> Screenshots[执行截图]
    ViewDetail --> Statistics[统计数据]
    
    Screenshots --> ViewScreenshot[查看大图]
    Statistics --> SuccessRate[成功率]
    Statistics --> Duration[执行时长]
    Statistics --> ErrorAnalysis[错误分析]
```

## 9. 系统设置流程

```mermaid
graph TD
    Settings[系统设置] --> AISettings[AI 模型设置]
    Settings --> DBSettings[数据库设置]
    
    AISettings --> SelectEngine{选择引擎}
    SelectEngine -->|Mobile MCP| MCPConfig[配置 MCP 参数]
    SelectEngine -->|OpenAutoGLM| AutoGLMConfig[配置 AutoGLM 参数]
    
    MCPConfig --> SetAPIKey[设置 API Key]
    MCPConfig --> SetModel[设置模型名称]
    MCPConfig --> SetProvider[设置 Provider]
    
    AutoGLMConfig --> SetServiceURL[设置服务地址]
    AutoGLMConfig --> SetModelBaseURL[设置模型地址]
    
    SetAPIKey --> SaveConfig[保存配置]
    SetModel --> SaveConfig
    SetProvider --> SaveConfig
    SetServiceURL --> SaveConfig
    SetModelBaseURL --> SaveConfig
    
    DBSettings --> GetDBConfig[获取当前配置]
    GetDBConfig --> ModifyDB[修改数据库配置]
    ModifyDB --> TestConn[测试连接]
    TestConn --> ConnResult{连接成功?}
    ConnResult -->|是| SaveDB[保存配置]
    ConnResult -->|否| ModifyDB
    SaveDB --> RestartHint[提示重启生效]
```

## 10. 用户中心流程

```mermaid
graph TD
    UC[用户中心] --> UserInfo[用户信息]
    
    UserInfo --> ViewProfile[查看个人信息]
    UserInfo --> EditProfile[修改个人信息]
    UserInfo --> ChangePassword[修改密码]
    UserInfo --> Logout[退出登录]
    
    EditProfile --> SaveProfile[保存修改]
    SaveProfile --> UserInfo
    
    ChangePassword --> InputOld[输入旧密码]
    InputOld --> InputNew[输入新密码]
    InputNew --> ConfirmNew[确认新密码]
    ConfirmNew --> UpdatePassword[更新密码]
    UpdatePassword --> UserInfo
    
    Logout --> ClearToken[清除 Token]
    ClearToken --> Login[返回登录页面]
```

## 11. 执行监控流程

```mermaid
graph TD
    Monitor[执行监控] --> ExecList[执行列表]
    
    ExecList --> ExecDetail[执行详情]
    
    ExecDetail --> Progress[实时进度]
    ExecDetail --> LogStream[日志流]
    ExecDetail --> DeviceScreen[设备画面]
    ExecDetail --> StepScreenshots[步骤截图]
    
    Progress --> ProgressBar[进度条显示]
    LogStream --> WebSocketLog[WebSocket 实时推送]
    DeviceScreen --> PollScreen[轮询 ADB 截图]
    StepScreenshots --> ScreenshotList[截图列表]
    
    ScreenshotList --> ViewBigImage[查看大图]
    
    ExecDetail --> ExecAction{执行操作}
    ExecAction -->|停止| StopExec[停止执行]
    ExecAction -->|重跑| RerunExec[重新执行]
    
    StopExec --> ExecStatus[状态: RUNNING → STOPPED]
    RerunExec --> NewExec[创建新执行]
```

## 完整用户旅程图

```mermaid
graph LR
    subgraph 登录阶段
        A[访问平台] --> B[登录/注册]
    end
    
    subgraph 设备准备
        B --> C[扫描设备]
        C --> D[租借设备]
    end
    
    subgraph 测试准备
        D --> E{测试方式}
        E -->|手动编排| F[流程编排]
        E -->|AI 驱动| G[创建 AI 任务]
        E -->|录制| H[场景录制]
        E -->|已有流程| I[选择流程]
    end
    
    subgraph 测试执行
        F --> J[执行流程]
        G --> K[执行 AI 任务]
        H --> L[生成流程]
        L --> J
        I --> J
    end
    
    subgraph 结果分析
        J --> M[查看报告]
        K --> N[查看 AI 监控]
        M --> O[分析结果]
        N --> O
    end
    
    subgraph 持续优化
        O --> P[优化流程]
        P --> Q[设置定时任务]
        Q --> R[自动化执行]
    end
```

## 状态流转图

```mermaid
stateDiagram-v2
    [*] --> 未登录
    未登录 --> 已登录: 登录成功
    已登录 --> 未登录: 退出登录
    
    state 设备状态 {
        [*] --> 离线
        离线 --> 在线: 设备连接
        在线 --> 使用中: 租借
        使用中 --> 在线: 释放
        在线 --> 离线: 设备断开
        使用中 --> 离线: 设备断开
    }
    
    state 执行状态 {
        [*] --> 待执行
        待执行 --> 运行中: 开始执行
        运行中 --> 成功: 执行完成
        运行中 --> 失败: 执行异常
        运行中 --> 已停止: 手动停止
        成功 --> [*]
        失败 --> [*]
        已停止 --> [*]
    }
    
    state AI任务状态 {
        [*] --> 待执行
        待执行 --> 运行中: 开始执行
        运行中 --> 成功: 任务完成
        运行中 --> 失败: 任务异常
        运行中 --> 已停止: 手动停止
        成功 --> [*]
        失败 --> [*]
        已停止 --> [*]
    }
```
