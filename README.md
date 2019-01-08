# Through the wall 穿墙而过

## 搬瓦工VPS
* [BandwagonHost](https://bwh8.net/clientarea.php?action=products)
* [KiwiVM控制面板](https://kiwivm.64clouds.com/main.php)
* [KiwiVM控制面板-Shadowsocks Server](https://kiwivm.64clouds.com/main-exec.php?mode=extras_shadowsocks)
* [非官方网站](http://banwagong.cn)

## CentOS7

### 创建SOCKS代理
1. 安装最新版Shadowsocks
```bash
sudo pip3 install https://github.com/shadowsocks/shadowsocks/archive/master.zip
```

2. 查看Shadowsocks版本
```bash
ssserver --version
Shadowsocks 3.0.0
```

3. 编写Shadowsocks的配置文件
```bash
sudo nano /etc/shadowsocks/shadowsocks.json
```
```json
{
    "server":"255.255.255.255",
    "server_port":8888,
    "local_address": "127.0.0.1",
    "local_port":1080,
    "password":"12345678",
    "timeout":300,
    "method":"aes-256-cfb",
    "fast_open": false,
    "workers": 1
}
```
> 通过访问[KiwiVM控制面板-Shadowsocks Server](https://kiwivm.64clouds.com/main-exec.php?mode=extras_shadowsocks)，把255.255.255.255换成Server IP的内容；把8888换成Server Port的内容；把12345678换成Password的内容。

> ![](https://kiwivm.64clouds.com/img/shadowsocks_settings.png)

4. 创建shadowsocks服务
```bash
sudo nano /etc/systemd/system/shadowsocks.service
```
```txt
[Unit]
Description=Shadowsocks

[Service]
TimeoutStartSec=0
ExecStart=/usr/bin/sslocal -c /etc/shadowsocks/shadowsocks.json

[Install]
WantedBy=multi-user.target
```

5. 启动shadowsocks服务
```bash
sudo systemctl start shadowsocks
```

6. 测试socks5代理
```bash
curl --socks5 127.0.0.1:1080 http://httpbin.org/ip
```
对应地应该显示你的Server IP
```json
{
  "origin": "xxx.xxx.xxx.xxx"
}
```

7. 设置shadowsocks服务开机自启动
```bash
sudo systemctl enable shadowsocks
```

### 创建HTTP代理
1. 安装Privoxy
```bash
sudo yum install privoxy -y
```

2. 编写Privoxy的配置文件
```bash
sudo nano /etc/privoxy/config
```
> 需要在配置中查找、修改和增加
```txt
listen-address 127.0.0.1:8118 # 8118 是默认端口，不用改
forward-socks5t / 127.0.0.1:1080 . #转发到本地端口，注意最后有个点
```

3. 设置系统环境变量
```bash
sudo nano /etc/profile
```
```txt
PROXY_HOST=127.0.0.1
export all_proxy=http://$PROXY_HOST:8118
export ftp_proxy=http://$PROXY_HOST:8118
export http_proxy=http://$PROXY_HOST:8118
export https_proxy=http://$PROXY_HOST:8118
export no_proxy=localhost,172.16.0.0/16,192.168.0.0/16.,127.0.0.1,10.10.0.0/16
```
```bash
source /etc/profile
```

4. 测试http代理
```bash
curl -I www.google.com
```

5. 设置privoxy服务开机自启动
```bash
sudo systemctl enable privoxy
```

### 防火墙开放端口设置（这个忘记了是不是必须配置的）
1. 开启防火墙服务
```bash
sudo systemctl start firewalld
```

2. 开放端口
```bash
sudo firewall-cmd --permanent --add-port=18381-18383/tcp
```

3. 重新加载防火墙规则
```bash
sudo firewall-cmd --reload
```
