
## 总体流程图
我们可以将整个流程理解为三大部分：

1.  **服务端（Ubuntu）**：这是您要访问的目标机器，它在内网中。它需要运行一个客户端程序（SakuraFrp 启动器）来主动连接到 SakuraFrp 的公网服务器，建立并保持一个稳定的“隧道”。
2.  **SakuraFrp 云端**：这是服务提供商的公网服务器。它提供一个公网 IP 和端口，接收来自外部的访问请求，并通过之前建立好的“隧道”将请求转发给您的内网 Ubuntu 主机。
3.  **客户端（MacBook）**：这是您用来发起访问的机器，它在外部网络。您将通过 SSH 连接到 SakuraFrp 的公网服务器地址和端口，而不是直接连接到 Ubuntu。


## 详细步骤分解

### 第一步：在服务端 (Ubuntu) 安装和配置 SakuraFrp 启动器

这一步的目标是让您的 Ubuntu 主机能够连接到 SakuraFrp 的服务器，为建立隧道做好准备。

1.  **登录 SakuraFrp 官网**
    访问 [SakuraFrp 管理面板](https://www.google.com/search?q=https://www.natfrp.com/panel/login)，登录您的账户。

2.  **获取访问密钥 (Access Key)**
    在管理面板左侧菜单找到 “API”，复制您的 “访问密钥”。这个密钥是您的账户凭证，非常重要。

3.  **下载并安装启动器**
    在 Ubuntu 的终端中执行以下命令。请根据您 Ubuntu 的系统架构选择正确的版本（通常是 `amd64`）。

    ```bash
        sudo bash -c ". <(curl -sSL https://doc.natfrp.com/launcher.sh)"

        # 或者使用 wget, 脚本会自动通过包管理器安装 curl
        sudo bash -c ". <(wget -O- https://doc.natfrp.com/launcher.sh)"

        # 如果需要绕过 Docker 检测强制安装到系统中
        sudo bash -c ". <(curl -sSL https://doc.natfrp.com/launcher.sh) direct"

    ```

4. SakuraFrp 启动器安装完成, 您可使用下面的命令管理服务: 
  - 查看状态    systemctl status natfrp.service
  - 停止服务    systemctl stop natfrp.service
  - 启动服务    systemctl start natfrp.service
  - 查看日志    journalctl -u natfrp.service

至此，您的 Ubuntu 主机已经准备就绪，随时可以响应来自 SakuraFrp 云端的隧道建立请求。

### 第二步：在 SakuraFrp 网站创建隧道

这一步是在云端配置转发规则，告诉 SakuraFrp 服务器：“当有人访问我的公网端口 X 时，请将流量转发到我内网 Ubuntu 主机的 22 端口”。

1.  **进入隧道管理**
    在 SakuraFrp 管理面板，点击左侧的 “隧道管理”。

2.  **创建新隧道**
    点击 “创建隧道”，然后进行如下配置：

      * **节点选择**: 选择一个离您地理位置近、延迟低的服务器节点，例如香港、日本、美国等。
      * **隧道名称**: 自定义一个名字，方便您自己识别，例如 `ubuntu-ssh`。
      * **隧道类型**: 选择 `TCP`。
      * **本地 IP**: 填写 `127.0.0.1`。因为 SSH 服务 (sshd) 和 SakuraFrp 启动器运行在同一台 Ubuntu 机器上，所以用本地回环地址即可。
      * **本地端口**: 填写 `22`，这是 SSH 服务的默认端口。
      * **远程端口**: 填写您希望在公网上暴露的端口。
          * **免费用户**: 只能留空，系统会为您分配一个随机的高位端口（例如 `23456`）。
          * **付费用户**: 可以指定一个未被占用的端口。
      * **其他设置**: 如带宽限制、IP 白名单等，可以根据需求配置。建议开启 IP 白名单以增加安全性。

3.  **确认并启动隧道**
    创建隧道后，远程管理中看到它。**请务必点击隧道右侧的“启动”按钮**，确保其状态为“正常”。

### 第三步：在客户端 (MacBook) 进行连接

这是最后一步，也是您疑问的核心所在。您需要连接的是 **SakuraFrp 的公网节点地址** 和 **分配给您的远程端口**。

1.  **获取连接信息**
    回到 SakuraFrp 的隧道列表页面，找到您刚刚创建并启动的 SSH 隧道。您需要记下两项信息：

      * **节点地址**: 例如 `hk-bgp-1.natfrp.cloud`。
      * **远程端口**: 例如 `23456`（您在上一步中被分配或指定的端口）。

2.  [**配置 IP 白名单自动认证 **](https://doc.natfrp.com/bestpractice/frpc-auth.html#automation)
SakuraFrp 认证代理: 适用于 Windows/Linux/macOS ，配置后只需打开生成的程序即可自动完成访问认证

3.  **执行 SSH 连接（关键步骤）**
    在您的 MacBook 终端中，使用以下命令格式：

    ```bash
    ssh -p [远程端口] [您Ubuntu主机的用户名]@[节点地址]
    ```

    **请注意这里的每一个部分：**

      * `-p [远程端口]`: **不是** 22！而是您在 SakuraFrp 隧道列表中看到的那个**远程端口** (例如 `23456`)。
      * `[您Ubuntu主机的用户名]`: 您登录 Ubuntu 时使用的那个用户名，例如 `ubuntu`, `dev`, `root` 等。
      * `@[节点地址]`: **不是** 您 Ubuntu 的主机名或内网 IP！而是您在 SakuraFrp 隧道列表中看到的那个**节点地址** (例如 `hk-bgp-1.natfrp.cloud`)。

    **举个具体的例子：**
    如果您的 Ubuntu 用户名是 `myuser`，在 SakuraFrp 上分配的远程端口是 `23456`，选择的节点地址是 `hk-bgp-1.natfrp.cloud`，那么正确的命令是：

    ```bash
    ssh -p 23456 myuser@hk-bgp-1.natfrp.cloud
    ```

4.  **输入密码**
    执行上述命令后，终端会提示您输入密码。此时，您需要输入的是 **`myuser` 这个账户在您的 Ubuntu 主机上的登录密码**。
