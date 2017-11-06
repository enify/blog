# 在OpenmeidaVauit上安装h5ai列表程序
> 导读：OpenMedaiVault([官网地址](http://www.openmediavault.org/)，以下以OMV简称)是一个开源的NAS系统，它基于Debian，并提供强大的硬盘管理、文件共享等功能。而h5ai([官网地址](https://larsjung.de/h5ai/))是一个基于php的目录列表程序，除了提供列表功能之外，还可以对文件进行在线预览、批量下载、在线播放等操作。

> 测试环境：php5.6.30 + nginx1.6.2


- [前言](#前言)
- [安装步骤](#安装步骤)
  - 1.[创建站点文件夹](#1-创建站点文件夹)
  - 2.[下载并解压h5ai](#2-下载并解压h5ai)
  - 3.[配置h5ai](#3-配置h5ai)
  - 4.[测试h5ai](#4-测试h5ai)
  - 5.[将h5ai整合到OMV中](#5-将h5ai整合到omv中)
  - 6.[启用h5ai功能](#6-启用h5ai功能)
  - 7.[定制h5ai](#7-定制h5ai)
- [结语](#结语)
- [参考配置文件](#参考配置文件)

## 前言
自7月份将家里的NAS改成OMV后，一直苦于上面没有合适的文件浏览工具。不如群晖方便。只得依靠smb来浏览。一日逛论坛时发现了h5ai这个东西，只带中文还不依赖于数据库，让我很是兴奋。因为NAS当前的系统装在一个4G的小U盘上，所剩空间并不多，所以这类小东西确实很合我胃口。不过由于以前没有部署过php应用，而且还要考虑与OMV的管理页面配合，所以折腾下来也花了挺长时间的。这里把安装过程记录下来：


## 安装步骤
### 1. 创建站点文件夹
在目录 `"/var/www/"` 下创建站点文件夹 `h5ai`：

![创建站点文件夹](https://github.com/enify/blog/blob/master/在OpenMedaiVault上安装h5ai/images/创建站点文件夹.png)
### 2. 下载并解压h5ai
注：你可以在[官网](https://larsjung.de/h5ai/)或[GitHub](https://github.com/lrsjng/h5ai)找到下载地址。

![下载并解压h5ai到站点文件夹下](https://github.com/enify/blog/blob/master/在OpenMedaiVault上安装h5ai/images/下载并解压h5ai到站点文件夹下.png)
### 3. 配置h5ai
- 说明：nginx的站点配置文件位于 `"/etc/nginx/sites-available/"` 文件夹下，通过把配置文件软链接到 `"/etc/nginx/sites-enabled/"` 文件夹中可以启用它。
- 如图，我们可以看到当前有一个名为 `"openmediavault-webui"` 的配置文件被启用了，这是OMV管理页面的站点配置文件：<br/>
![nginx站点配置文件](https://github.com/enify/blog/blob/master/在OpenMedaiVault上安装h5ai/images/nginx站点配置文件.png)
- 接下来我们创建h5ai的站点配置文件，并将以下内容写入其中：<br/>
![写入h5ai配置文件](https://github.com/enify/blog/blob/master/在OpenMedaiVault上安装h5ai/images/写入h5ai配置文件.png)
```
server {
	server_name h5ai;
	root /var/www/h5ai;  # 这里填h5ai的站点文件夹路径
	index  index.html  index.php  /_h5ai/public/index.php;
	
	location / {
		try_files $uri $uri/ =404;
	}
	
	location /_h5ai {
		location /_h5ai/private {
			return 404;
		}
	}
	
	location ~ .php/ {
		rewrite ^(.*.php)/ $1 last;
	}
	
	location ~ \.php$ {
		fastcgi_split_path_info ^(.+\.php)(.*)$;
		try_files $fastcgi_script_name =404;
		fastcgi_pass php;
		fastcgi_index index.php;
		fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
		include fastcgi_params;
	}
}
```
- 修改nginx的配置文件 `"/etc/nginx/nginx.conf"` ，在 `"http {......}"` 大括号中添加以下行：
![修改nginx配置文件](https://github.com/enify/blog/blob/master/在OpenMedaiVault上安装h5ai/images/修改nginx配置文件.png)
```
upstream php {
    server unix:/var/run/php5-fpm.sock;
}
```
### 4. 测试h5ai
- 为了测试h5ai是否可用，这里先把OMV管理页面的站点配置文件从 `"/etc/nginx/sites-enabled/"` 中移除，换成h5ai的：
![测试h5ai](https://github.com/enify/blog/blob/master/在OpenMedaiVault上安装h5ai/images/测试h5ai.png)
- 重启nginx服务器：<br/>
![重启nginx服务器](https://github.com/enify/blog/blob/master/在OpenMedaiVault上安装h5ai/images/重启nginx服务器.png)
- 访问 `"http://主机IP地址/_h5ai/public/index.php"` ，可以发现h5ai的测试页面已经可以正常访问了：
![h5ai测试页面](https://github.com/enify/blog/blob/master/在OpenMedaiVault上安装h5ai/images/h5ai测试页面.png)
- 不需要输入密码，点击页面上的 `login` 按钮，会跳转检测页面，显示出当前环境的一些信息：
![h5ai环境信息](https://github.com/enify/blog/blob/master/在OpenMedaiVault上安装h5ai/images/h5ai环境信息.png)
- 可以看到还有很多功能还没有启用，这个先不管。
- 如果单纯地是在服务器上安装h5ai的话，到这步就结束了。你可以将要管理的目录软链接到站点文件夹中( `"/var/www/h5ai"` ，第1步创建的)，便可在web端管理它：
![h5ai已经可用了](https://github.com/enify/blog/blob/master/在OpenMedaiVault上安装h5ai/images/h5ai已经可用了.png)
- 要启用显示为"no"功能，请点击：[启用h5ai功能](#6-启用h5ai功能)。如果你是想让h5ai和OMV整合的话，请继续往下看。
### 5. 将h5ai整合到OMV中
上一步中为了测试h5ai，我们没有启用OWV的管理页面配置文件。由于我想把管理页面和h5ai都部署在80端口上，所以得想办法将它俩的配置文件整合一下。
方案为：保持OMV后台页面为主页不变，将h5ai挂载到 `"主机IP/h5ai/"` 下。
- 将第一步创建的站点文件夹移至OMV的站点文件夹下：<br/>
![移动站点目录](https://github.com/enify/blog/blob/master/在OpenMedaiVault上安装h5ai/images/移动站点目录.png)
- 复制OMV的站点配置文件为 `"omv-h5ai"`：
![复制配置文件](https://github.com/enify/blog/blob/master/在OpenMedaiVault上安装h5ai/images/复制配置文件.png)
- 编辑该配置文件：<br/>
![修改配置文件](https://github.com/enify/blog/blob/master/在OpenMedaiVault上安装h5ai/images/修改配置文件.png)
- 启用该配置文件，并重启nginx服务：
![重启服务器](https://github.com/enify/blog/blob/master/在OpenMedaiVault上安装h5ai/images/重启服务器.png)
- 可以看到OMV后台页面和h5ai已经能够正常访问了：<br/>
![访问OMV](https://github.com/enify/blog/blob/master/在OpenMedaiVault上安装h5ai/images/访问OMV.png)
![访问h5ai](https://github.com/enify/blog/blob/master/在OpenMedaiVault上安装h5ai/images/访问h5ai.png)
### 6. 启用h5ai功能
- **Public Cache directory**

  将站点文件夹下的 `"_h5ai/public/cache"` 目录权限设为777即可：
  ```
  chmod -R 777 /var/www/openmediavault/h5ai/_h5ai/public/cache/
  ```
- **Private Cache directory**

  将站点文件夹下的 `"_h5ai/private/cache"` 目录权限设为777即可：
  ```
  chmod -R 777 /var/www/openmediavault/h5ai/_h5ai/private/cache/
  ```
- **Image thumbs**
  
  需要安装PHP的GD图形拓展库：
  ```
  # 检测GD库是否已安装：
  > php5 -m | grep -i gd
  # OMV基于debian，可通过以下命令直接安装：
  > sudo apt-get install php5-gd
  ```
- **Movie thumbs**
  
  需要安装 `avconv` 或者 `ffmpeg` 命令行工具：
  ```
  # 以下两项选择其中的一项安装就好

  # 安装 avconv （它是 "libav-tools" 的一部分）：
  > sudo apt-get install libav-tools

  # 安装 ffmepg：
  > sudo apt-get install ffmpeg

  ```
- **PDF thumbs**
  
  需要安装 `convert` 或者 `gm` 命令行工具：
  ```
  # 以下两项选择其中的一项安装就好

  # 安装convert （它是 "imagemagick" 的一部分）
  > sudo apt-get install imagemagick

  # 安装gm （它的全名为"GraphicsMagick"）
  > sudo apt-get install graphicsmagick
  ```
- **Shell zip**
  
  需要安装 `zip` 命令行工具：
  ```
  > sudo apt-get install zip
  ```
进入检测页面 (`"http://主机IP地址/h5ai/_h5ai/public/index.php"` )，可以看见所有功能已经启用了：<br/>
![所有功能已经启用了](https://github.com/enify/blog/blob/master/在OpenMedaiVault上安装h5ai/images/所有功能已经启用了.png)
### 7. 定制h5ai
通过配置站点文件夹下的 `"_h5ai/private/conf/options.json"` 文件，可对h5ai的界面或功能进行定制，包括文件搜索、多选、显示文件/文件夹详细信息、QR码等。下面取几个常见的例子来说明：
- **开启文件搜索功能**
  
  在配置文件中搜索 "search" 字段，将其改为 true ：
  ```
  "search": {
        "enabled": true,
        "advanced": true,
        "debounceTime": 300,
        "ignorecase": true
    }
  ```
- **开启多选**
  
  在配置文件中搜索 "select" 字段，将其改为 true，然后重启nginx服务器：
  ```
  "select": {
        "enabled": true,
        "clickndrag": true,
        "checkboxes": true
    }
  ``` 
- **开启多选后打包下载**

  在配置文件中搜索 "download" 字段，将其改为 true，然后重启nginx服务器：
  ```
  "download": {
        "enabled": true,
        "type": "php-tar",
        "packageName": null,
        "alwaysVisible": false
    }
  ```
- **开启自动刷新**

  在配置文件中搜索 "autorefresh" 字段，将其改为 true，然后重启nginx服务器：
  ```
  "autorefresh": {
        "enabled": true,
        "interval": 5000
    }
  ```
- **显示文件夹大小**

  在配置文件中搜索 "foldersize" 字段，将其改为 true：
  ```
  "foldersize": {
        "enabled": true,
        "type": "php"
    }
  ```
- **设置默认语言为中文**

  在配置文件中搜索 "l10n" 字段，将 "lang" 改为 "zh-cn"：  
  ```
  "l10n": {
        "enabled": true,
        "lang": "zh-cn",
        "useBrowserLang": true
    }
  ```
- **显示详细信息侧边栏（及QR码）**

  在配置文件中搜索 "info" 字段，将 "enabled" 和 "show" 改为 true：
  ```
  "info": {
        "enabled": true,
        "show": true,
        "qrcode": true,
        "qrFill": "#999",
        "qrBack": "#fff"
    }
  ```
## 结语
设置完后，h5ai已经基本可用了，我测试的时候使用搜索功能偶尔会卡住，不知道是php部分没有配置好，还是机器性能的原因(N270小主机)。不过能用就不折腾了。建议对nginx和php不熟的同学还是将h5ai部署在单独的端口，能够避免很多坑点。

## 参考配置文件
  - 站点配置文件：[h5ai](https://github.com/enify/blog/blob/master/在OpenMedaiVault上安装h5ai/resources/h5ai)
  - ngnix配置文件： [nginx.conf](https://github.com/enify/blog/blob/master/在OpenMedaiVault上安装h5ai/resources/nginx.conf)
  - 与OMV整合后的站点配置文件：[omv-h5ai](https://github.com/enify/blog/blob/master/在OpenMedaiVault上安装h5ai/resources/omv-h5ai)
  - h5ai定制：[options.json](https://github.com/enify/blog/blob/master/在OpenMedaiVault上安装h5ai/resources/options.json)
  
  

参考资料：
 [1].配置nginx服务器： [h5ai Issue #493 GitHub](https://github.com/lrsjng/h5ai/issues/493)