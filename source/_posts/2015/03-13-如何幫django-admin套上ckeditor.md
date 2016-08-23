title: 如何幫Django Admin套上CKEditor
date: 2015-03-13 08:53:54
categories:
  - Development
  - Django
---

因為工讀內容需要透過Django內建的Admin系統發佈公告訊息，而預設的Admin樣式又十分陽春，肯定不符合學校這種公家機關的需求，爬了一些Google文章，發現搭配強大的WYSIWYG編輯器CKEditor可以讓整件事情簡化到只有三個步驟...

<!--more-->

## Step 1 下載[CKEditor](http://ckeditor.com/)

以我為例，下載後解壓縮到`Project目錄/App目錄/static/plugins/ckeditor`

## Step 2 編輯admin.py

舉我的公告系統作為例子，主要注意10~16行即可，第10行讓我們可以改寫預設的Textarea樣式，13行注入ckeditor主程式，14~16行請見下一步說明

```py
from django.contrib import admin
from django.db import models
from django import forms

from noc.models import Announcement

class AnnouncementAdmin(admin.ModelAdmin):
    list_display = ('title', 'post_at', 'author', 'phone')
    search_fields = ('title', 'content', 'author')
    formfield_overrides = { models.TextField: {'widget': forms.Textarea(attrs={'class':'ckeditor'})}, }

    class Media:
        js = ('/static/plugins/ckeditor/ckeditor.js',)
        css = {
            'all': ('/static/plugins/ckeditor/djangoAdminCustomizer.css',)
        }

admin.site.register(Announcement, AnnouncementAdmin)
```

## Step 3 修正CSS

如果僅僅單純注入js，Admin系統會跑版如下圖

![](跑版.png)

必須另外建個css檔案注入Admin來修正

```css
div>.cke_chrome {
    width: 100%;
    padding: 0!important;
    clear: both;
}
```

## 完成

最終效果如下

![](Django-Admin-with-CKEditor.png)

## 參考資料

- [ADDING CKEDITOR TO YOUR DJANGO ADMIN](http://glynjackson.org/weblog/entry/adding-ckeditor-to-django-admin.html)
- [How to fix CKEditor 4 default theme in Django admin](http://mornie.org/blog/2012/12/22/how-fix-ckeditor-4-default-theme-django-admin/)
- [ModelAdmin asset definitions](https://docs.djangoproject.com/en/1.7/ref/contrib/admin/#modeladmin-asset-definitions)