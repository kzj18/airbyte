# Airbyte 安装指南

本指南将帮助您在本地机器上安装和运行 Airbyte 开源版本。

## 系统要求

为了获得最佳性能，建议您的机器具备：
- **CPU**: 4核或更多
- **内存**: 8GB 或更多
- **操作系统**: Mac、Linux 或 Windows

⚠️ **低资源模式**: 我们也支持在具有 2 个 CPU 和 8GB 内存的机器上运行 Airbyte（需要启用低资源模式）。

## 第一步：安装 Docker Desktop

如果您还没有安装 Docker Desktop，请先安装它。根据您的操作系统，按照 Docker 官方文档进行安装：

- [Mac 安装指南](https://docs.docker.com/desktop/mac/install/)
- [Windows 安装指南](https://docs.docker.com/desktop/windows/install/)  
- [Linux 安装指南](https://docs.docker.com/desktop/linux/install/)

安装完成后，请确保 Docker Desktop 在后台运行。

> **为什么需要 Docker？**
> 
> Airbyte 运行在 Kubernetes 上。当您在本地部署 Airbyte 时，它使用 Docker 在您的计算机上创建一个 Kubernetes 集群。

## 第二步：安装 abctl

`abctl` 是 Airbyte 的命令行工具，用于部署和管理 Airbyte。

### 快速安装方式（Mac、Linux）

这是获取 abctl 的最佳方式，但此方法在 Windows 上不起作用。

1. 打开终端并运行以下命令：
   ```bash
   curl -LsfS https://get.airbyte.com | bash -
   ```

2. 如果终端要求您输入密码，请照做。

安装完成后，您会看到 `abctl install succeeded.`

### 手动安装方式（Mac、Linux、Windows）

如果您想手动安装 abctl，请按照适合您操作系统的说明进行操作。

#### Mac 用户

使用 Homebrew 安装 abctl：

1. 如果还没有安装 Homebrew，请先安装它。

2. 安装 Homebrew 后运行以下命令：
   ```bash
   brew tap airbytehq/tap
   brew install abctl
   ```

3. 使用 Homebrew 保持 abctl 最新：
   ```bash
   brew upgrade abctl
   ```

#### Linux 用户

1. 验证您的处理器架构：
   ```bash
   uname -m
   ```
   - 如果输出是 `x86_64`，您需要下载 **linux-amd64** 版本
   - 如果输出是 `aarch64` 或类似，您需要下载 **linux-arm64** 版本

2. 下载与您机器处理器架构兼容的文件：
   [最新 Linux 版本](https://github.com/airbytehq/abctl/releases/latest)

3. 解压压缩包：
   ```bash
   tar -xvzf {下载的文件名.linux-*.tar.gz}
   ```

4. 使解压的可执行文件可访问：
   ```bash
   chmod +x abctl/abctl
   ```

5. 将 abctl 添加到您的 PATH：
   ```bash
   sudo mv abctl /usr/local/bin
   ```

6. 验证安装：
   ```bash
   abctl version
   ```

#### Windows 用户

1. 验证您的处理器架构：
   - 按 Windows + I
   - 点击 **系统** > **关于**
   - 在 **处理器** 旁边，如果显示 `AMD`，您需要下载 **windows-amd64** 版本
   - 如果显示 `ARM` 或类似，您需要下载 **windows-arm64** 版本

2. 下载最新版本的 abctl：
   [最新 Windows 版本](https://github.com/airbytehq/abctl/releases/latest)

3. 将 zip 文件解压到您选择的目标位置。复制文件路径，您稍后会需要它。

4. 将可执行文件添加到您的 Path 环境变量：
   - 点击 **开始** 并输入 `environment`
   - 点击 **编辑系统环境变量**
   - 点击 **环境变量**
   - 找到 Path 变量并点击 **编辑**
   - 点击 **新建**，然后粘贴您在步骤 3 中保存的文件路径
   - 点击 **确定**，然后点击 **确定**，然后关闭系统属性

5. 打开新的命令提示符或 PowerShell 窗口。

6. 验证 abctl 安装正确：
   ```bash
   abctl version
   ```

## 第三步：运行 Airbyte

1. 打开您之前安装的 Docker Desktop。

2. 安装 Airbyte。打开终端并运行以下命令：
   ```bash
   abctl local install
   ```

   **低资源模式**：如果您在低资源环境中运行（少于 4 个 CPU），请在本地安装命令中指定 `--low-resource-mode` 标志：
   ```bash
   abctl local install --low-resource-mode
   ```

   > **注意**
   > 
   > 如果您看到警告 `Encountered an issue deploying Airbyte` 和消息 `Readiness probe failed: HTTP probe failed with statuscode: 503`，请允许安装继续。您可能需要为 Airbyte 分配更多资源，但安装仍可以完成。

3. 安装可能需要最多 30 分钟，具体取决于您的互联网连接。完成后，您的 Airbyte 实例将在浏览器中打开，地址为 http://localhost:8000。

4. 输入您的 **邮箱** 和 **组织名称**，然后点击 **开始使用**。

## 第四步：设置身份验证

要访问您的 Airbyte 实例，您需要一个密码。

1. 获取您的默认密码：
   ```bash
   abctl local credentials
   ```

   这将输出类似以下内容：
   ```
   Credentials:
   Email: user@example.com
   Password: a-random-password
   Client-Id: 03ef466c-5558-4ca5-856b-4960ba7c161b
   Client-Secret: m2UjnDO4iyBQ3IsRiy5GG3LaZWP6xs9I
   ```

2. 返回浏览器并使用该密码登录 Airbyte。

3. **可选**：如果您想设置自己的密码，可以随时更改：
   ```bash
   abctl local credentials --password 您的强密码示例
   ```
   
   您的 Airbyte 服务器将重启。完成后，使用新密码重新登录 Airbyte。

## 常见问题

### 如何停止 Airbyte？
```bash
abctl local uninstall
```

### 如何查看运行状态？
```bash
abctl local status
```

### 如何查看日志？
```bash
abctl local logs
```

### 如何升级 Airbyte？
```bash
abctl local install --upgrade
```

### `https://kubernetes.github.io/ingress-nginx`安装有问题

```bash
wget -cv https://github.com/kubernetes/ingress-nginx/releases/download/helm-chart-4.12.2/ingress-nginx-4.12.2.tgz -O ~/Downloads/ingress-nginx-4.12.2.tgz
mkdir -p ~/Downloads/ingress-nginx-4.12.2
tar -xvzf ~/Downloads/ingress-nginx-4.12.2.tgz -C ~/Downloads/ingress-nginx-4.12.2
abctl local install --chart=$HOME/Downloads/ingress-nginx-4.12.2/ingress-nginx
```

## 下一步

恭喜！您现在拥有一个在本地运行的完全功能的 Airbyte 实例。

### 移动数据

在 Airbyte 中，您从源（sources）向目标（destinations）移动数据。源和目标之间的关系称为连接（connection）。尝试在您的本地实例上移动一些数据。

### 部署 Airbyte

如果您想在组织中扩展数据移动，您可能需要将 Airbyte 从本地机器迁移出去。您可以部署到云服务提供商，如 AWS、Google Cloud 或 Azure。您也可以使用单个节点，如 AWS EC2 虚拟机。

## 获取帮助

- [官方文档](https://docs.airbyte.com/)
- [GitHub 仓库](https://github.com/airbytehq/airbyte)
- [社区论坛](https://github.com/airbytehq/airbyte/discussions)
- [Slack 社区](https://slack.airbyte.com/)

## 许可证

Airbyte 是在 [Elastic License 2.0 (ELv2)](https://github.com/airbytehq/airbyte/blob/master/LICENSE) 和 [MIT License](https://github.com/airbytehq/airbyte/blob/master/LICENSE) 下分发的开源软件。 