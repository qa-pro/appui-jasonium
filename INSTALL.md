# AppUI Automated Testing Platform — Installation Guide

## Environment Requirements

| Dependency | Minimum Version | Purpose |
|------------|----------------|---------|
| Java | 17+ | Run `appui-platform-1.0.0.jar` |
| Node.js | 18+ | `npx vite preview` to serve the frontend |
| MySQL | 8.0+ | Primary database |
| MinIO | Latest stable | Screenshot / object storage |

## Directory Structure

```
.
├── appui-platform-1.0.0.jar   # Java backend service
├── autoglm-service             # AutoGLM AI service (macOS binary)
├── start-all.sh                # One-click startup script
├── config/
│   ├── env.properties.example  # Configuration template
│   └── app_packages.json       # Tested app package name mapping
└── dist/                       # Pre-built frontend static assets
```

## Step 1: Configure Environment Variables

```bash
cd config
cp env.properties.example env.properties
```

Edit `config/env.properties` and fill in the required fields:

### MySQL (required)

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/appui?useUnicode=true&characterEncoding=utf8&useSSL=false&serverTimezone=Asia/Shanghai&allowPublicKeyRetrieval=true
spring.datasource.username=<your_username>
spring.datasource.password=<your_password>
```

Create the database in advance:

```sql
CREATE DATABASE appui DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```

### Mobile MCP Engine (required, for AI-driven automated testing)

```properties
mobile-mcp.model-base-url=https://api.anthropic.com
mobile-mcp.model-api-key=<your_anthropic_api_key>
mobile-mcp.model-name=claude-opus-4-5
mobile-mcp.model-provider=anthropic/openai_compatible
```

### JWT (required)

```properties
# Generate with: openssl rand -base64 48
jwt.secret=<value_generated_by_the_command_above>
jwt.expiration=86400000
```

### AutoGLM Service (required, AI model invocation)

```properties
# Address for Java backend to call autoglm-service
autoglm.service-url=http://localhost:8400

# Model configuration read by the autoglm-service process
autoglm.model-base-url=http://model.autoglm.ai.srv/v1
autoglm.model-api-key=<your_api_key>
autoglm.model-name=autoglm-phone-9b
```

## Step 2: Configure Tested App Package Names (optional)

Edit `config/app_packages.json` to add or remove target applications:

```json
{
  "My Test App": "com.example.myapp",
  "Alipay": "com.eg.android.AlipayGphone",
  "WeChat": "com.tencent.mm.override"
}
```

## Step 3: One-Click Startup

macOS and Linux:
```bash
chmod +x start-all.sh
./start-all.sh
```

Windows:
```bash
Double-click start-all.bat
```

The script starts the following services in order:

| # | Service | Default Port |
|---|---------|-------------|
| 1/3 | Java backend (`appui-platform-1.0.0.jar`) | 8080 |
| 2/3 | Frontend (`npx vite preview`) | 4173 |
| 3/3 | AutoGLM service (`autoglm-service`) | 8400 |

After startup, open **http://localhost:4173** to access the platform.

Press `Ctrl+C` to stop all services.

## Important Notes

1. **Config file location**: `env.properties` must be placed in the `config/` directory (the `config/` subdirectory alongside the JAR). It takes precedence over the `application.yml` embedded in the JAR.
2. **autoglm-service platform limitation**: The current `autoglm-service` is a macOS (arm64) compiled binary. For Linux environments, replace it with the corresponding platform version.
3. **First startup**: The JAR automatically creates database tables — no manual DDL execution is required.
4. **Port conflicts**: If ports 8080/4173/8400 are already in use, free them or modify the corresponding service configuration.
5. **Pre-built frontend**: The `dist/` directory contains packaged static assets. No `npm install` is needed — it is served directly via `npx vite preview` (make sure `vite` is installed globally, or npx will download it automatically).
6. **Cross-machine deployment**: If the Java backend and AutoGLM service are deployed on different machines, change `autoglm.service-url` to the actual IP address of the AutoGLM service.
7. **API Key security**: `env.properties` contains sensitive credentials — never commit it to version control.


----------------------------------------------------------------------------------------------------------------------------------


# AppUI 自动化测试平台 — 安装指南

## 环境要求

| 依赖 | 最低版本 | 用途 |
|------|---------|------|
| Java | 17+ | 运行 `appui-platform-1.0.0.jar` |
| Node.js | 18+ | `npx vite preview` 启动前端 |
| MySQL | 8.0+ | 主数据库 |
| MinIO | 最新稳定版 | 截图/对象存储 |

## 目录结构

```
.
├── appui-platform-1.0.0.jar   # Java 后端服务
├── autoglm-service             # AutoGLM AI 服务（macOS 二进制）
├── start-all.sh                # 一键启动脚本
├── config/
│   ├── env.properties.example  # 配置模板
│   └── app_packages.json       # 被测 App 包名映射
└── dist/                       # 前端静态资源（已构建）
```

## 第一步：配置环境变量

```bash
cd config
cp env.properties.example env.properties
```

编辑 `config/env.properties`，填入以下必填项：

### MySQL（必填）

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/appui?useUnicode=true&characterEncoding=utf8&useSSL=false&serverTimezone=Asia/Shanghai&allowPublicKeyRetrieval=true
spring.datasource.username=<你的用户名>
spring.datasource.password=<你的密码>
```

需要提前创建数据库：

```sql
CREATE DATABASE appui DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```

### mobile-mcp 引擎（必填，用于 AI 驱动的自动化测试）

```properties
mobile-mcp.model-base-url=https://api.anthropic.com
mobile-mcp.model-api-key=<你的 Anthropic API Key>
mobile-mcp.model-name=claude-opus-4-5
mobile-mcp.model-provider=anthropic/openai_compatible
```

### JWT（必填）

```properties
# 生成方式：
jwt.secret=<运行 openssl rand -base64 48 得到的值>
jwt.expiration=86400000
```

### AutoGLM 服务（必填，AI 模型调用）

```properties
# Java 后端调 autoglm-service 的地址
autoglm.service-url=http://localhost:8400

# autoglm-service 进程读取的模型配置
autoglm.model-base-url=http://model.autoglm.ai.srv/v1
autoglm.model-api-key=<你的 API Key>
autoglm.model-name=autoglm-phone-9b
```

## 第二步：配置被测 App 包名（可选）

编辑 `config/app_packages.json`，按需增减被测应用：

```json
{
  "我的测试App": "com.example.myapp",
  "支付宝": "com.eg.android.AlipayGphone",
  "微信": "com.tencent.mm.override"
}
```

## 第三步：一键启动

MacOS和Linux
```bash
chmod +x start-all.sh
./start-all.sh
```
Windows
```bash
双击start-all.bat
```

脚本会依次启动：

| 序号 | 服务 | 默认端口 |
|------|------|---------|
| 1/3 | Java 后端 (`appui-platform-1.0.0.jar`) | 8080 |
| 2/3 | 前端 (`npx vite preview`) | 4173 |
| 3/3 | AutoGLM 服务 (`autoglm-service`) | 8400 |

启动后访问 **http://localhost:4173** 进入平台。

按 `Ctrl+C` 停止所有服务。

## 注意事项

1. **配置文件位置**：`env.properties` 必须放在 `config/` 目录下（与 JAR 同级的 `config/` 子目录），优先级高于 JAR 内的 `application.yml`。
2. **autoglm-service 平台限制**：当前 `autoglm-service` 是 macOS (arm64) 编译的二进制文件，Linux 环境需替换为对应平台的版本。
3. **首次启动**：JAR 会自动建表，无需手动执行 DDL。
4. **端口冲突**：若 8080/4173/8400 已被占用，需先释放或修改对应服务配置。
5. **前端已构建**：`dist/` 目录为已打包的静态资源，无需 `npm install`，直接通过 `npx vite preview` 提供服务（需确保全局安装了 `vite`，否则 npx 会自动下载）。
6. **分机部署**：如果 Java 后端和 AutoGLM 服务部署在不同机器，修改 `autoglm.service-url` 为 AutoGLM 服务的实际 IP 地址。
7. **API Key 安全**：`env.properties` 包含敏感凭据，切勿提交到版本控制。
