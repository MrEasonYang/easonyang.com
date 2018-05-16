---
title: temporary-solution-for-hexo-social-issue
date: 2017-06-19 18:51:02
updated: 2017-06-19 18:51:02
tags:
categories:
keywords:
---

Hexo NexT 主题默认使用 fontawesome 库中的图标进行侧边栏社交图标的显示，然而 fontawesome 目前对很多网站，如知乎、豆瓣的图标仍不支持。为了能让侧边栏显示出网站对应的图标而非默认的小地球，我们来对主题的代码做点小改动。

打开 `themes\next\layout\_macro\sidebar.swig` 文件，在约 83 行也就是：

```html
          {% if theme.social %}
            {% for name, link in theme.social %}
              <span class="links-of-author-item">
                <a href="{{ link }}" target="_blank" title="{{ name }}">
                  {% if theme.social_icons.enable %}
                    <i class="fa fa-fw fa-{{ theme.social_icons[name] | default('globe') | lower }}"></i>
                  {% endif %}
                  {{ name }}
                </a>
              </span>
            {% endfor %}
          {% endif %}
```

之后添加如下内容：

```html
          {% if theme.special_social %}
            {% for name, link in theme.special_social %}
              <span class="links-of-author-item">
                <a href="{{ link }}" target="_blank" title="{{ name }}">
                  <span class="fa fa-fw">{{ theme.special_social_icons[name] }}</span>
                  {{ name }}
                </a>
              </span>
            {% endfor %}
          {% endif %}
```

可以看到

本文由 [Eason Yang](https://eason-yang.com) 创作，采用*[署名 4.0 国际（CC BY 4.0）创作共享协议](http://creativecommons.org/licenses/by/4.0/deed.zh)*进行许可，[详细声明 ](https://eason-yang.com/about/)。