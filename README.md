# Through the wall 穿墙而过

## 搬瓦工VPS
* [BandwagonHost](https://bwh88.net/clientarea.php?action=products)
* [KiwiVM控制面板](https://kiwivm.64clouds.com/main.php)
* [KiwiVM控制面板-Shadowsocks Server](https://kiwivm.64clouds.com/main-exec.php?mode=extras_shadowsocks)
* [KiwiVM控制面板-ShadowsocksR Server](https://kiwivm.64clouds.com/main-exec.php?mode=extras_shadowsocksr)
* [Just My Socks](https://justmysocks.net/members/)
* [Block List Check](https://kiwivm.64clouds.com/main-exec.php?mode=blacklistcheck)
* [非官方网站](http://banwagong.cn)

## Linux

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

    * CentOS7
    ```txt
    [Unit]
    Description=Shadowsocks

    [Service]
    TimeoutStartSec=0
    ExecStart=/usr/bin/sslocal -c /etc/shadowsocks/shadowsocks.json

    [Install]
    WantedBy=multi-user.target
    ```
    
    * Ubuntu18
    ```txt
    [Unit]
    Description=Shadowsocks

    [Service]
    TimeoutStartSec=0
    ExecStart=/usr/local/bin/sslocal -c /etc/shadowsocks/shadowsocks.json

    [Install]
    WantedBy=multi-user.target
    ```
    
    * **如果启动服务后重新编辑了这个文件需要运行下面的命令**
    ```bash
    sudo systemctl daemon-reload
    sudo systemctl start shadowsocks
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
1. 安装[Privoxy](http://www.privoxy.org/)

    * CentOS7
    ```bash
    sudo yum install privoxy -y
    ```

    * Ubuntu18
    ```bash
    sudo apt-get install privoxy -y
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

### 浏览器Chrome代理[SwitchyOmega](https://www.switchyomega.com/)
1. 下载[SwitchyOmega](https://github.com/FelisCatus/SwitchyOmega/releases)

2. 安装SwitchyOmega。打开浏览器的扩展程序，拖拽SwitchyOmega_Chromium.crx到扩展程序页面。
> 当出现错误：CRX_HEADER_INVALID时，把SwitchyOmega_Chromium.crx改名为SwitchyOmega_Chromium.zip，解压后，打开浏览器的扩展程序，选择加载已解压的扩展程序，选择刚才解压后的目录。

3. 配置代理。设置代理协议：SOCKS5，代理服务器：127.0.0.1，代理端口：1080。

### 参考资料
* [CentOS 7 安装使用Shadowsocks客户端](https://www.jianshu.com/p/824912d9afda)
* [Centos7 切换阿里云yum源 安装shadowsocks](https://www.jianshu.com/p/5ea81488a9ca)
* [Ubuntu 16.04下Shadowsocks服务器端安装及优化](https://www.polarxiong.com/archives/Ubuntu-16-04%E4%B8%8BShadowsocks%E6%9C%8D%E5%8A%A1%E5%99%A8%E7%AB%AF%E5%AE%89%E8%A3%85%E5%8F%8A%E4%BC%98%E5%8C%96.html)
* [使用 privoxy 转发 socks 到 http](http://einverne.github.io/post/2018/03/privoxy-forward-socks-to-http.html)
* [SSH(Putty)远程连接VPS服务器](https://www.jianshu.com/p/132f09cb4577)
* [linux systemctl 命令](https://www.cnblogs.com/sparkdev/p/8472711.html)
* [Proxy SwitchyOmega](https://proxy-switchyomega.com)
* [【CRX_HEADER_INVALID 】Unable to install from the crx file](https://github.com/FelisCatus/SwitchyOmega/issues/1766)
