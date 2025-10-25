---
private: false
featured: false
title: wifi_bridge
authors:
  - admin
tags:
  - 计算机网络
categories:
  - tech
summary:
---
{{< toc mobile_only=true is_open=true >}}

要实现物联网设备（C）通过Linux设备（B）的**AP+STA双模**接入校园网（A），核心是让Linux设备同时扮演两个角色：  
- **STA角色**：作为客户端，连接校园网AP（A），获取校园网访问权限；  
- **AP角色**：作为热点，让物联网设备（C）连接，再通过Linux设备的网络转发功能，将C的流量转发到校园网（A）。  


### 一、前置条件检查
在开始配置前，需确保Linux设备（B）满足以下条件，否则会导致配置失败：
1. **无线网卡支持AP模式**：  
   执行命令 `iw list` 查看无线网卡功能，若输出中包含 `AP`（如下方示例），则支持AP模式；若没有，则需更换支持AP模式的无线网卡（如USB无线网卡）。  
   ```bash
   supported interface modes:
             * IBSS
             * managed  # STA模式（客户端）
             * AP       # 支持AP模式（热点）
             * AP/VLAN
             * monitor
   ```
2. **管理员权限（root）**：  
   所有网络配置（如修改接口、启动服务）需root权限，执行 `sudo -i` 切换至root用户。
3. **必要工具安装**：  
   需安装3个核心工具，不同Linux发行版（如Ubuntu/Debian、CentOS/RHEL）安装命令不同：

| 工具用途               | Ubuntu/Debian 安装命令              | CentOS/RHEL 安装命令                |
| ------------------ | ------------------------------- | ------------------------------- |
| 管理AP模式（热点）         | `apt install hostapd`           | `yum install hostapd`           |
| 提供DHCP/DNS（给C分配IP） | `apt install dnsmasq`           | `yum install dnsmasq`           |
| 无线接口配置             | `apt install wireless-tools iw` | `yum install wireless-tools iw` |


### 二、分步配置（核心步骤）
假设Linux设备的无线网卡名为 `wlan0`（可通过 `ip addr` 查看，可能是 `wlp2s0` 等），配置分为4个关键环节：**STA模式（连校园网）→ AP模式（发热点）→ 网络转发 → 测试连通性**。


#### 环节1：配置STA模式（Linux设备B连接校园网A）
STA模式是让B作为“客户端”，连接校园网的AP（A）。校园网常见认证方式分两种，需针对性配置：


##### 情况1：校园网是简单密码认证（WPA2-PSK，如家用WiFi）
通过 `wpa_supplicant` 或系统网络管理工具（如NetworkManager）配置：  
1. 创建wpa_supplicant配置文件（如 `/etc/wpa_supplicant/wpa_supplicant-wlan0.conf`）：  
   ```conf
   ctrl_interface=/var/run/wpa_supplicant
   update_config=1
   network={
       ssid="校园网AP名称（A的SSID）"  # 替换为实际校园网名称（如“Campus-WiFi”）
       psk="校园网密码"                # 替换为实际密码
       key_mgmt=WPA-PSK               # 认证方式（WPA2-PSK填WPA-PSK即可）
       proto=RSN                      # 支持WPA2
       pairwise=TKIP                  # 加密算法
       group=TKIP
   }
   ```
2. 启动STA连接：  
   ```bash
   wpa_supplicant -B -i wlan0 -c /etc/wpa_supplicant/wpa_supplicant-wlan0.conf
   ```
3. 给STA接口（wlan0）获取IP（若校园网用DHCP）：  
   ```bash
   dhclient wlan0
   ```
4. 验证是否连上网：  
   执行 `ping www.baidu.com`，若能通，说明B已成功接入校园网A。


##### 情况2：校园网是企业级认证（WPA2-Enterprise，常见于高校）
校园网若需“账号+密码”认证（如802.1X），需修改wpa_supplicant配置文件，示例如下（需替换实际账号密码）：  
```conf
ctrl_interface=/var/run/wpa_supplicant
update_config=1
network={
    ssid="校园网AP名称（如“Campus-Enterprise”）"
    key_mgmt=WPA-EAP                 # 企业级认证
    eap=PEAP                         # 认证协议（校园网可能是PEAP或TTLS，需确认）
    phase2="auth=MSCHAPV2"           # 二次认证（常见MSCHAPV2）
    identity="校园网账号（如学号）"    # 替换为你的校园网账号
    password="校园网密码"             # 替换为你的密码
}
```
后续启动和验证步骤与“情况1”一致。


#### 环节2：配置AP模式（Linux设备B作为热点，供C连接）
AP模式是让B作为“热点”，物联网设备（C）通过WiFi连接B。需配置两个核心文件：`hostapd`（热点参数）和 `dnsmasq`（给C分配IP）。


##### 步骤1：创建AP虚拟接口（关键！避免与STA冲突）
多数无线网卡**不支持同一物理接口（如wlan0）同时运行STA和AP模式**，因此需为AP模式创建一个**虚拟接口**（如 `wlan0-ap`）：  
```bash
# 先关闭wlan0的现有连接（避免冲突）
iw dev wlan0 disconnect
# 创建AP虚拟接口（类型为__ap）
iw dev wlan0 interface add wlan0-ap type __ap
# 启用虚拟接口
ip link set wlan0-ap up
```
执行 `ip addr` 可看到新增的 `wlan0-ap` 接口，后续AP模式用此接口。
**设置开机自启**
创建添加`wlan0-ap`接口的服务
```bash
sudo nano /etc/systemd/system/create-wlan0-ap.service
```
加入以下内容：
```ini
[Unit]
Description=Create wlan0-ap virtual interface for AP mode
After=network.target
Before=hostapd.service

[Service]
Type=oneshot
ExecStart=/bin/bash -c 'if iw dev | grep -q "wlan0-ap"; then iw dev wlan0-ap del; fi; \
    iw dev wlan0 interface add wlan0-ap type __ap; \
    ip link set wlan0-ap up; \
    ip addr add 192.168.4.1/24 dev wlan0-ap'
RemainAfterExit=yes

[Install]
WantedBy=multi-user.target
```
添加该服务
```bash
sudo systemctl enable create-wlan0-ap.service
```

##### 步骤2：配置hostapd（热点参数）
1. 创建hostapd配置文件（如 `/etc/hostapd/hostapd-wlan0-ap.conf`），需注意：  
   - 物联网设备（C）多只支持2.4GHz频段，建议优先配置2.4GHz（信道选1/6/11，避免干扰）；  
   - 若C支持5GHz，可改为5GHz频段（需确认网卡支持）。  
   ```conf
   #使用时去除所有注释及每行末尾的空格，否则解析错误
   interface=wlan0-ap               # AP模式用的虚拟接口
   driver=nl80211                   # 驱动类型（默认nl80211）
   ssid=Linux-AP                  # 热点名称（C要连接的WiFi名称，可自定义）
   hw_mode=g                        # 频段：g=2.4GHz，a=5GHz
   channel=6                        # 2.4GHz信道（1/6/11无重叠，选一个）
   wmm_enabled=0                    # 关闭QoS（简化配置）
   macaddr_acl=0                    # 允许所有MAC地址连接
   auth_algs=1                      # 允许开放系统认证
   ignore_broadcast_ssid=0          # 不隐藏SSID（方便C查找）
   wpa=2                            # 仅支持WPA2
   wpa_passphrase=12345678          # 热点密码（至少8位，C连接时用）
   wpa_key_mgmt=WPA-PSK             # 认证方式（简单密码）
   wpa_pairwise=TKIP                # 加密算法
   rsn_pairwise=CCMP
   ```
2. 测试hostapd是否能启动（验证配置文件无错误）：  
   ```bash
   hostapd /etc/hostapd/hostapd-wlan0-ap.conf
   ```
   若输出类似 `AP-ENABLED`，说明配置正确；若报错（如“invalid channel”），需检查频段/信道是否支持（用 `iw list` 看网卡支持的信道）。
**设置开机自启**
编辑 `/etc/default/hostapd` 文件
```ini
DAEMON_CONF="/etc/hostapd/hostapd-wlan0-ap.conf"  # 指向你的自定义配置
```
确保hostapd在接口创建后启动
编辑
```bash
sudo systemctl edit hostapd.service
```
添加
```ini
[Unit]
# 要求create-wlan0-ap.service必须先成功启动
Requires=create-wlan0-ap.service
# 确保hostapd在create-wlan0-ap之后启动
After=create-wlan0-ap.service
```


##### 步骤3：配置dnsmasq（给C分配IP）
物联网设备（C）连接B的热点后，需通过DHCP获取IP，dnsmasq负责提供DHCP和DNS服务：  
1. 备份默认dnsmasq配置（避免冲突）：  
   ```bash
   cp /etc/dnsmasq.conf /etc/dnsmasq.conf.bak
   ```
2. 创建新配置文件（`/etc/dnsmasq.conf`）：  
   ```conf
   interface=wlan0-ap               # 仅在AP接口提供服务
   dhcp-range=192.168.4.2,192.168.4.20,255.255.255.0,24h  # 分配IP段（给C）
   dhcp-option=3,192.168.4.1        # 网关（C的网关指向B的AP接口IP）
   dhcp-option=6,8.8.8.8,114.114.114.114  # DNS服务器（用公共DNS或校园网DNS）
   server=8.8.8.8                   # 转发DNS请求到谷歌DNS
   log-queries                      # 日志记录（方便排查问题）
   log-dhcp                         # 记录DHCP分配情况
   ```
3. 给B的AP接口（wlan0-ap）设置静态IP（需与上述网关一致）：  
   ```bash
   ip addr add 192.168.4.1/24 dev wlan0-ap
   ```
4. dnsmasq使用端口53，通常被系统自带的DNS服务（如 `systemd-resolved`）占用，导致 `dnsmasq` 无法使用该端口

如果 `systemd-resolved` 占用，需要修改其配置，让它释放端口53：

1. 编辑 `systemd-resolved` 配置文件：
   ```bash
   sudo nano /etc/systemd/resolved.conf
   ```

2. 在文件中添加/修改以下内容（禁用其对端口53的监听）：
```ini
   [Resolve]
   DNSStubListener=no  # 关键：关闭端口53的监听
```

3. 保存文件（`Ctrl+O` 确认，`Ctrl+X` 退出），然后重启 `systemd-resolved`：
   ```bash
   sudo systemctl restart systemd-resolved
   ```

4. 验证端口53是否已释放：
   ```bash
   sudo lsof -i :53  # 此时应无输出，说明端口已释放
   ```


5. 重启 `dnsmasq` 服务
```bash
sudo systemctl restart dnsmasq
```

6. 验证 `dnsmasq` 是否启动成功
```bash
sudo systemctl status dnsmasq
```

如果输出中显示 `Active: active (running)`，说明启动成功；若仍失败，重复步骤1，检查是否有其他进程（如 `bind9`、`named` 等）占用端口53，停止对应的服务即可。


#### 环节3：配置网络转发（让C的流量通过B到A）
Linux默认关闭IP转发，需手动开启，并配置防火墙（iptables）做NAT转发，实现“C→B→A”的流量通路。


##### 步骤1：开启IP转发（永久生效）
1. 临时开启（立即生效，重启后失效）：  
   ```bash
   echo 1 > /proc/sys/net/ipv4/ip_forward
   ```
2. 永久开启（重启后仍生效）：  
   编辑 `/etc/sysctl.conf`，确保以下行无注释（若没有则添加）：  
   ```conf
   net.ipv4.ip_forward=1
   ```
   执行 `sysctl -p` 让配置生效。


##### 步骤2：配置iptables（NAT转发规则）
如果是ubuntu22.04，默认使用的是nf_tables
切换为传统iptables
```bash
update-alternatives --set iptables /usr/sbin/iptables-legacy
update-alternatives --set ip6tables /usr/sbin/ip6tables-legacy
```

通过iptables将AP接口（wlan0-ap）的流量转发到STA接口（wlan0，已连校园网），并允许双向通信：  
```bash
# 1. 清空现有规则（可选，避免旧规则干扰）
iptables -F
iptables -t nat -F

# 2. 允许AP接口（wlan0-ap）与STA接口（wlan0）之间的转发
iptables -A FORWARD -i wlan0-ap -o wlan0 -j ACCEPT
iptables -A FORWARD -o wlan0-ap -i wlan0 -j ACCEPT

# 3. 配置NAT（将C的私网IP转换为B的校园网IP，实现访问外网）
iptables -t nat -A POSTROUTING -o wlan0 -j MASQUERADE

# 4. 保存规则（避免重启后丢失，Ubuntu需安装iptables-persistent）
# Ubuntu/Debian：
apt install iptables-persistent
netfilter-persistent save
# CentOS/RHEL：
service iptables save
```


#### 环节4：启动服务并测试连通性
1. 后台启动hostapd和dnsmasq服务：  
   ```bash
   # 启动hostapd（用自定义配置文件）
   hostapd -B /etc/hostapd/hostapd-wlan0-ap.conf
   # 启动dnsmasq
   systemctl start dnsmasq
   # 设置开机自启（可选）
   systemctl enable hostapd
   systemctl enable dnsmasq
   ```

2. 测试物联网设备（C）的连通性：  
   - 让C连接B的热点（SSID：`Linux-AP`，密码：`12345678`）；  
   - 查看C是否获取到IP（应在 `192.168.4.2~20` 范围内）；  
   - 在C上执行 `ping www.baidu.com` 或 `ping 校园网网关`，若能通，说明配置成功！


### 三、常见问题排查
若配置后C无法联网，按以下步骤排查：
1. **hostapd启动失败**：  
   - 报错“invalid interface”：检查虚拟接口 `wlan0-ap` 是否存在（`ip addr`），重新执行 `iw dev wlan0 interface add wlan0-ap type __ap`；  
   - 报错“channel not allowed”：用 `iw list` 查看网卡支持的信道，修改hostapd配置中的 `channel`。

2. **C无法获取IP**：  
   - 检查dnsmasq是否启动（`systemctl status dnsmasq`），查看日志（`cat /var/log/syslog | grep dnsmasq`）；  
   - 确认AP接口 `wlan0-ap` 的IP是 `192.168.4.1`（`ip addr`）。

3. **C能连热点但无法ping外网**：  
   - 检查B的STA接口（wlan0）是否能联网（`ping www.baidu.com`）；  
   - 检查IP转发是否开启（`cat /proc/sys/net/ipv4/ip_forward`，应输出1）；  
   - 检查iptables规则（`iptables -t nat -L`，确认POSTROUTING规则存在）。

4. **无线网卡不支持同时STA和AP**：  
   - 若执行 `iw dev wlan0 interface add wlan0-ap type __ap` 报错，说明网卡不支持“单卡双模”，需添加一块额外的无线网卡（如USB网卡）：一块用于STA（连校园网），另一块用于AP（发热点），配置时分别用不同的接口名（如 `wlan0` 做STA，`wlan1` 做AP）。


### 四、优化建议
1. **固定接口名称**：Linux网卡名称可能随重启变化（如 `wlp2s0` 变 `wlp3s0`），可通过 `/etc/udev/rules.d/70-persistent-net.rules` 固定接口名。  
2. **校园网DHCP租期**：若校园网IP租期短，可配置 `dhclient` 自动续约（无需手动操作）。  
3. **低功耗适配**：若C是低功耗物联网设备（如ESP8266），建议将B的热点信道固定为1/6/11，减少C的连接功耗。