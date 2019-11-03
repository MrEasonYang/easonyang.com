---
title: 编写Chrome插件来监控URL的变化
date: 2017-10-24 22:45:22
updated: 2017-10-24 22:45:22
tags: [工具, Chrome]
categories: [编程]
keywords: [Chrome, 插件, plugin, url监控]
---

## 前言

最近写了个简单的 chrome 插件来简化工作流程，其中一个功能就是对当前页面的 URL 进行监控以根据 URL 中的参数发起网络请求获取数据，本文主要记录本功能相关逻辑的实现。

## 监控 URL

通过 `window.location.href` 就可以轻松获取到当前页面的 URL ，然而想要响应当前页面的 URL 变化则需要对相应事件进行处理。

### 弹出页获取页面 URL

首先在 manifest.json 中添加对 tab 的操作权限：

```json
"permissions": [ 
  "tabs"
]
```

<!--more-->随后在弹出页的 JS 代码中，使用浏览器 API 进行当前 tab 的查询，并在回调中进行相应的逻辑处理即可：

```javascript
chrome.tabs.query({active:true}, function (tab) {
    var url = tab[0].url;
  	...
});
```

### 页面加载时获取页面 URL

本例中，笔者的需求是不仅在用户点击插件图标来启动弹出页可进行操作，还可以在某类页面加载时，对当前正在加载页面的 URL 进行判断和处理。这样的需求就需要使用 Content Scripts 来实现了。

首先新建一个 JS 文件用于储存 Content Scripts ：

```javascript
var url = window.location.href;
if (/.*a.test.com\/job.html\?id=\d+.*/g.test(url)) {
    ...	
}
```

可以看到，在 Content Scripts 中可以直截了当地使用 `window.location.href` 来获取当前页面的 URL ，随后使用正则判断是否匹配，如果匹配再做进一步的处理。

最后在 manifest.json 中设置 Content Scripts 即可：

```json
    "content_scripts": [
        {
          "matches": ["*://a.test.com/*"],
          "js": ["jquery.min.js", "ajax.js", "content_script.js"],
          "run_at": "document_end"
        }
    ]
```

细心的读者会发现，matches 键所对的值与上文正则中的 URL 是高度一致的，实际上通过这个设置我们就可以实现仅在指定页面加载 content_script.js 的需求，避免冗余加载的问题。此外，如果你的脚本中引用了其他 JS 文件（如 jquery 、Vue 等）的话，还需要将相应文件加入到上述配置的 JS 键的值中方可生效。

本文由 [Eason Yang](https://eason-yang.com) 创作，采用*[署名 4.0 国际（CC BY 4.0）创作共享协议](http://creativecommons.org/licenses/by/4.0/deed.zh)*进行许可，[详细声明 ](https://eason-yang.com/about/)。