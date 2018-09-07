# Proxy Setting

## Linux -- Ubuntu

### Interactive Shell (Bash)

```bash
$ cat .bashrc

# Added by WY @ 2018-08-30
export http_proxy=http://127.0.0.1:63128
export https_proxy=$http_proxy
export HTTP_PROXY=$http_proxy
export HTTPS_PROXY=$http_proxy
export NO_PROXY="localhost,127.0.0.1,::1,localaddress,.localdomain.com,.xxx-xxxx.com,.xxx.com"

```

### System level Proxy setting

For **ubuntu**, especiall when running inside **VirtualBox** behind company firewall, add the following into `/etc/environment`

```bash
$ cat /etc/environment 
...
http_proxy="http://127.0.0.1:63128"
https_proxy="http://127.0.0.1:63128"
no_proxy="localhost,127.0.0.1,::1,localaddress,.localdomain.com"
# Duplicate both upper-case and lower-case because some programs only look for one or the other
HTTP_PROXY="http://127.0.0.1:63128"
HTTPS_PROXY="http://127.0.0.1:63128"
NO_PROXY="localhost,127.0.0.1,::1,localaddress,.localdomain.com"
...
```

To enable browser (FireFox, Chrome) to connect to internet, change their Proxy Setting to : `Use system proxy settings`

### `apt`

```bash
$ cat /etc/apt/apt.conf
Acquire::http::proxy   "http://xxx.com:8080";
Acquire::https::proxy  "http://xxx.com:8080";
```

## Python

### Anaconda

Create `.condarc` under C:/Users/<user>/

```bash
proxy_servers:
    http: http://127.0.0.1:63128
    https: http://127.0.0.1:63128

ssl_verify: False
```

### `pip`

Install `pip`
```bash
python get-pip.py  --proxy http://127.0.0.1:63128  --trusted-host  pypi.python.org
```

Install package using `pip`
```bash

pip install Django==2.0.5  --proxy http://127.0.0.1:63128  --trusted-host  pypi.python.org
pip install pandas==0.22.0  --proxy http://127.0.0.1:63128  
pip install pyodbc==4.0.23  --proxy http://127.0.0.1:63128  
pip install django-pyodbc-azure==2.0.4.1  --proxy http://127.0.0.1:63128  
pip install SQLAlchemy==1.2.7  --proxy http://127.0.0.1:63128  
pip install cx-Oracle==6.2.1  --proxy http://127.0.0.1:63128  
pip install networkx==2.1  --proxy http://127.0.0.1:63128 
pip install sortedcontainers==1.5.10  --proxy http://127.0.0.1:63128  

pip install django-cors-headers  --proxy http://127.0.0.1:63128  
pip install pulp  --proxy http://127.0.0.1:63128  
pip install xlrd   --proxy http://127.0.0.1:63128 

pip install spyder==3.2.8   --proxy http://127.0.0.1:63128 

pip install --upgrade pip   --proxy http://127.0.0.1:63128 
```

## GIT

```bash
git clone https://github.com/skyformat99/30dayMakeOS.git   --config "http.proxy=http://127.0.0.1:63128"
```


## `curl` downloading

```bash
$ curl -O http://releases.ubuntu.com/18.04/ubuntu-18.04.1-desktop-amd64.iso -x http://127.0.0.1:63128
```

## Misc

Ref: <http://cloudate.net/?p=1033> 

新公司的网络环境真是不可恭维，上网还需要设置代理，于是，在使用Ubuntu下各种程序的时候都遇到了代理问题，现在总结一下，做个记录，供以后遇到同样的问题时进行查阅。
 
Firefox
1. 点击右上角的三条横线图标，也就是设置图标，或者菜单->编辑， 然后点击设置菜单。
2. 接下来依次点击高级->网络->设置。
3. 在设置窗口的选项卡上，选择手工配置代理，输入代理，并且选择为所有协议使用这个代理。
4. 最后，在选项卡最下面跳过代理段增加内网ip和域名等等，可以用*进行通配符，这些配置的ip和域名将跳过代理直接进行连接。
 
Chrome
1. 点击右上角的三条横线图标，也就是设置图标，或者菜单->编辑, 然后点击设置菜单。
2. 在设置页面滚动到最底端，然后点击显示高级设置。
3. 在高级设置的网络一栏点击改变代理设置按钮。
4. 这将会调用Ubuntu系统代理配置对话框。
5. 这里请参考下面系统代理配置段进行配置。
 
系统代理配置
1. 按开始按键（键盘上Widnows的开始按键），搜索Proxy，然后，点击网络应用程序图标。
2. 在左边的列表框选择网络代理，在右边配置代理设置，请注意，这里需要为所有协议进行配置相应的代理。
 
命令行代理配置

```bash
export http_proxy=http://ip:port
```
 
APT-GET代理配置
在Ubuntu中，更新软件apt-get并不使用系统的代理或者命令行的代理，而是需要进行单独配置。
如果你想通过代理来访问apt-get上的源，可以修改或者添加一个/etc/apt/apt.conf文件

```bash
sudo vi /etc/apt/apt.conf
```

然后在文件中添加内容如下：

```bash
Acquire::http::proxy "http://ip:port/";
Acquire::ftp::proxy "ftp://ip:port/";
Acquire::https::proxy "https://ip:port/"; 
```

这样就可以使用代理服务器访问apt-get上的源了。
要想编辑源的列表，可以修改
```bash
$ cat /etc/apt/sources.list
deb http://cn.archive.ubuntu.com/ubuntu/ raring main restricted
deb-src http://cn.archive.ubuntu.com/ubuntu/ raring main restricted 
```

MAVEN代理配置
如果你是一个Java开发人员，你一定也需要配置maven的代理，从而才可以进行maven项目的编译打包工作，因为maven需要访问maven的远程repository来下载各种构建库，maven的配置文件可以在`$MAVEN_HOME/conf`下或者在`$HOME/.m2/`下，在这些目录下找到`settings.xml`，打开后在最后添加如下代理配置：
 
```xml
<proxies>
    <proxy>
        <active>true</active>
        <protocol>http</protocol>
        <host>10.199.75.12</host>
        <port>8080</port>
        <nonProxyHosts>10.*</nonProxyHosts>
    </proxy>
</proxies>
```
 
Eclipse代理配置

当你配置了系统代理，系统代理设置请参考前面段落，Eclipse可以自动识别这些配置，如果没有识别或者你想对Eclipse使用的代理做定制化的更改，请打开Eclipse，打开窗口 -> 配置， 然后依次打开通用->网络连接来设置代理。

在Ubuntu14.04中，Eclipse自动识别native的代理多了一个socket配置，使用内网的SVN和GIT不能访问服务器，需要进行手工配置，请参考下图:

由于自动识别的native代理无法配置跳过代理设置，也请注意，如果你的svn和git使用内网的域名，请把域名放入跳过代理的配置，如果不是域名，请把ip放入跳过代理的配置中。

在Ubuntu13.04不存在这个问题，使用自动配识别的native配置即可。



```bash

```


```bash

```
