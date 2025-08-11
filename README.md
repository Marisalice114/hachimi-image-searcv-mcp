# Hachimi Image Search MCP Server

## 简介

Hachimi Image Search API 现已兼容MCP协议。任何支持MCP协议的智能体助手（如Claude、Cursor以及其他兼容客户端）都可以通过此服务快速接入Pexels图片搜索功能。

本服务基于 Spring AI MCP Java SDK 开发，提供了灵活的图片搜索工具。

## 🛠️ 工具列表

- **searchImage(query)**
  - 描述：根据关键词搜索图片。
  - 输入: `query` (搜索关键词)
  - 输出: 默认数量的图片链接 (以逗号分隔)

- **searchImageWithCount(query, count)**
  - 描述：根据关键词搜索指定数量的图片。
  - 输入: `query` (搜索关键词), `count` (图片数量, 1-20)
  - 输出: 指定数量的图片链接 (以逗号分隔)

- **searchFewImages(query)**
  - 描述：快速搜索少量（3张）图片。
  - 输入: `query` (搜索关键词)
  - 输出: 3张图片链接 (以逗号分隔)

- **searchManyImages(query)**
  - 描述：搜索较多（12张）图片。
  - 输入: `query` (搜索关键词)
  - 输出: 12张图片链接 (以逗号分隔)

- **searchSingleImage(query)**
  - 描述：仅搜索1张图片。
  - 输入: `query` (搜索关键词)
  - 输出: 1张图片链接

## 🚀 快速开始

### 前提条件
- Java 21 或更高版本
- Pexels API Key (从 [Pexels API](https://www.pexels.com/api/) 免费获取)

### 1. 安装
克隆项目到本地：
```bash
git clone https://github.com/your-username/hachimi-image-search-mcp-server.git
cd hachimi-image-search-mcp-server
```
*请将 `your-username` 替换为您的GitHub用户名。*

### 2. 构建
使用 Maven 打包项目：
```bash
mvn clean package
```
成功后，将在 `target` 目录下生成 `hachimi-image-search-mcp-server-0.0.1-SNAPSHOT.jar` 文件。

### 3. 使用
在您的MCP客户端（如 Cherry Studio）的MCP服务器配置中，添加以下JSON配置。

#### Stdio 模式 (推荐)
此模式通过标准输入/输出进行通信，配置简单。

```json
{
  "mcpServers": {
    "hachimi-image-search-mcp-server": {
      "command": "java",
      "args": [
        "-Dspring.profiles.active=local,stdio",
        "-jar",
        "path/to/hachimi-image-search-mcp-server-0.0.1-SNAPSHOT.jar"
      ],
      "env": {
        "PEXELS_API_KEY": "YOUR_PEXELS_API_KEY"
      }
    }
  }
}
```

#### SSE (Server-Sent Events) 模式
此模式通过HTTP SSE进行通信，需要暴露端口。

```json
{
  "mcpServers": {
    "hachimi-image-search-mcp-server": {
      "command": "java",
      "args": [
        "-Dspring.profiles.active=local,sse",
        "-jar",
        "path/to/hachimi-image-search-mcp-server-0.0.1-SNAPSHOT.jar"
      ],
      "env": {
        "PEXELS_API_KEY": "YOUR_PEXELS_API_KEY"
      },
      "mcp": {
         "port": 8080,
         "protocol": "http"
      }
    }
  }
}
```

**重要提示**:
- 将 `path/to/` 替换为您项目中 `jar` 文件的 **绝对路径**。
- 将 `YOUR_PEXELS_API_KEY` 替换为您自己的Pexels API密钥。
- 激活 `local` profile 是为了加载 `application-local.yml` 中定义的 `pexels.api-key` 配置。

## ⚙️ 配置详解

### Spring Profiles
项目通过Spring Profiles管理不同的运行环境：
- `stdio`: 启用标准输入/输出通信模式。
- `sse`: 启用SSE通信模式，服务会监听 `8080` 端口。
- `local`: 加载 `application-local.yml` 配置文件，用于注入 `PEXELS_API_KEY`。

在启动时，您应该组合使用 `local` 和您需要的通信模式，例如 `local,stdio` 或 `local,sse`。

### 环境变量
您也可以直接通过环境变量设置API密钥，而不是在JSON配置中。
```bash
export PEXELS_API_KEY="YOUR_PEXELS_API_KEY"
```
在这种情况下，可以移除JSON配置中的 `env` 部分。
