# n8n YouTube 视频下载自动化系统

基于 n8n 工作流自动化平台构建的 YouTube 视频下载系统，支持通过飞书（Lark）聊天机器人接收下载请求，使用 AI Agent 解析自然语言指令，并自动执行视频下载任务。

## 功能特性

- 🤖 **AI 驱动的自然语言处理**：集成 DeepSeek AI Agent，支持自然语言指令解析
- 📱 **飞书集成**：通过飞书聊天机器人接收和处理下载请求
- 🎬 **YouTube 视频下载**：基于 yt-dlp 实现高质量视频/音频下载
- 🐳 **Docker 容器化部署**：一键启动，易于维护和扩展
- 📦 **持久化存储**：视频文件和数据持久化保存

## 技术栈

- **n8n**: 2.2.5 - 工作流自动化平台
- **Redis**: 最新版 - 缓存服务
- **Python**: 3.13 - 代码执行环境
- **Node.js**: 22.21.1 - JavaScript 执行环境
- **yt-dlp**: YouTube 视频下载工具
- **Docker Compose**: 容器编排

## 项目结构

```
n8n/
├── docker-compose.yml          # Docker Compose 配置文件
├── start.cmd                   # 启动脚本（Windows）
├── stop.cmd                    # 停止脚本（Windows）
├── runners/                    # Task Runners 配置
│   ├── Dockerfile             # 自定义 Runner 镜像构建文件
│   └── n8n-task-runners.json  # Runner 配置文件
├── workflow/                   # n8n 工作流文件
│   ├── YouTube视频下载器-v1.json
│   ├── YouTube视频下载器-v2.json
│   └── YouTube视频下载的子流程-v2.json
├── cookies/                    # Cookie 文件存储目录
└── .n8n/                       # n8n 数据目录
```

## 快速开始

### 前置要求

- Docker Desktop（Windows）或 Docker + Docker Compose（Linux/Mac）
- 至少 4GB 可用内存
- 足够的磁盘空间用于存储下载的视频

### 安装步骤

1. **克隆项目**
   ```bash
   git clone <repository-url>
   cd n8n
   ```

2. **配置环境变量**
   
   编辑 `docker-compose.yml`，修改以下关键配置：
   - `GENERIC_TIMEZONE`: 根据需要调整时区

3. **准备 Cookie 文件（可选）**
   
   如果需要下载需要登录的视频，将 YouTube Cookie 文件放置在 `cookies/` 目录下。

4. **启动服务**

   ```cmd
   start.cmd
   ```
   
5. **访问 n8n 界面**
   
   打开浏览器访问：http://localhost:5678
   
   首次访问需要创建管理员账户。

6. **导入工作流**
   
   在 n8n 界面中：
   - 点击左侧菜单的 "Workflows"
   - 点击 "Import from File"
   - 选择 `workflow/` 目录下的 JSON 文件导入

7. **配置飞书机器人**
   
   - 在飞书开放平台创建机器人应用
   - 获取 App ID 和 App Secret
   - 在 n8n 中配置飞书凭证（Lark API credentials）
   - 激活工作流

## 使用说明

### 通过飞书机器人下载视频

1. 在飞书群组中添加已配置的机器人
2. 发送消息，例如：
   - "下载这个视频：https://www.youtube.com/watch?v=xxx"
   - "帮我保存这个链接的视频"
   - "看看这个视频的信息"
3. AI Agent 会自动解析指令并执行下载
4. 下载完成后会收到确认消息

### 工作流说明

- **YouTube视频下载器-v2**: 主工作流，包含完整的 AI Agent 和下载逻辑
- **YouTube视频下载的子流程-v2**: 子流程，处理具体的下载任务

## 数据存储

- **n8n 数据**: 存储在 `.n8n/` 目录（Docker volume）
- **Cookie 文件**: 存储在 `cookies/` 目录
- **下载的视频**: 存储在 `videos_data` volume 中，可以在 Docker Desktop 查看，或者 Windows 文件夹的 `\\wsl.localhost\docker-desktop\mnt\docker-desktop-disk\data\docker\volumes\n8n_videos_data\_data\` 路径下。

## 停止服务

```cmd
stop.cmd
```

完全清理（包括数据）:
```bash
docker compose down -v
```

## 许可证

查看 [LICENSE](LICENSE) 文件了解详情。

## 贡献

欢迎提交 Issue 和 Pull Request！

## 相关链接

- [n8n 官方文档](https://docs.n8n.io/)
- [yt-dlp 文档](https://github.com/yt-dlp/yt-dlp)
- [飞书开放平台](https://open.feishu.cn/)
