# How to write code snippet on th web in django

Writing code snippets is one of the major challenges faced by some people most especially those who are working on blog applications that require writing code content. Here, I will be giving a step by step guide to do that so just follow the process step by step.

## Installation of ckeditor'
The package we will be using is `django ckeditor`. To install it, run this command on the command prompt or terminal

`pip install django-ckeditor`

## Adding ckeditor to INSTALLED_APPS 
For every django project, any third party package should be added to `INSTALLED_APPS`. This one is not an exception.Go to settings.py in your project and add the following configuratons

```
INSTALLED_APPS = [
    #...
    "ckeditor",
    "ckeditor_uploader",  
    #...
]
```

```
# media path
CKEDITOR_IMAGE_BACKEND = "pillow"
CKEDITOR_UPLOAD_PATH = "uploads/"

# for responsive & code snippet
CKEDITOR_CONFIGS = {
    "default": {
        "toolbar": None,
        "width": "100%",
        "extraPlugins": ",".join(
            [
                "codesnippet",
            ]
        ),
    },
}
```

## Adding URL config for ckeditor
Go into your urls.py file in your django project and add the code snippet below

```
from django.conf import settings
from django.conf.urls.static import static

urlpatterns = [
    path("admin/", admin.site.urls),
    path("", include("blog_app.urls")),
    path("ckeditor/", include("ckeditor_uploader.urls")),
] + static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

## Adding ckeditor to models.py
Here, we are going to add the field to be used for writing our post either on the admin page or ln the web

```
from ckeditor_uploader.fields import RichTextUploadingField

class Blog(models.Model):
    # other fields
    content = RichTextUploadingField(null=True, blank=True)
```

## Making Migrations
After all the above configurations, do this on your command prompt or terminal

```
$ python manage.py makemigrations
$ python manage.py migrate
```

## Adding static files for ckeditor in HTML
This should be done in your base.html file or the file you will be writing the post but in that case, ignore `block content` and `endblock content`
```
{% load static %}
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>CKEditor</title>
</head>
<body>
    {% block forms %}
      
    {% endblock forms %}
    {% block content %}
      
    {% endblock content %}
    {% block ckeditor %}

    <link rel="stylesheet" href="{% static 'ckeditor/ckeditor/plugins/codesnippet/lib/highlight/styles/monokai.css' %}" />
    <script src="{% static 'ckeditor/ckeditor/plugins/codesnippet/lib/highlight/highlight.pack.js' %}"></script>

    <script>hljs.initHighlightingOnLoad();</script>

	{% endblock ckeditor %}
</body>
</html>
```

## Rendering the post on the web
To render our post on the web, we need to escape it since by default, it will be rendering the html tags. To do this, add this in your post.html file

`{{ post.content|safe }}`

After this, you can write your post from django's admin page or from the web. To add code snippet, click on the icon that looks like this `<>` and you are good to go.

