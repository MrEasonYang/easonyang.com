---
title: 用Python为 WoX 写插件
alias: 2016/06/19/use-python-to-build-a-wox-plugin
dir: /
tags: [Python, 工具, Windows]
categories: [教程]
date: 2016-06-19 22:18:50
---


> **WoX** is a launcher for Windows that simply works. It's an alternative to [Alfred](https://www.alfredapp.com/) and [Launchy](http://www.launchy.net/). You can call it Windows omni-eXecutor if you want a long name.

这是 WoX 官网的简介，作为一个 Windows 下开源的快速启动工具，虽然没有 Launchy 成熟稳定，但是可以方便地写插件这点还是挺值得一试的。到这里获取 WoX ：[getWox](http://www.getwox.com/) [Github](https://github.com/Wox-launcher/Wox)

WoX 的插件开发支持 C# 和  Python 3 ，本文以 Python 为基础开发几款小插件。<!--more-->

## IPIP

WoX 有一个提供 ip138 的 ip 地址查询的插件，然而在我这似乎并不奏效，索性就借助 [ipip.net](http://ipip.net) 的免费 API 来重新开发一个用来快速查询IP信息的小插件。（P.S. ipip 的免费API仅支持每天1000次查询，不过对个人用户来说绝对够用了，此外这个免费 API 貌似不支持 IPV6 和域名查询，所以本插件对这两者也就不做考虑）

首先要定义一个 plugin.json 用来储存插件的相关信息和配置：

```json
{
    "ID":"92p6d94f-4e10-4dw4-b650-8c8aba1d7ace",
    "ActionKeyword":"ipi",
    "Name":"IPIP",
    "Description":"Get IP info from ipip.net",
    "Author":"Eason Yang",
    "Version":"0.0.1",
    "Language":"python",
    "Website":"https://easonyang.com",
    "ExecuteFileName":"ipip.py",
    "IcoPath":"Images\\icon.png"
}
```

这些参数中需要特别说明的是：

1. ID 应该是一个与其他插件不相同的UUID
2. ActionKeyword 定义了执行此插件的关键字，同样也不能与其他插件重叠

随后 WoX 的插件需要编写一个继承 WoX 父类的子类，并且该子类必须重载参数为用户输入内容的 query 方法，插件的逻辑都在 query 方法中完成。

```python
class IpInfo(Wox):    
    def query(self,query):
        """
        sub class need to override this method
        """
        return []
```

query 方法的返回值是一个由固定格式字典组成的列表，这个列表的元素也会作为 UI 中 item 显示，字典格式如下：

```python
        result = [{
            "Title": "IPIP",
            "SubTitle": data,
            "IcoPath": "app.ico",
            "JsonRPCAction": {
                "method": "detail",
                "parameters": ["http://www.ipip.net/ip.html"],
                "dontHideAfterAction": False
            }
        }]
```

数据放在副标题中显示即可，JsonRPCAction返回的这个字典用于 WoX 的主程序处理用户点击item后的动作，method 指要调用的方法，parameters 是要向方法中传递的值，dontHideAfterAction 指点击后是否隐藏 UI，根据官方文档，method 除了可以填咱们所创建的这个子类的方法，还可以调用系统自身的一些方法，详情参见文档。这里我们只简单地调用自定义的 detail 方法来在浏览器中打开网页，参数是固定的 ipip 网址。

[ipip.net](http://ipip.net) 的查询结果是一个 JSON 字符串，这样我们调用 requests 模块就可以很轻松地处理结果了。

```python
        data = "Result:"
        url = "http://freeapi.ipip.net/" + query
        result = requests.get(url)
        for item in result.json():
            data += " " + item
```

但是这个插件在实际使用中会在日志中报错，原因就在于 WoX 在匹配到用户输入的关键字后，就会调用对应的插件处理此后用户的每个输入，所以这些不完整 IP 格式的就造成了插件报错，所以加入正则改进一下（P.S. 鉴于目的并不真的要判断 IP 地址格式是否正确，所以就不写那个一长串的匹配 IP 的正则表达式了）：

```python
        pattern = re.compile('(\d{1,3}\.){3}\d', re.S)
        if not re.search(pattern, query):
            return ""
```

至此一个简单的插件就完成了：

```python
class IpInfo(Wox):
    def query(self, query):
        if not query:
            return ""
        pattern = re.compile('(\d{1,3}\.){3}\d', re.S)
        if not re.search(pattern, query):
            return ""
        data = "Result:"
        url = "http://freeapi.ipip.net/" + query
        result = requests.get(url)
        for item in result.json():
            data += " " + item
        result = [{
            "Title": "IPIP",
            "SubTitle": data,
            "IcoPath": "app.ico",
            "JsonRPCAction": {
                "method": "detail",
                "parameters": ["http://www.ipip.net/ip.html"],
                "dontHideAfterAction": False
            }
        }]
        return result
    def detail(self, url):
        webbrowser.open(url)

if __name__ == "__main__":
    IpInfo()
```

注：在正式使用前，我们还可以通过运行脚本时传参的方式来模拟运行，也就是说我们可以借助这个方法来做 Debug。参数的格式是 `"{\"method\":\"\",\"parameters\":[\"\"]}"`

## PING

接下来写一个利用 Python 调用系统 ping 命令并输出结果的小插件，同样只考虑 IPV4。

定义一个 ping 方法，在该方法中使用 subprocess 模块来执行命令后，将结果转为字符串并存入列表中返回，为了美观，把结果中的无用行忽略掉：

```python
    def ping(self, ip):
        ping = subprocess.Popen(["ping", ip], shell=True, stdout=subprocess.PIPE, stderr=subprocess.STDOUT)
        result = []
        line_to_print = [1, 2, 3, 4, 5, 8, 10]
        for line_number, line in enumerate(ping.stdout.readlines()):
            if line_number in line_to_print:
                result.append(line.strip().decode('utf-8'))
        return result
```

随后仿照 IPIP 插件编写 query 方法，当 ip 符合格式时，调用 ping 方法，最后把结果处理之后返回：

```python
    def query(self, query):
        ip = self.regex(query, '\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}')
        if not ip:
            return ""
        ping = self.ping(ip)
        result = []
        if len(ping) != 0:
            for item in ping:
                result.append({
                    "Title": "Ping Result",
                    "SubTitle": item,
                    "IcoPath": "Images/app.png",
                    "JsonRPCAction": {
                        "method": "detail",
                        "parameters":["http://ping.chinaz.com"],
                        "dontHideAfterAction": False
                    }
                })
        return result
```

这里为了方便把上文的正则处理抽象为一个独立的方法：

```python
    def regex(self, query, condition):
        pattern = re.compile(condition)
        result = re.search(pattern, query)
        if result:
            return result.group()
        else:
            return ""
```

又一个小插件完成了：

```python
class Ping(Wox):
    def query(self, query):
        ip = self.regex(query, '\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}')
        if not ip:
            return ""
        ping = self.ping(ip)
        result = []
        if len(ping) != 0:
            for item in ping:
                result.append({
                    "Title": "Ping Result",
                    "SubTitle": item,
                    "IcoPath": "Images/app.png",
                    "JsonRPCAction": {
                        "method": "detail",
                        "parameters":["http://ping.chinaz.com"],
                        "dontHideAfterAction": False
                    }
                })
        return result

    def regex(self, query, condition):
        pattern = re.compile(condition, re.S)
        result = re.findall(pattern, query)
        if len(result) == 1:
            return result[0]
        else:
            return []

    def detail(self, url):
        webbrowser.open(url)

    def ping(self, ip):
        ping = subprocess.Popen(["ping", ip], shell=True, stdout=subprocess.PIPE, stderr=subprocess.STDOUT)
        result = []
        line_to_print = [1, 2, 3, 4, 5, 8, 10]
        for line_number, line in enumerate(ping.stdout.readlines()):
            if line_number in line_to_print:
                result.append(line.strip().decode('utf-8'))
        return result

if __name__ == "__main__":
    Ping()
```



## IPIP&PING

最后为了便捷把这两个插件整合在一起：

```python
# -*- coding: utf-8 -*-
# Author: Eason Yang
# Date: 6/18/2016
import webbrowser

import requests
import re
import subprocess
from wox import Wox, WoxAPI


class IpInfo(Wox):
    def query(self, query):
        if not query:
            return ""
        ip = self.regex(query, '(\d{1,3}\.){3}\d')
        if not ip:
            return ""
        data = "Result:"
        url = "http://freeapi.ipip.net/" + ip
        result = requests.get(url)
        for item in result.json():
            data += " " + item
        result = [{
            "Title": "IPIP",
            "SubTitle": data,
            "IcoPath": "Image/app.png",
            "JsonRPCAction": {"method": "detail", "parameters": ["http://www.ipip.net/ip.html"],
                              "dontHideAfterAction": False}
        }]
        if self.regex(query, "ping [\d\.]+"):
            ping = self.ping(ip)
            if len(ping) != 0:
                for item in ping:
                    result.append({
                        "Title": "Ping Result",
                        "SubTitle": item,
                        "IcoPath": "Images/app.png",
                        "JsonRPCAction": {"method": "detail", "parameters": ["http://ping.chinaz.com"],
                                          "dontHideAfterAction": False}
                    })
        return result

    def regex(self, query, condition):
        pattern = re.compile(condition)
        result = re.search(pattern, query)
        if result:
            return result.group()
        else:
            return ""

    def detail(self, url):
        webbrowser.open(url)

    def ping(self, ip):
        ping = subprocess.Popen(["ping", ip], shell=True, stdout=subprocess.PIPE, stderr=subprocess.STDOUT)
        result = []
        line_to_print = [1, 2, 3, 4, 5, 8, 10]
        for line_number, line in enumerate(ping.stdout.readlines()):
            if line_number in line_to_print:
                result.append(line.strip().decode('utf-8'))
        return result


if __name__ == "__main__":
    IpInfo()
```

这样就能输入 ipip+ip 来查询 IP 信息，也可以通过 ipip+ping+ip 来同时显示 IP 信息和 ping 的结果。

## 踩坑记录

- ### 缺少 requests 模块

```
2016-06-18 17:49:24.7948|ERROR|Wox.Core.Plugin.JsonRPCPlugin.Execute|Traceback (most recent call last):
  File "C:\Users\Eason Yang\AppData\Local\Wox\app-1.3.67\Plugins\Wox.Plugin.Ipip\ipip.py", line 8, in <module>
    import requests
ImportError: No module named 'requests'
```

明显是缺少 requests 模块，但[官方文档](http://doc.getwox.com/zh/plugin/python_plugin.html)中写的是

>Wox支持使用Python进行插件的开发。Wox自带了一个打包的Python及其标准库，所以使用Python 插件的用户不必自己再安装Python环境。同时，Wox还打包了requests和beautifulsoup4两个库， 方便用户进行网络访问与解析。

然后试了下官方的其他Python插件，都提示缺少request模块。后来几经周折，才发现原来还要在 WoX 的 UI 里设置 Python 路径才能正常使用。

- ### 缺少  wox 模块

```
2016-06-18 19:01:47.7101|ERROR|Wox.Core.Plugin.JsonRPCPlugin.Execute|Traceback (most recent call last):
  File "C:\Users\Eason Yang\AppData\Roaming\Wox\Plugins\Hacker News-36f26938-4a86-4a14-b105-68124c769c99\main.py", line 6, in <module>
    from wox import Wox,WoxAPI
ImportError: No module named 'wox'
```

解决上一个问题的时候发现有些用 Python 写的插件运行时会报上面这个错，只要在能正常运行的插件（比如默认安装的 HelloWorldPython ）目录中找到 wox.py 复制到报错插件的目录即可。