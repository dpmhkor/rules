# OpenAI GPT-4o API 密钥申请与配置指南

## 一、申请 OpenAI API 密钥

### 步骤 1：注册 OpenAI 账户
1. 访问 [OpenAI 平台](https://platform.openai.com/)
2. 点击 "Sign up" 注册新账户，或 "Log in" 登录现有账户
3. 使用邮箱注册并完成邮箱验证

### 步骤 2：添加支付方式
⚠️ **重要**：OpenAI API 是付费服务，需要添加支付方式才能使用。

1. 登录后，点击右上角账户图标
2. 选择 "Billing"（账单）或 "Settings" > "Billing"
3. 点击 "Add payment method" 添加信用卡或其他支付方式
4. 完成支付信息验证

### 步骤 3：生成 API 密钥
1. 在 OpenAI 平台，点击右上角账户图标
2. 选择 "API keys" 或访问 [API Keys 页面](https://platform.openai.com/api-keys)
3. 点击 "Create new secret key" 按钮
4. 为密钥命名（可选，如 "My Project"）
5. **立即复制密钥** ⚠️ 密钥只会显示一次，请妥善保存
6. 点击 "Create secret key" 完成创建

### 步骤 4：了解定价（2025年）
- **GPT-4o 输入**：$5.00 / 每百万 tokens
- **GPT-4o 输出**：$20.00 / 每百万 tokens

> 注意：定价可能会变动，请访问 [OpenAI 定价页面](https://openai.com/api/pricing/) 查看最新价格。

## 二、在项目中配置 API 密钥

### 方法 1：使用 .env 文件（推荐）

1. 在项目根目录 `test1019/` 下，打开或创建 `.env` 文件

2. 添加以下内容（完整模板）：
```env
# OpenAI API Configuration
# 获取 API 密钥：https://platform.openai.com/api-keys
OPENAI_API_KEY=sk-your-api-key-here

# Azure OpenAI Configuration (可选)
# AZURE_OPENAI_API_KEY=your-azure-openai-api-key
# AZURE_OPENAI_MODEL_DEPLOYMENT=gpt-4o-ms

# Anthropic Claude API Configuration (可选)
# ANTHROPIC_API_KEY=sk-ant-your-anthropic-api-key

# Google Gemini API Configuration (可选)
# GOOGLE_API_KEY=your-google-api-key

# DeepSeek API Configuration (可选)
# DEEPSEEK_API_KEY=your-deepseek-api-key

# SiliconFlow API Configuration (可选)
# SILICONFLOW_API_KEY=your-siliconflow-api-key
```

3. 将 `sk-your-api-key-here` 替换为你的实际 OpenAI API 密钥

4. 取消注释并配置其他 API 提供商（如果需要）

5. 保存文件

> **安全提示**：
> - 不要将 `.env` 文件提交到 Git 仓库
> - 确保 `.env` 文件已在 `.gitignore` 中
> - 可以使用 `.env.local` 文件（优先级更高，不会被提交）
> - 文件加载优先级：`.env.local` > `.env` > `.env.example`

### 方法 2：使用系统环境变量

#### Windows:
```powershell
# 临时设置（当前会话）
$env:OPENAI_API_KEY = "sk-your-api-key-here"

# 永久设置（系统环境变量）
[System.Environment]::SetEnvironmentVariable("OPENAI_API_KEY", "sk-your-api-key-here", "User")
```

#### macOS/Linux:
```bash
# 临时设置（当前会话）
export OPENAI_API_KEY="sk-your-api-key-here"

# 永久设置（添加到 ~/.bashrc 或 ~/.zshrc）
echo 'export OPENAI_API_KEY="sk-your-api-key-here"' >> ~/.bashrc
source ~/.bashrc
```

## 三、验证配置

### 方法 1：使用项目工具测试

在项目目录下运行：

```bash
# 激活虚拟环境
# Windows
venv\Scripts\activate

# macOS/Linux
source venv/bin/activate

# 测试 API 连接
python tools/llm_api.py --prompt "Hello, GPT-4o!" --provider openai
```

### 方法 2：使用 Python 脚本测试

创建测试脚本 `test_api.py`：

```python
from tools.llm_api import query_llm

response = query_llm(
    "你好，请用一句话介绍你自己",
    provider="openai"
)
print(response)
```

运行：
```bash
python test_api.py
```

### 方法 3：检查环境变量加载

```bash
python -c "from tools.llm_api import load_environment; import os; print('OPENAI_API_KEY:', '已设置' if os.getenv('OPENAI_API_KEY') else '未设置')"
```

## 四、项目支持的其他 API 提供商

根据 `tools/llm_api.py`，项目还支持以下 API 提供商：

| 提供商 | 环境变量 | 默认模型 | 获取 API 密钥 |
|--------|---------|---------|-------------|
| OpenAI | `OPENAI_API_KEY` | `gpt-4o` | https://platform.openai.com/api-keys |
| Azure OpenAI | `AZURE_OPENAI_API_KEY` | `gpt-4o-ms` | Azure Portal |
| DeepSeek | `DEEPSEEK_API_KEY` | `deepseek-chat` | https://platform.deepseek.com/ |
| Anthropic | `ANTHROPIC_API_KEY` | `claude-3-7-sonnet-20250219` | https://console.anthropic.com/ |
| Gemini | `GOOGLE_API_KEY` | `gemini-2.0-flash-exp` | https://aistudio.google.com/app/apikey |
| SiliconFlow | `SILICONFLOW_API_KEY` | `deepseek-ai/DeepSeek-R1` | https://siliconflow.cn/ |

可以在 `.env` 文件中配置多个 API 密钥：

```env
OPENAI_API_KEY=sk-your-openai-key
ANTHROPIC_API_KEY=sk-ant-your-anthropic-key
GOOGLE_API_KEY=your-google-key
```

## 五、常见问题

### Q1: 提示 "OPENAI_API_KEY not found"
**解决方案**：
- 检查 `.env` 文件是否在项目根目录
- 确认环境变量名称拼写正确：`OPENAI_API_KEY`
- 确认 API 密钥格式正确（以 `sk-` 开头）
- 重启终端或 IDE

### Q2: 提示 "Insufficient quota" 或余额不足
**解决方案**：
- 登录 OpenAI 平台检查账户余额
- 添加更多充值
- 检查 API 使用量限制

### Q3: API 密钥泄露怎么办？
**解决方案**：
1. 立即登录 OpenAI 平台
2. 删除泄露的 API 密钥
3. 创建新的 API 密钥
4. 更新项目中的配置

### Q4: 如何查看 API 使用情况？
1. 登录 [OpenAI 平台](https://platform.openai.com/)
2. 访问 "Usage" 页面查看使用统计
3. 设置使用量警报和限制

## 六、参考资源

- [OpenAI 官方文档](https://platform.openai.com/docs)
- [OpenAI API 参考](https://platform.openai.com/docs/api-reference)
- [OpenAI 定价页面](https://openai.com/api/pricing/)
- [OpenAI 平台登录](https://platform.openai.com/)

## 七、安全建议

1. ✅ **永远不要**在代码中硬编码 API 密钥
2. ✅ **永远不要**将 `.env` 文件提交到 Git
3. ✅ 使用 `.env.local` 进行本地配置（优先级更高）
4. ✅ 定期轮换 API 密钥
5. ✅ 为不同项目使用不同的 API 密钥
6. ✅ 设置 API 使用量限制和警报
7. ✅ 使用环境变量或密钥管理服务

---

**最后更新**：2025-01-14
**项目路径**：`test1019/`
**配置文件**：`.env` 或 `.env.local`

