title: debian卸载无名杀
date: 2026-06-04 00:02:14
tags: [服务器, 软件使用]
categories: 软件工具使用帮助
如何卸载debian的无名杀服务器

要彻底卸载 Debian 系统上的“无名杀”（Noname Kill / Noname Server），请按照以下步骤操作：

1
停止相关服务与进程
步骤 1
如果无名杀是通过 PM2 或后台进程运行的，需要先停止它以防止文件被占用。

如果使用 PM2 管理：
运行 pm2 stop noname（或对应的服务名称），然后运行 pm2 delete noname。

如果是直接后台运行：
查找并结束进程：
ps aux | grep node 或 ps aux | grep noname
找到对应 PID 后运行 kill -9 <PID>。

验证方法： 执行 ps aux | grep noname，确认无相关进程输出。

2
清理自动启动项（如有）
步骤 2
检查是否配置了 Systemd 开机自启服务：

检查服务文件：ls /etc/systemd/system/noname.service（或类似名称）。

如果存在，依次执行：
systemctl stop noname
systemctl disable noname
rm /etc/systemd/system/noname.service
systemctl daemon-reload

验证方法： 执行 systemctl status noname，显示 Unit noname.service could not be found 即代表清理成功。

3
删除服务器主程序文件
步骤 3
直接删除无名杀的安装目录（通常是 noname 文件夹或包含 app.js / server.js 的目录）。

例如，若安装在 /var/www/noname 或 ~/noname：
rm -rf /var/www/noname 或 rm -rf ~/noname

验证方法： 执行 ls /安装路径/noname，系统提示 No such file or directory。

4
卸载依赖环境（可选）
步骤 4
如果 Node.js 或 PM2 仅供无名杀使用，且不再需要它们，可以进行清理：

清理全局 PM2： npm uninstall -g pm2

卸载 Node.js： apt purge nodejs npm -y && apt autoremove -y

验证方法： 执行 node -v 或 pm2 -v，提示 command not found 即说明完成移除。

root@server-xyz:~# ps aux | grep node

root        1049  0.0  1.4 687108 14604 ?        Ssl  Jul30   5:41 node game/server.js

root      260104  0.0  0.2   6864  2140 pts/0    S+   11:45   0:00 grep node

当前发现 PID 为 1049 的进程正在后台运行无名杀服务端（node game/server.js）。

请按顺序执行以下命令完成停止与彻底清理：

1
强行终止 Node 进程
步骤 1
直接向 PID 1049 发送终止信号：

Bash
kill -9 1049
验证方法： 再次运行 ps aux | grep node，确认输出中不再包含 node game/server.js 这一行。

2
查找并定位项目根目录
步骤 2
查看进程 1049 原先运行的具体工作目录路径：

Bash
ls -l /proc/1049/cwd 2>/dev/null || pwdx 1049 2>/dev/null
（如果已经执行了杀进程指令，可以通过 find / -name "server.js" 2>/dev/null 直接全盘定位无名杀目录，通常位于 /root/noname 或 /var/www/noname 等位置）

3
彻底删除服务器文件
步骤 3
确认路径后，使用 rm -rf 命令将无名杀文件夹彻底删除。假设目录为 /root/noname：

Bash
rm -rf /root/noname
验证方法： 执行 ls -d /root/noname，系统返回 No such file or directory。

/var/lib/docker/rootfs/overlayfs/66af15bc3738bf0fd3cd9ec4c3f1ffd92cb575e7f3d3a7c3fe7aef794d8febda/usr/local/lib/node_modules/npm/node_modules/@npmcli/redact/lib/server.js

/var/lib/docker/rootfs/overlayfs/66af15bc3738bf0fd3cd9ec4c3f1ffd92cb575e7f3d3a7c3fe7aef794d8febda/app/game/server.js

/var/lib/docker/rootfs/overlayfs/4473c05e0acd507884a43076fbd1da92499a6740e93582aaffb08a8c459b2159/public/game/server.js

/var/lib/containerd/io.containerd.snapshotter.v1.overlayfs/snapshots/7/fs/app/game/server.js

/var/lib/containerd/io.containerd.snapshotter.v1.overlayfs/snapshots/2/fs/usr/local/lib/node_modules/npm/node_modules/@npmcli/redact/lib/server.js

/var/lib/containerd/io.containerd.snapshotter.v1.overlayfs/snapshots/12/fs/public/game/server.js

/root/.nvm/versions/node/v22.23.1/lib/node_modules/pm2/modules/pm2-axon-rpc/lib/server.js

/root/.nvm/versions/node/v22.23.1/lib/node_modules/npm/node_modules/@npmcli/redact/lib/server.js

根据 find 的输出，你的无名杀实际上是以 Docker 容器（或 containerd 容器）的形式在后台运行的，因此文件直接存在于 /var/lib/docker/overlayfs/... 镜像存储层中。

直接删除那些 /var/lib/docker 里的底层路径是没有效果的，必须通过 Docker 命令停止并清理容器和镜像。请按以下步骤彻底卸载：

1
查找并停止正在运行的 Docker 容器
步骤 1
查看正在运行的容器列表，寻找无名杀对应的容器 ID 或名称：

Bash
docker ps
运行以下命令停止并彻底删除该容器（如果容器叫 noname，或换成对应 CONTAINER ID）：

Bash
docker stop noname && docker rm noname
如果不知道容器名称，可以批量停止并删除所有使用无名杀镜像的容器。

验证方法： 执行 docker ps，确认列表中已无相关容器。

2
删除 Docker 镜像
步骤 2
查看本地 Docker 镜像：

Bash
docker images
找到对应的无名杀镜像（或标签），执行删除（将 IMAGE_ID 替换为实际 ID）：

Bash
docker rmi <IMAGE_ID>
验证方法： 执行 docker images，确认对应镜像已被清理。

3
清理 Docker 悬空残留与卷（彻底释放空间）
步骤 3
删除未使用的容器、镜像层和网络，彻底释放 /var/lib/docker 占用的磁盘空间：

Bash
docker system prune -a --volumes -f
验证方法： 重新运行 find / -name "server.js" 2>/dev/null | grep game，确认 /var/lib/docker/overlayfs/ 下对应的路径全部消失。

注意事项
docker system prune -a --volumes 会清理所有未被使用的 Docker 镜像和容器。如果你这台服务器上还有其他正在运行或停止的 Docker 服务，请手动指定 docker rmi 卸载无名杀的镜像，避免误删其他服务的数据。

CONTAINER ID   IMAGE                             COMMAND                 CREATED        STATUS              PORTS                                                 NAMES

4473c05e0acd   firfe/noname:web-1.10.17.4.1      "/static-web-server"    3 months ago   Up 2 months         80/tcp, 0.0.0.0:9681->5161/tcp, [::]:9681->5161/tcp   noname_web

66af15bc3738   firfe/noname:server-1.10.17.4.1   "node game/server.js"   3 months ago   Up About a minute   0.0.0.0:9680->8080/tcp, [::]:9680->8080/tcp           noname_server

成功定位到了无名杀的 Docker 容器！你的服务器上运行着两个无名杀相关的容器：

noname_server（PID 1049 对应的服务端，镜像 firfe/noname:server-1.10.17.4.1）

noname_web（网页前端，镜像 firfe/noname:web-1.10.17.4.1）

请按照以下步骤彻底停止并删除它们：

1
停止并删除无名杀容器
步骤 1
直接使用容器名称停止并删除这两个容器：

Bash
docker stop noname_server noname_web && docker rm noname_server noname_web
验证方法： 运行 docker ps -a，确认列表中已无 noname_server 和 noname_web。

2
删除对应的 Docker 镜像
步骤 2
清理无名杀占用的本地镜像，释放磁盘空间：

Bash
docker rmi firfe/noname:server-1.10.17.4.1 firfe/noname:web-1.10.17.4.1
验证方法： 运行 docker images | grep firfe/noname，确认无任何相关镜像返回。

3
检查是否由 Docker Compose 部署（如有）
步骤 3
如果你最初是用 docker-compose 启动的，建议检查当前目录下或用户家目录（如 ~/noname）是否有 docker-compose.yml 文件。如果有，直接进入该目录并清理项目组：

Bash
docker compose down -v
验证方法： 执行 find / -name "docker-compose.yml" 2>/dev/null，确认没有残留的无名杀配置文件。

root@server-xyz:~# find / -name "docker-compose.yml" 2>/dev/null

/var/www/wum/docker-compose.yml

已找到配置文件 /var/www/wum/docker-compose.yml。使用 docker compose 一键停止服务、清理容器以及移除卷是最高效、最彻底的卸载方式。

请按以下步骤完成最后的清理：

1
使用 Compose 清理服务与挂载卷
步骤 1
进入项目所在目录，运行 Compose 卸载指令（-v 参数会同时删除该项目关联的存储卷）：

Bash
cd /var/www/wum && docker compose down -v
如果提示 docker compose 未找到，请尝试旧版语法：docker-compose down -v

验证方法： 运行 docker ps -a，确认 noname_server 和 noname_web 均已消失。

2
删除配置文件与部署目录
步骤 2
返回上级目录并删除整个 /var/www/wum 文件夹：

Bash
cd /var/www && rm -rf /var/www/wum
验证方法： 执行 ls /var/www/wum，系统提示 No such file or directory。

3
删除本地无名杀镜像
步骤 3
清理对应的容器镜像以彻底释放磁盘空间：

Bash
docker rmi firfe/noname:server-1.10.17.4.1 firfe/noname:web-1.10.17.4.1
验证方法： 运行 docker images | grep firfe，无任何输出。
