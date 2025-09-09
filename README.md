# OrchestrationApi

一个基于 .NET 9 的企业级多服务商 AI API 代理服务，提供统一的 OpenAI 兼容接口，支持智能路由、故障转移、负载均衡等企业级功能。

## 🚀 核心特性（Claude帮我吹的，但意思差不多）

### 多服务商支持
- **OpenAI**: 支持 GPT-3.5、GPT-4、GPT-4 Turbo 等全系列模型
- **Anthropic Claude**: 支持 Claude 3 Haiku、Claude 3 Sonnet、Claude 3 Opus、Claude 3.5 Sonnet 等
- **Google Gemini**: 支持 Gemini Pro、Gemini Flash、Gemini Vision 等模型
- **透明代理模式**: 支持各服务商的原生 API 格式（OpenAI、Anthropic、Gemini）
- **可扩展架构**: 通过接口驱动设计，轻松添加更多 AI 服务商

### 智能路由与负载均衡
- **智能路由**: 基于模型、代理密钥权限、分组优先级自动选择最佳提供商
- **多重故障转移**: 支持密钥级别和分组级别的智能故障转移
- **负载均衡策略**: 支持轮询(round_robin)负载均衡策略
- **实时健康检查**: 自动监控 API 密钥状态，智能剔除无效密钥
- **分组权重配置**: 支持基于权重的分组选择策略

### 完全兼容多种 API 格式
- **OpenAI 兼容**: 完全兼容 OpenAI API v1 格式，直接替换 base_url 即可使用
- **Anthropic 原生**: 支持 Anthropic Claude 原生 API 格式（/v1/messages）
- **Gemini 原生**: 支持 Google Gemini 原生 API 格式（generateContent）
- **流式响应**: 完整的 Server-Sent Events 支持，支持所有服务商的流式输出
- **函数调用**: 完整的 Function Calling 和 Tools 支持
- **参数透传**: 支持所有原生参数，包括 temperature、top_p、max_tokens 等

### 企业级管理功能
- **现代化 Web 管理界面**: 响应式设计的管理仪表板
- **分组管理**: 灵活的服务商分组配置和管理
- **代理密钥系统**: 多级代理密钥管理和权限控制
- **实时日志分析**: 详细的请求统计、性能分析和错误跟踪
- **系统监控**: 实时系统状态、性能指标、资源使用情况监控
- **密钥健康检查**: 自动检测和管理无效的 API 密钥

### 高性能与可扩展性
- **全异步架构**: 基于 .NET 9 异步模式，支持高并发请求
- **HTTP 连接池**: 优化的 HTTP 客户端连接池管理
- **内存缓存**: 智能缓存机制提升响应性能
- **Docker 原生支持**: 完整的容器化部署方案
- **数据库支持**: 支持 SQLite（开发）和 MySQL（生产）
- **配置热重载**: 支持动态配置更新

## 📋 系统要求

- .NET 9 SDK 或 Docker
- SQLite（默认）或 MySQL 数据库
- 支持的操作系统：Windows、Linux、macOS

## 🚀 快速开始

### 使用 Docker Compose（推荐）

1. **准备部署文件**
```bash
# 克隆或下载项目
git clone <repository-url>
cd OrchestrationApi

# 创建数据目录
mkdir -p data logs
```

2. **启动服务**
```bash
# 使用 Docker Compose 启动服务
docker-compose up -d

# 查看服务状态
docker-compose logs -f orchestration-api
```

3. **访问服务**
- 初始用户名密码：admin/admin123
- 管理界面: http://localhost:5000/dashboard
- 登录页面: http://localhost:5000/login
- 日志查看: http://localhost:5000/logs
- API 文档: http://localhost:5000/swagger（开发环境）
- 健康检查: http://localhost:5000/health

### 本地开发

1. **克隆项目**
```bash
git clone <repository-url>
cd OrchestrationApi
```

2. **安装依赖**
```bash
dotnet restore
```

3. **配置数据库**
```bash
# SQLite（默认，无需额外配置）
# 或编辑 appsettings.json 配置 MySQL
```

4. **运行服务**
```bash
# 开发模式
dotnet run

# 或使用热重载
dotnet watch run
```

## 🔧 配置指南

### 基础配置 (appsettings.json)

```json
{
  "OrchestrationApi": {
    "Server": {
      "Host": "0.0.0.0",
      "Port": 5000,
      "Mode": "release"
    },
    "Auth": {
      "Username": "admin",
      "Password": "admin123",
      "SessionTimeout": 86400,
      "JwtSecret": "OrchestrationApi-SecretKey-2025-ChangeMeInProduction"
    },
    "Database": {
      "Type": "sqlite",
      "ConnectionString": "Data Source=Data/orchestration_api.db",
      "MySqlConnectionString": "Server=localhost;Database=orchestration_api;Uid=root;Pwd=password;",
      "TablePrefix": "orch_"
    },
    "Global": {
      "Timeout": 60,
      "ConnectionTimeout": 30,
      "ResponseTimeout": 300,
      "Retries": 3,
      "BalancePolicy": "round_robin",
      "MaxProviderRetries": 3
    },
    "RequestLogging": {
      "Enabled": true,
      "EnableDetailedContent": true,
      "MaxContentLength": 10000,
      "ExcludeHealthChecks": true,
      "RetentionDays": 30
    },
    "KeyHealthCheck": {
      "IntervalMinutes": 5,
      "Enabled": true
    }
  }
}
```

### 高级配置选项

#### Gemini 专用配置
```json
{
  "Gemini": {
    "StreamingTimeout": 300,
    "NonStreamingTimeout": 180,
    "ConnectionTimeout": 30,
    "QualityCheck": {
      "Enabled": true,
      "BufferSize": 10,
      "DetectEmptyResponse": true,
      "DetectTruncation": true
    },
    "AutoRetry": {
      "Enabled": true,
      "MaxRetries": 2,
      "RetryOnEmpty": true,
      "RetryOnTruncation": true
    }
  }
}
```

#### 生产环境 MySQL 配置
```json
{
  "OrchestrationApi": {
    "Database": {
      "Type": "mysql",
      "MySqlConnectionString": "Server=localhost;Database=orchestration_api;Uid=orchestration;Pwd=secure_password;CharSet=utf8mb4;"
    }
  }
}
```

#### 日志配置 (Serilog)
```json
{
  "Serilog": {
    "MinimumLevel": {
      "Default": "Information",
      "Override": {
        "Microsoft": "Warning",
        "System": "Warning"
      }
    },
    "WriteTo": [
      {
        "Name": "Console"
      },
      {
        "Name": "File",
        "Args": {
          "path": "logs/orchestration-api-.log",
          "rollingInterval": "Day",
          "retainedFileCountLimit": 31
        }
      }
    ]
  }
}
```


## 📖 API 使用指南

### OpenAI 兼容 API

#### 聊天完成接口

与 OpenAI API 完全兼容，支持所有参数：

```bash
curl -X POST http://localhost:5000/v1/chat/completions \
  -H "Authorization: Bearer your-proxy-key" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "gpt-3.5-turbo",
    "messages": [
      {"role": "user", "content": "Hello, world!"}
    ],
    "temperature": 0.7,
    "max_tokens": 1000,
    "stream": false
  }'
```

#### 流式响应

```bash
curl -X POST http://localhost:5000/v1/chat/completions \
  -H "Authorization: Bearer your-proxy-key" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "gpt-4",
    "messages": [
      {"role": "user", "content": "Tell me a story"}
    ],
    "stream": true
  }'
```

#### 函数调用（Function Calling）

```bash
curl -X POST http://localhost:5000/v1/chat/completions \
  -H "Authorization: Bearer your-proxy-key" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "gpt-4",
    "messages": [
      {"role": "user", "content": "What is the weather like in Beijing?"}
    ],
    "tools": [
      {
        "type": "function",
        "function": {
          "name": "get_weather",
          "description": "Get weather information for a city",
          "parameters": {
            "type": "object",
            "properties": {
              "city": {"type": "string", "description": "City name"}
            },
            "required": ["city"]
          }
        }
      }
    ],
    "tool_choice": "auto"
  }'
```

### Anthropic Claude 原生 API

```bash
curl -X POST http://localhost:5000/v1/messages \
  -H "Authorization: Bearer your-proxy-key" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "claude-3-sonnet-20240229",
    "max_tokens": 1000,
    "messages": [
      {"role": "user", "content": "Hello, Claude!"}
    ]
  }'
```

### Google Gemini 原生 API

```bash
curl -X POST "http://localhost:5000/v1beta/models/gemini-pro:generateContent" \
  -H "Authorization: Bearer your-proxy-key" \
  -H "Content-Type: application/json" \
  -d '{
    "contents": [
      {
        "parts": [
          {"text": "Hello, Gemini!"}
        ]
      }
    ]
  }'
```

### 模型列表

```bash
# OpenAI 格式
curl -X GET http://localhost:5000/v1/models \
  -H "Authorization: Bearer your-proxy-key"

# Gemini 格式
curl -X GET http://localhost:5000/v1beta/models \
  -H "Authorization: Bearer your-proxy-key"
```

## 🐳 Docker 部署

### 基础部署（SQLite）

```yaml
version: '3.8'
services:
  orchestration-api:
    image: orchestration-api:latest
    container_name: orchestration-api
    ports:
      - "5000:5000"
    environment:
      - ASPNETCORE_ENVIRONMENT=Production
      - OrchestrationApi__Database__Type=sqlite
      - OrchestrationApi__Database__ConnectionString=Data Source=/app/data/orchestration_api.db
      - OrchestrationApi__Auth__Password=your-secure-password
      - OrchestrationApi__Auth__JwtSecret=your-jwt-secret-change-in-production
    volumes:
      - ./data:/app/data
      - ./logs:/app/logs
    restart: unless-stopped
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:5000/health"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 40s
```

### 生产环境（MySQL）

```yaml
version: '3.8'
services:
  orchestration-api:
    image: orchestration-api:latest
    container_name: orchestration-api
    ports:
      - "5000:5000"
    environment:
      - ASPNETCORE_ENVIRONMENT=Production
      - OrchestrationApi__Database__Type=mysql
      - OrchestrationApi__Database__MySqlConnectionString=Server=mysql;Database=orchestration_api;Uid=orchestration;Pwd=secure_password;CharSet=utf8mb4;
      - OrchestrationApi__Auth__Password=your-secure-password
      - OrchestrationApi__Auth__JwtSecret=your-jwt-secret-change-in-production
    depends_on:
      mysql:
        condition: service_healthy
    restart: unless-stopped
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:5000/health"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 40s

  mysql:
    image: mysql:8.0
    container_name: orchestration-mysql
    environment:
      MYSQL_ROOT_PASSWORD: root_password
      MYSQL_DATABASE: orchestration_api
      MYSQL_USER: orchestration
      MYSQL_PASSWORD: secure_password
      MYSQL_CHARSET: utf8mb4
      MYSQL_COLLATION: utf8mb4_unicode_ci
    ports:
      - "3306:3306"
    volumes:
      - mysql_data:/var/lib/mysql
      - ./mysql-init:/docker-entrypoint-initdb.d
    restart: unless-stopped
    healthcheck:
      test: ["CMD", "mysqladmin", "ping", "-h", "localhost", "-u", "orchestration", "-psecure_password"]
      interval: 30s
      timeout: 10s
      retries: 5
      start_period: 30s

volumes:
  mysql_data:
    driver: local
```

## 🚨 故障排除

### 常见问题和解决方案

#### 1. 服务启动问题

**问题**: 服务无法启动
```bash
# 检查服务状态
docker-compose ps
docker logs orchestration-api

# 常见原因和解决方案
# - 端口被占用: 修改docker-compose.yml中的端口映射
# - 配置文件错误: 检查appsettings.json语法
# - 数据库连接失败: 验证数据库配置和网络连接
```

#### 2. 数据库连接问题

**问题**: 数据库连接失败
```bash
# 检查数据库服务状态
docker ps | grep mysql
docker logs orchestration-mysql

# 验证连接字符串
# SQLite: 检查数据目录权限
# MySQL: 验证用户名、密码、网络连接

# 测试数据库连接
curl http://localhost:5000/health/detailed
```

## 🤝 贡献指南

我们欢迎社区贡献！请遵循以下步骤：

1. **Fork 项目**: Fork 本仓库到您的 GitHub 账户
2. **创建分支**: `git checkout -b feature/amazing-feature`
3. **开发功能**: 遵循项目的编码规范和架构设计
4. **编写测试**: 为新功能添加适当的单元测试
5. **提交更改**: `git commit -m 'Add amazing feature'`
6. **推送分支**: `git push origin feature/amazing-feature`
7. **创建 PR**: 开启 Pull Request 并详细描述变更内容

### 开发规范

- **代码风格**: 遵循 C# 编码规范，使用 PascalCase 命名类和方法
- **架构原则**: 保持控制器轻量，业务逻辑放在 Services 层
- **错误处理**: 使用结构化异常处理，记录详细的错误日志
- **文档更新**: 及时更新 README 和 API 文档
- **依赖管理**: 谨慎添加新的 NuGet 包依赖

### 项目架构

```
OrchestrationApi/
├── Controllers/        # API 控制器层
├── Services/           # 业务逻辑层
│   ├── Core/          # 核心服务接口和实现
│   ├── Providers/     # AI 服务商实现
│   └── Background/    # 后台服务
├── Models/            # 数据模型和 DTO
├── Configuration/     # 配置相关类
├── Middleware/        # 自定义中间件
└── wwwroot/          # 静态文件（管理界面）
```

## 📄 许可证

本项目采用 MIT 许可证 - 详见 [LICENSE](LICENSE) 文件。

MIT 许可证允许您自由使用、修改和分发本软件，包括商业用途。

## 📞 支持与反馈

### 获取帮助
- **项目文档**: 查看完整的 README 和代码注释
- **问题排查**: 参考故障排除章节
- **配置指南**: 查看详细的配置说明

### 反馈渠道
- **Bug 报告**: 通过 GitHub Issues 提交问题报告
- **功能建议**: 通过 Issues 提出新功能需求
- **使用问题**: 在 Issues 中提问使用相关问题
- **安全问题**: 通过私有方式报告安全漏洞

### 社区参与
- **代码贡献**: 欢迎提交 Pull Request
- **文档改进**: 帮助完善项目文档
- **测试反馈**: 在不同环境下测试并提供反馈
- **经验分享**: 分享使用经验和最佳实践

## 🗓️ 版本历史

### v1.0.0 - 初始版本
- ✅ .NET 9 框架
- ✅ 全新的 Web 管理界面
- ✅ 支持 OpenAI 兼容和支持 Gemini 原生API
- ✅ 完善的 Docker 容器化部署
- ✅ 增强的监控和日志功能
- ✅ 智能密钥健康检查
- ✅ 多重故障转移机制

### 技术栈
- [.NET 9](https://dotnet.microsoft.com/) - 现代化的跨平台开发框架
- [SqlSugar](https://www.donet5.com/Home/Doc) - 高性能 ORM 框架
- [Serilog](https://serilog.net/) - 结构化日志记录库
- [Newtonsoft.Json](https://www.newtonsoft.com/json) - JSON 序列化库
