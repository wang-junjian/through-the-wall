# Through the wall 穿墙而过

## 搬瓦工VPS
* [BandwagonHost](https://bwh8.net/clientarea.php?action=products)
* [KiwiVM控制面板](https://kiwivm.64clouds.com/main.php)
* [KiwiVM控制面板-Shadowsocks Server](https://kiwivm.64clouds.com/main-exec.php?mode=extras_shadowsocks)
* [非官方网站](http://banwagong.cn)

## CentOS7
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
