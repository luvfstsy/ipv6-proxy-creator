如果想从头重新编译并设置 `ipv6-proxy-creator`，你需要按照以下步骤操作。请注意，直接用 `git@github.com` 地址需要配置 SSH 密钥，建议使用 HTTPS 地址进行下载。

---

### 从头重新编译和安装的步骤

#### 1. **清理旧的安装**
如果之前的安装存在问题，建议先清理旧的配置：
```bash
rm -rf /home/proxy-installer
rm -rf /usr/local/etc/3proxy
```

---

#### 2. **下载 `ipv6-proxy-creator`**
推荐使用 HTTPS 地址下载：
```bash
yum install git -y   # 如果没有安装 git
git clone https://github.com/rafaelb128/ipv6-proxy-creator.git
cd ipv6-proxy-creator
```

---

#### 3. **检查和安装必要依赖**
确保你有以下工具和依赖：
```bash
yum install gcc net-tools bsdtar zip make -y
```

---

#### 4. **编译和安装 `3proxy`**
手动下载并编译最新版本的 `3proxy`：

```bash
wget https://github.com/z3APA3A/3proxy/archive/refs/tags/0.8.13.tar.gz
tar -xvzf 0.8.13.tar.gz
cd 3proxy-0.8.13
make -f Makefile.Linux
mkdir -p /usr/local/etc/3proxy/bin
cp src/3proxy /usr/local/etc/3proxy/bin/
```

验证是否成功安装：
```bash
/usr/local/etc/3proxy/bin/3proxy -v
```

---

#### 5. **配置 `ipv6-proxy-creator`**
进入项目目录（cd ipv6-proxy-creator），运行安装脚本：
```bash
bash install.sh
```

#### 6. **生成代理**
安装完成后，脚本会询问你要生成多少代理，输入例如 `500`：
```bash
How many proxy do you want to create? Example 500
500
```

这会生成代理列表和相关配置文件。如果过程中遇到 `SIOGIFINDEX: No such device` 错误，请根据以下步骤解决。

---

### 解决 `SIOGIFINDEX: No such device`

`SIOGIFINDEX` 错误表示脚本尝试在错误的网络接口上绑定 IPv6 地址。你需要手动修改脚本以使用正确的网络接口。

#### 1. **检查你的网络接口名称**
运行以下命令查看服务器的网络接口：
```bash
ip link show
```
示例输出：
```
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 state UNKNOWN qlen 1000
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 state UP qlen 1000
```

网络接口可能是 `eth0`、`enp1s0` 或其他名称。

#### 2. **修改脚本**
打开生成 IPv6 地址的脚本（通常是 `boot_ifconfig.sh` 或 `install.sh`）：
```bash
nano install.sh
```

找到配置网络接口的地方，将 `enp1s0` 替换为你的网络接口名称，例如 `eth0`。

保存修改后，重新运行安装脚本：
```bash
bash install.sh
```

---

### 7. **验证代理是否正常运行**
完成安装后，使用以下命令检查代理端口是否监听：
```bash
netstat -tuln | grep 10000
```

如果显示端口正在监听，说明代理服务启动正常。

---

### 8. **测试代理**
尝试使用代理测试：
```bash
curl -x http://username:password@IPv4_or_IPv6:port http://google.com
```

如果需要进一步帮助，请随时告知问题或错误的具体输出！
