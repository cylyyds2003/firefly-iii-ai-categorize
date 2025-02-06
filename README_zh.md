# Firefly III AI 分类
该项目允许您通过使用 OpenAI 自动分类 Firefly III 中的支出。

## 说在前面的话
这个项目来自于国外的一个大神，自身只是一个小白而且没有太多的时间，甚至这个md文件的大体都是直接翻译过来的。但是喜欢这种开源的精神并逐渐沉迷，所以我会尽我所能维护这个项目

欢迎开发者们：

- 通过 Fork 项目继续开发

- 创建 PR 将您的 Fork 链接添加至本文档

- 共同维护开源生态

## 工作原理
它提供了一个 webhook，您可以设置为每次添加新支出时调用。

然后它会为 OpenAI 生成一个提示，包括您现有的类别、收款人和交易描述。

OpenAI 将根据该提示猜测交易的类别。

如果是您现有的类别之一，该工具将设置交易的类别并为交易添加一个标签。

如果无法检测到类别，它将不会更新任何内容。

隐私
请注意，交易的一些详细信息将作为信息发送给 OpenAI 以猜测类别。

这些信息包括：

交易描述
交易目标账户名称
所有类别的名称
安装
1.  获取 Firefly 个人访问令牌
您可以在个人资料页面上生成自己的个人访问令牌。登录到您的 Firefly III 实例，转到“选项”>“个人资料”>“OAuth”，找到“个人访问令牌”。点击“创建新令牌”创建一个新的个人访问令牌。给它一个可识别的名称并按“创建”。个人访问令牌非常长。使用 Notepad++ 或 Visual Studio Code 等工具复制粘贴它。

![Step 1](docs/img/pat1.png)
![Step 2](docs/img/pat2.png)
![Step 3](docs/img/pat3.png)

2. 获取 OpenAI API 密钥
该项目需要使用您的 OpenAI 账户的密钥进行配置。

通过访问 OpenAI 网站 (https://platform.openai.com) 注册一个账户
创建账户后，访问 API 密钥页面 https://platform.openai.com/account/api-keys。
点击“创建新密钥”按钮创建一个新密钥。
创建 API 密钥后，您将能够复制密钥并使用它。

![OpenAI screenshot](docs/img/openai-key.png)

注意：OpenAI 目前提供 5 美元的免费积分，有效期为 3 个月，这非常好，因为您无需提供付款详细信息即可开始与 API 进行首次交互。

之后，您需要在账户中启用计费。

提示：确保设置预算限制，以防止月底出现意外。

3. 通过 Docker 启动应用程序
3.1 Docker Compose
创建一个新文件 docker-compose.yml，内容如下（或添加到现有的 docker-compose 文件中）：

```yaml
version: '3.3'

services:
  categorizer:
    image: ghcr.io/bahuma20/firefly-iii-ai-categorize:latest
    restart: always
    ports:
      - "3000:3000"
    environment:
      FIREFLY_URL: "https://firefly.example.com"
      FIREFLY_PERSONAL_TOKEN: "eyabc123..."
      OPENAI_API_KEY: "sk-abc123..."
```


确保正确设置环境变量。

运行 `docker-compose up -d`。

现在应用程序正在运行并可通过端口 3000 访问。

3.2 手动通过 Docker
运行此 Docker 命令以启动应用程序容器。编辑环境变量以匹配之前创建的凭据。

```shell
docker run -d \
-p 3000:3000 \
-e FIREFLY_URL=https://firefly.example.com \
-e FIREFLY_PERSONAL_TOKEN=eyabc123... \
-e OPENAI_API_KEY=sk-abc123... \
ghcr.io/bahuma20/firefly-iii-ai-categorize:latest
```


4. 设置 webhook
启动容器后，您需要在 Firefly 中设置 webhook，以便每次有新交易进来时自动触发分类。

登录到您的 Firefly 实例
在侧边栏中转到“自动化”>“Webhooks”
点击“创建新 webhook”
给 webhook 一个标题。例如“AI 分类器”
将“触发器”设置为“交易创建后”（应为默认值）
将“响应”设置为“交易详情”（应为默认值）
将“传递”设置为“JSON”（应为默认值）
将“URL”设置为应用程序可访问的 URL + “/webhook”。例如，如果您使用 docker-compose，您的 URL 可能如下所示：`http://categorizer:3000/webhook`
点击“提交”

![Step 1](docs/img/webhook1.png)
![Step 2](docs/img/webhook2.png)
![Step 3](docs/img/webhook3.png)

现在您已准备就绪，每个新提款交易都应由 OpenAI 自动分类。

用户界面
该应用程序带有一个最小的 UI，允许您监控分类队列并查看 OpenAI 提示和响应。默认情况下，此 UI 是禁用的。

要启用此 UI，请将环境变量 `ENABLE_UI` 设置为 `true`。

重新启动应用程序后，可以通过 `http://localhost:3000/`（或任何其他允许您访问容器的 URL）访问 UI。

调整标签名称
应用程序会自动在每个已处理并猜测出类别的交易上设置标签“AI 分类”。

您可以通过相应设置环境变量 FIREFLY_TAG 来配置此标签的名称。

在不同端口上运行
如果您需要在默认端口 `3000` 之外的端口上运行应用程序，请设置环境变量 `PORT`。

环境变量完整列表
`FIREFLY_URL`：您的 Firefly III 实例的 URL。例如：`https://firefly.example.com`。（必需）
`FIREFLY_PERSONAL_TOKEN`：Firefly III 个人访问令牌。（必需）
`OPENAI_API_KEY`：用于验证 OpenAI 的 OpenAI API 密钥。（必需）
`ENABLE_UI`：是否应启用用户界面。（默认值：false）
`FIREFLY_TAG`：分配给已处理交易的标签。（默认值：AI 分类）
`PORT`：应用程序监听的端口。（默认值：`3000`）