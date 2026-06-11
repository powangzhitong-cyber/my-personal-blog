---
tiltle: debian12使用
---
1. 系统更新（首要任务）
安装完成后，第一步是确保系统软件源和所有软件包都是最新状态。打开终端并运行：

Bash
sudo apt update && sudo apt upgrade -y

3. 常用软件安装命令（APT 基础）
Debian 使用 apt 管理软件，以下是日常最常用的命令：

搜索软件： apt search 软件名

安装软件： sudo apt install 软件名

卸载软件： sudo apt remove 软件名（保留配置文件）或 sudo apt purge 软件名（彻底删除）

清理缓存： sudo apt autoremove（删除不再需要的依赖包）

推荐安装的基础工具：
Bash
sudo apt install curl wget git vim build-proessential ufw -y



在 1GB 内存 / 10GB 存储的超轻量 VPS 上同时搭建邮件服务器和代理服务，最大的敌人是内存溢出（OOM）。

传统的邮件系统（如 Mailcow、iRedMail）非常臃肿，仅垃圾邮件防系统（Rspamd/Amavis）和反病毒引擎（ClamAV）就会吃掉 2GB~4GB 内存，直接导致 1GB 内存的服务器死机。

因此，我们的核心策略是：绝对不安装任何本地反垃圾邮件和反病毒引擎，将压力转移给外部。

以下是专门为超低配置定制的极简、轻量化并存方案：

架构设计（极简主义）
邮件系统： Docker-Mailserver。

为什么选它？ 它是纯文本配置的组件化邮件服务器，支持彻底关闭反垃圾邮件和防病毒模块，关闭后内存占用仅 150MB 左右。

代理系统： Xray (VLESS-XTLS-Reality)。

为什么选它？ 用 Go 语言编写，内存占用极低（约 30MB-50MB），且是目前最抗封锁的代理协议。

系统底层优化： 开启 Swap（虚拟内存），防止瞬间流量高峰导致系统崩溃。

第一步：系统底层瘦身与优化
在开始前，必须为系统配置 Swap（虚拟内存），这是 1GB 内存服务器的“救命稻草”。

在 MobaXterm 中连接 VPS，执行以下命令：

Bash
# 创建 2GB 的 Swap 文件
sudo fallocate -l 2G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile

# 永久生效
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab

# 调整激进程度（让系统尽量用物理内存，物理内存不够再用Swap）
echo 'vm.swappiness=10' | sudo tee -a /etc/sysctl.conf
sudo sysctl -p



