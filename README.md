# Through the wall 穿墙而过

## 搬瓦工VPS
* [BandwagonHost](https://bwh8.net/clientarea.php?action=products)
* [KiwiVM控制面板](https://kiwivm.64clouds.com/main.php)
* [KiwiVM控制面板-Shadowsocks Server](https://kiwivm.64clouds.com/main-exec.php?mode=extras_shadowsocks)
* [非官方网站](http://banwagong.cn)

## CentOS7
* 安装最新版Shadowsocks
```bash
sudo pip3 install https://github.com/shadowsocks/shadowsocks/archive/master.zip
```

* 查看Shadowsocks版本
```bash
ssserver --version
Shadowsocks 3.0.0
```

* 编写Shadowsocks的配置文件
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

