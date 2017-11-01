---
layout: post
title:  "django调试利器 -django-debug-toolbar"
date:   2017-08-23 15:04:23
categories: [jekyll]
tags: [Python]
---



django调试利器 django-debug-toolbar

1.安装

$ pip install django-debug-toolbar

2. 在django项目settings中进行配置

        INSTALLED_APPS = [
        # ...
        'django.contrib.staticfiles',
        # ...
        'debug_toolbar',
        ]
    
        STATIC_URL = '/static/'
        
        MIDDLEWARE = [
            # ...
            'debug_toolbar.middleware.DebugToolbarMiddleware',
            # ...
        ]
        
        INTERNAL_IPS = ['127.0.0.1']
        
        DEBUG_TOOLBAR_PANELS = [
            'debug_toolbar.panels.versions.VersionsPanel',
            'debug_toolbar.panels.timer.TimerPanel',
            'debug_toolbar.panels.settings.SettingsPanel',
            'debug_toolbar.panels.headers.HeadersPanel',
            'debug_toolbar.panels.request.RequestPanel',
            'debug_toolbar.panels.sql.SQLPanel',
            'debug_toolbar.panels.staticfiles.StaticFilesPanel',
            'debug_toolbar.panels.templates.TemplatesPanel',
            'debug_toolbar.panels.cache.CachePanel',
            'debug_toolbar.panels.signals.SignalsPanel',
            'debug_toolbar.panels.logging.LoggingPanel',
            'debug_toolbar.panels.redirects.RedirectsPanel',
        ]
3. 在项目/urls.py文件中配置

        from django.conf import settings
        from django.conf.urls import include, url
        
        if settings.DEBUG:
            import debug_toolbar
            urlpatterns = [
                url(r'^__debug__/', include(debug_toolbar.urls)),
            ] + urlpatterns
            
这个例子中使用__debug__作为前缀，

[jekyll]:      http://jekyllrb.com
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-help]: https://github.com/jekyll/jekyll-help