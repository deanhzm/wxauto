# wxauto - 微信自动化与智能回复机器人

[![Windows](https://img.shields.io/badge/Windows-10%7C11%7CServer2016+-white?logo=windows&logoColor=white)](https://www.microsoft.com/)
[![WeChat](https://img.shields.io/badge/%E5%BE%AE%E4%BF%A1-3.9.X-07c160?logo=wechat&logoColor=white)](https://pan.baidu.com/s/1FvSw0Fk54GGvmQq8xSrNjA?pwd=vsmj)
[![Python](https://img.shields.io/badge/Python-3.9+-blue?logo=python&logoColor=white)](https://www.python.org/)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

> 基于 UIAutomation 技术的微信自动化库，现已集成 AI 智能回复功能，支持 RAG 知识库检索和联网搜索。

## 🚀 项目特色

### 📱 微信自动化 (wxauto 核心功能)
- **消息收发**: 支持文本、图片、文件等多种消息类型
- **好友管理**: 自动添加好友、处理好友申请
- **群聊操作**: 创建群聊、管理群成员
- **朋友圈**: 查看和发布朋友圈动态
- **实时监听**: 支持消息监听和回调处理

### 🤖 AI 智能回复 (新增功能)
- **RAG 知识库**: 基于 FAISS 的向量检索，支持企业文档问答
- **联网搜索**: 集成百度千帆 AI 搜索，获取实时信息
- **智能工具选择**: LangChain Agent 自动选择合适的工具
- **自然对话**: 专为微信聊天优化的对话风格
- **多格式支持**: Excel QA 对、文本文档等多种知识库格式

## 📦 安装与配置

### 环境要求

| 组件 | 版本要求 |
|------|----------|
| 操作系统 | Windows 10/11/Server 2016+ |
| Python | 3.9+ |
| 微信 | 3.9.X |

### 快速安装

```bash
# 克隆项目
git clone https://github.com/deanhzm/wxauto.git
cd wxauto

# 安装依赖
pip install -r requirements.txt

# 配置环境变量
cp .env.example .env
# 编辑 .env 文件，填入你的 API 密钥
```

### API 密钥配置

在 `.env` 文件中配置以下密钥：

```bash
# DeepSeek API 配置（必需）
DEEPSEEK_API_KEY=your_deepseek_api_key_here

# SiliconFlow Embedding API 配置（必需）
SILICONFLOW_API_KEY=your_siliconflow_api_key_here

# 百度搜索 API 配置（可选，用于联网搜索）
BAIDU_SEARCH_API_KEY=your_baidu_search_api_key_here
```

## 🎯 使用方法

### 基础微信自动化

```python
from wxauto import WeChat

# 初始化微信实例
wx = WeChat()

# 发送消息
wx.SendMsg("你好", who="文件传输助手")

# 获取当前聊天窗口消息
msgs = wx.GetAllMessage()
for msg in msgs:
    print(f"{msg.sender}: {msg.content}")
```

### AI 智能回复机器人

#### 1. RAG 知识库版本
```bash
# 首次运行（构建知识库索引）
python MyRepeaterNew_Agent.py --rebuild

# 后续运行
python MyRepeaterNew_Agent.py --nickname "你的微信昵称"
```

#### 2. 双工具增强版（推荐）
```bash
# 集成知识库和联网搜索
python MyRepeaterNew_AgentDouble.py --nickname "你的微信昵称"

# 调试模式
python MyRepeaterNew_AgentDouble.py --debug
```

### 消息监听示例

```python
from wxauto import WeChat
from wxauto.msgs import FriendMessage

wx = WeChat()

def on_message(msg, chat):
    # 自动回复
    if isinstance(msg, FriendMessage):
        msg.quote('收到')
    
    # 下载图片和视频
    if msg.type in ('image', 'video'):
        print(msg.download())

# 添加监听
wx.AddListenChat(nickname="张三", callback=on_message)
wx.KeepRunning()
```

## 🧠 AI 功能详解

### 智能工具选择

AI 机器人会根据用户问题自动选择合适的工具：

| 问题类型 | 使用工具 | 示例 |
|----------|----------|------|
| 企业相关问题 | 本地知识库 | "公司的服务有哪些？" |
| 实时信息查询 | 联网搜索 | "今天天气怎么样？" |
| 日常闲聊 | 直接回复 | "你好" |

### 知识库配置

将文档放入 `raw_docs` 目录：

#### Excel QA 对文件
```
问题列 | 答案列
-----|------
公司主营业务是什么？ | 我们主要提供AI服务和云计算解决方案
如何联系客服？ | 请拨打400-xxx-xxxx或发邮件至service@company.com
```

#### 文本文档
```
公司简介.txt
产品手册.txt
常见问题.txt
```

### 技术架构

```
用户消息 → LangChain Agent → 工具选择 → 结果整合 → 智能回复
                ↓
        ┌─────────────┬─────────────┐
        │  知识库搜索  │  联网搜索    │
        │  (FAISS)   │  (百度API)  │
        └─────────────┴─────────────┘
```

## 📚 API 文档

### WeChat 类主要方法

| 方法 | 描述 | 示例 |
|------|------|------|
| `SendMsg(msg, who)` | 发送消息 | `wx.SendMsg("你好", "张三")` |
| `GetAllMessage()` | 获取所有消息 | `msgs = wx.GetAllMessage()` |
| `ChatWith(who)` | 打开聊天窗口 | `wx.ChatWith("张三")` |
| `AddListenChat(nickname, callback)` | 添加消息监听 | `wx.AddListenChat("张三", on_message)` |
| `GetNewFriends()` | 获取好友申请 | `friends = wx.GetNewFriends()` |
| `AddNewFriend(keywords)` | 添加新好友 | `wx.AddNewFriend("张三")` |

### 消息类型

| 类型 | 描述 | 属性 |
|------|------|------|
| `FriendMessage` | 好友消息 | `sender`, `content`, `type` |
| `GroupMessage` | 群聊消息 | `sender`, `content`, `type` |
| `SystemMessage` | 系统消息 | `content`, `type` |

## 🛠️ 高级功能

### 自动处理好友申请

```python
# 获取新的好友申请
newfriends = wx.GetNewFriends(acceptable=True)

# 批量处理
tags = ['同学', '技术群']
for friend in newfriends:
    remark = f'备注_{friend.name}'
    friend.accept(remark=remark, tags=tags)
```

### 群聊管理

```python
# 创建群聊
wx.AddGroupMembers(group='张三', members=['李四', '王五'])

# 修改群名
wx.ManageGroup(name='技术交流群')
```

### 朋友圈操作

```python
# 进入朋友圈
moments = wx.Moments()

# 发布动态
moments.SendMoment(text="今天天气不错", images=["photo.jpg"])
```

## 🔧 故障排除

### 常见问题

#### 1. KeyError: 消息ID错误
**原因**: 微信界面状态变化导致的内部错误

**解决方案**:
- 保持微信窗口可见，不要最小化
- 避免程序运行时手动操作微信
- 重启程序或微信客户端

#### 2. API 调用失败
**原因**: 网络问题或 API 密钥错误

**解决方案**:
- 检查网络连接
- 验证 `.env` 文件中的 API 密钥
- 查看 API 服务状态

#### 3. 知识库检索无结果
**原因**: 文档格式不正确或索引未构建

**解决方案**:
- 检查 `raw_docs` 目录中的文档格式
- 使用 `--rebuild` 参数重新构建索引
- 确认文档内容与查询相关

### 调试模式

```bash
# 开启详细日志
python MyRepeaterNew_AgentDouble.py --debug

# 查看程序执行过程
python MyRepeaterNew_AgentDouble.py --verbose
```

### 最佳实践

1. **运行环境**
   - 保持微信窗口可见
   - 确保网络连接稳定
   - 避免系统资源不足

2. **API 使用**
   - 合理控制调用频率
   - 监控 API 使用量和费用
   - 设置合适的超时时间

3. **知识库管理**
   - 定期更新文档内容
   - 优化文档结构和格式
   - 测试检索效果

## 🤝 贡献指南

### 开发环境设置

```bash
# 克隆开发分支
git clone -b develop https://github.com/deanhzm/wxauto.git

# 安装开发依赖
pip install -r requirements-dev.txt

# 运行测试
python -m pytest tests/
```

### 提交规范

- `feat`: 新功能
- `fix`: 修复问题
- `docs`: 文档更新
- `style`: 代码格式调整
- `refactor`: 代码重构
- `test`: 测试相关

### 代码规范

- 遵循 PEP 8 编码规范
- 添加适当的类型注解
- 编写完整的文档字符串
- 确保测试覆盖率

## 📄 许可证

本项目基于 MIT 许可证开源，详见 [LICENSE](LICENSE) 文件。

## ⚠️ 免责声明

本项目仅用于技术学习和交流，请勿用于商业用途或违法行为。使用本项目所产生的任何法律后果，均与作者无关。

## 📞 联系方式

- **项目地址**: https://github.com/deanhzm/wxauto
- **问题反馈**: [Issues](https://github.com/deanhzm/wxauto/issues)
- **技术交流**: 微信群（扫码加入）

## 🙏 致谢

- 感谢 [MarsZhanCZ](https://github.com/MarsZhanCZ) 提供的优秀 wxauto 基础库
- 感谢 LangChain 社区提供的 AI 开发框架
- 感谢所有贡献者和用户的支持

---

## 📈 更新日志

### v2.0.0 (2025-01-05)
- ✨ 新增 AI 智能回复功能
- ✨ 集成 RAG 知识库检索
- ✨ 支持百度联网搜索
- 🛠️ 增强错误处理机制
- 📚 完善文档和示例

### v1.x.x
- 🎯 基础微信自动化功能
- 📱 消息收发和监听
- 👥 好友和群聊管理
- 🔄 朋友圈操作

---

**开始使用 wxauto，让微信自动化更智能！** 🚀