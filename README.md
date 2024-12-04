# V2Board 安装步骤

## 1. 配置 aaPanel

你需要在 aaPanel 选择你的系统获得安装方式。这里以 CentOS 7+ 作为系统环境进行安装。

官方建议使用 CentOS 7+ 安装aaPanel，其他系统可能会有未知问题（其实应该没啥问题）。

```bash
yum install -y wget && wget -O install.sh http://www.aapanel.com/script/install_6.0_en.sh && bash install.sh
```
也可以去这里寻找对应版本的宝塔开心版：https://www.hostcli.com/

安装完成后我们登陆 aaPanel 进行环境的安装，选择使用 LNMP 环境安装方式，并勾选以下内容：

☑️ Nginx 1.17

☑️ MySQL 5.6

☑️ PHP 7.4

选择 "Fast" 快速编译后进行安装。

<br>

## 2. 安装 Redis、fileinfo
通过 aaPanel 面板 > App Store（软件商店） > 找到 PHP 7.4，点击 "Setting（设置）" > "Install extensions（安装扩展）"

安装 redis 和 fileinfo 扩展。

<br>

## 3. 解除被禁止的函数
通过 aaPanel 面板 > App Store > 找到 PHP 7.4，点击 "Setting（设置）" > "Disabled functions（禁用函数）" 将以下函数从列表中删除：

putenv

proc_open

pcntl_alarm

pcntl_signal


<br>

## 4. 添加站点
通过 aaPanel 面板 > "Website（网站）" > "Add site（添加站点）" 添加站点：

在 "Domain（域名）" 填入你指向服务器的域名

在 "Database（数据库）" 选择 MySQL

在 "PHP Version" 选择 PHP 7.4


<br>

## 5. 安装 V2Board（xiao版本）
通过 SSH 登录到服务器后，访问站点路径，如：/www/wwwroot/你的站点域名。
```bash
cd /www/wwwroot/你的站点域名
```

然后执行以下命令：
```bash
git clone https://github.com/wyx2685/v2board.git ./
```

安装 V2Board：

```bash
sh init.sh
```

<br>

## 6. 配置站点目录及伪静态
添加站点后，编辑站点设置：

Site directory(网站目录) > Running directory（运行目录）: 选择 /public 并保存。

URL rewrite 填入以下伪静态信息：
```bash
location /downloads {
}

location / {  
    try_files $uri $uri/ /index.php$is_args$query_string;  
}

location ~ .*\.(js|css)?$ {
    expires 1h;
    error_log off;
    access_log /dev/null; 
}

```

<br>

## 7. 配置定时任务
通过 aaPanel 面板配置定时任务：

在 "Cron" 中选择：
Type of Task（任务类型）: 选择 "Shell Script（Shell脚本）"

Name of Task（任务名称）: 填写 "v2board"

Period（执行周期）: 选择 "N Minutes" 和 1 分钟

Script content（脚本内容）: 填写以下内容：

```bash
php /www/wwwroot/你的站点域名/artisan schedule:run
```

<br>

## 8. 启动队列服务
V2Board 强依赖队列服务，必须启动队列服务才能正常使用。以下是使用 aaPanel 中的 Supervisor 守护队列服务的方法：

在 aaPanel 面板中，选择 "App Store（应用商店）" > "Tools（系统工具）"。

找到 "Supervisor（Supervisor管理器）" 并进行安装。

安装完成后，点击设置 > "Add Daemon（添加守护进程）" 填写以下信息：

Name: 填写 "V2board"

Run User（启动用户）: 选择 "www"

Run Dir（运行目录）: 选择站点目录

Start Command（启动命令）: 填写：


```bash
php artisan horizon
```

Processes: 填写 "1"
填写后点击 "Confirm（确定）" 添加即可运行。

<br>

本教程仅供自用，有问题请联系 [https://t.me/@talkingstick233_bot](https://t.me/talkingstick233_bot
)


