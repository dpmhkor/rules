# Open WebUI 安装指南

Open WebUI 是一个为大型语言模型（LLMs）设计的友好型 Web 用户界面，支持多种功能，如直观的聊天界面、代码语法高亮、Markdown 和 LaTeX 支持等。

## 安装方法

### 方法 1：使用 Docker 安装（推荐）

Docker 安装是最简单且推荐的方式，可以确保环境一致性。

#### Windows 系统

**前置条件：**
- 安装 [Docker Desktop for Windows](https://www.docker.com/products/docker-desktop/)

**安装步骤：**

1. **拉取 Open WebUI 镜像**
   ```bash
   docker pull ghcr.io/open-webui/open-webui:main
   ```

2. **运行容器**
   ```bash
   docker run -d -p 3000:8080 -v open-webui:/app/backend/data --name open-webui ghcr.io/open-webui/open-webui:main
   ```
   
   参数说明：
   - `-d`：以后台模式运行容器
   - `-p 3000:8080`：将容器的 8080 端口映射到主机的 3000 端口
   - `-v open-webui:/app/backend/data`：创建数据卷，持久化存储数据
   - `--name open-webui`：为容器指定名称

3. **访问 Open WebUI**
   - 打开浏览器访问：`http://localhost:3000`
   - 首次访问需要注册管理员账户

**常用 Docker 命令：**
```bash
# 停止容器
docker stop open-webui

# 启动容器
docker start open-webui

# 查看容器状态
docker ps -a

# 查看日志
docker logs open-webui

# 删除容器（注意：不会删除数据卷）
docker rm open-webui

# 删除数据卷（谨慎操作）
docker volume rm open-webui
```

---

### 方法 2：使用 Python + uv 安装

适用于想要使用 Python 环境管理的用户。

**前置条件：**
- Python 3.11 或更高版本
- 安装 `uv` 运行时管理器

#### Windows 系统安装步骤

1. **安装 uv**
   
   在 PowerShell 中运行：
   ```powershell
   powershell -ExecutionPolicy ByPass -c "irm https://astral.ac.cn/uv/install.ps1 | iex"
   ```
   
   或者使用 pip 安装：
   ```bash
   pip install uv
   ```

2. **设置数据目录并启动**
   
   在 PowerShell 中运行：
   ```powershell
   $env:DATA_DIR="C:\DPAIS\OpenWebUI\data"
   uvx --python 3.11 open-webui@latest serve
   ```
   
   在 Git Bash 或 CMD 中：
   ```bash
   set DATA_DIR=C:\DPAIS\OpenWebUI\data
   uvx --python 3.11 open-webui@latest serve
   ```

3. **访问 Open WebUI**
   - 打开浏览器访问：`http://localhost:8080`

---

### 方法 3：使用 Conda 安装

适用于使用 Conda 进行环境管理的用户。

**安装步骤：**

1. **创建 Conda 环境**
   ```bash
   conda create -n open-webui python=3.11
   ```

2. **激活环境**
   ```bash
   conda activate open-webui
   ```

3. **安装 Open WebUI**
   ```bash
   pip install open-webui
   ```

4. **启动服务器**
   ```bash
   open-webui serve
   ```

5. **访问 Open WebUI**
   - 打开浏览器访问：`http://localhost:8080`

---

### 方法 4：使用 pip 直接安装

**安装步骤：**

1. **确保 Python 版本**
   - Python 3.11 或更高版本

2. **安装 Open WebUI**
   ```bash
   pip install open-webui
   ```

3. **启动服务器**
   ```bash
   open-webui serve
   ```

4. **访问 Open WebUI**
   - 打开浏览器访问：`http://localhost:8080`

---

## 配置说明

### 环境变量

可以通过环境变量配置 Open WebUI：

- `DATA_DIR`：数据存储目录
- `PORT`：服务端口（默认 8080）
- `WEBUI_SECRET_KEY`：Web UI 密钥
- `OPENAI_API_KEY`：OpenAI API 密钥（如需要）

### Docker 环境变量示例

```bash
docker run -d \
  -p 3000:8080 \
  -v open-webui:/app/backend/data \
  -e WEBUI_SECRET_KEY=your-secret-key \
  -e OPENAI_API_KEY=your-api-key \
  --name open-webui \
  ghcr.io/open-webui/open-webui:main
```

---

## 首次使用

1. **注册管理员账户**
   - 首次访问时，系统会提示创建管理员账户
   - 填写用户名、邮箱和密码完成注册

2. **配置模型**
   - 进入设置页面
   - 配置您要使用的 LLM 模型（如 OpenAI、Anthropic、本地模型等）
   - 添加相应的 API 密钥

3. **开始使用**
   - 创建聊天会话
   - 选择模型开始对话

---

## 常见问题

### 1. Ollama 模型在 Open WebUI 中不显示

如果 Ollama 中有模型，但在 Open WebUI 中看不到，这是因为 Docker 容器无法直接访问宿主机的 `localhost`。

**解决方法 A：在 Web 界面配置（推荐）**

1. 访问 Open WebUI：`http://localhost:3000`
2. 点击右下角用户头像 → 选择 **Settings**（设置）
3. 找到 **Connections** 或 **Model Settings**
4. 找到 **OLLAMA_BASE_URL** 字段
5. 设置为：
   - **Windows/Mac：** `http://host.docker.internal:11434`
   - **Linux：** `http://172.17.0.1:11434`
6. 点击 **Test Connection**（测试连接）或保存
7. 刷新页面，模型应该会显示

**解决方法 B：重新运行容器时配置**

如果方法 A 无效，停止当前容器并重新运行：

```bash
# 停止并删除现有容器（数据卷会保留）
docker stop open-webui
docker rm open-webui

# 重新运行容器，添加 Ollama 连接配置
docker run -d \
  -p 3000:8080 \
  -v open-webui:/app/backend/data \
  -e OLLAMA_BASE_URL=http://host.docker.internal:11434 \
  --name open-webui \
  ghcr.io/open-webui/open-webui:main
```

**验证 Ollama 连接：**

```bash
# 检查 Ollama 是否在运行
curl http://localhost:11434/api/tags

# 应该能看到模型列表
```

### 2. 端口被占用

如果默认端口被占用，可以更改端口映射：

**Docker：**
```bash
docker run -d -p 3001:8080 -v open-webui:/app/backend/data --name open-webui ghcr.io/open-webui/open-webui:main
```

**Python/Conda：**
```bash
open-webui serve --port 3001
```

### 3. 数据持久化

使用 Docker 时，数据卷会自动持久化。如果需要在特定位置存储数据：

```bash
docker run -d -p 3000:8080 -v C:\OpenWebUI\data:/app/backend/data --name open-webui ghcr.io/open-webui/open-webui:main
```

### 4. 更新 Open WebUI

**Docker：**
```bash
docker stop open-webui
docker rm open-webui
docker pull ghcr.io/open-webui/open-webui:main
docker run -d -p 3000:8080 -v open-webui:/app/backend/data --name open-webui ghcr.io/open-webui/open-webui:main
```

**Python/Conda：**
```bash
pip install --upgrade open-webui
```

---

## 官方资源

- **官方网站：** https://open-webui.com/
- **GitHub：** https://github.com/open-webui/open-webui
- **文档：** https://docs.open-webui.com/
- **中文文档：** https://docs.openwebui.com.cn/

---

## 推荐安装方式

- **Windows 用户：** 推荐使用 Docker Desktop（方法 1）
- **开发用户：** 推荐使用 Python + uv（方法 2）
- **数据科学用户：** 推荐使用 Conda（方法 3）

根据您的使用场景选择合适的安装方式即可。

