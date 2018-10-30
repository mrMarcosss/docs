# Django 2 by example

## When QuerySets are evaluated
You can concatenate as many filters as you like to a QuerySet, and you will not hit the database until the QuerySet is evaluated. QuerySets are only evaluated in the following cases:

-  The first time you iterate over them
    
-   When you slice them, for instance, `Post.objects.all()[:3]`
    
-   When you pickle or cache them
    
-   When you call `repr()` or `len()` on them
    
-   When you explicitly call `list()` on them
    
-   When you test them in a statement, such as `bool()`, `or` , `and`, or `if`

---------
[All built-in template tags and filters](https://docs.djangoproject.com/en/2.0/ref/templates/builtins/)

```python
@admin.register(Comment)  # register Comment model in admin.py
class CommentAdmin(admin.ModelAdmin):
	list_display = ('name', 'email', 'post', 'created')
```
---------
## Read this Django docs
* [settings](https://docs.djangoproject.com/en/2.0/ref/settings/)
* [urls](https://docs.djangoproject.com/en/2.0/topics/http/urls/)
* [django postgres](https://docs.djangoproject.com/en/2.0/ref/contrib/postgres/)
* [authentication-views](https://docs.djangoproject.com/en/2.0/topics/auth/default/#all-authentication-views)
* [messages](https://docs.djangoproject.com/en/2.0/ref/contrib/messages/)
* [other-authentication-sources](https://docs.djangoproject.com/en/2.0/topics/auth/customizing/#other-authentication-sources)
* [contenttypes](https://docs.djangoproject.com/en/2.0/ref/contrib/contenttypes/)
* [applications](https://docs.djangoproject.com/en/2.0/ref/applications/)
* [outputting-pdf](https://docs.djangoproject.com/en/2.0/howto/outputting-pdf/)
* [fixture-loading](https://docs.djangoproject.com/en/2.0/topics/testing/tools/#fixture-loading)
* [custom-model-fields](https://docs.djangoproject.com/en/2.0/howto/custom-model-fields/)
---
## Must read
* [Денормализация wiki](https://ru.wikipedia.org/wiki/%D0%94%D0%B5%D0%BD%D0%BE%D1%80%D0%BC%D0%B0%D0%BB%D0%B8%D0%B7%D0%B0%D1%86%D0%B8%D1%8F)
* [Денормализация БД. Зачем? Когда? Как?](https://habr.com/post/64524/)
* [Зачем нужна денормализация баз данных, и когда ее использовать](https://habr.com/company/latera/blog/281262/)
* [wsgi](https://wsgi.readthedocs.io/en/latest/)
---

```python
from django.contrib.auth import views as auth_views

path('', include('django.contrib.auth.urls')),
# it's the same as
path('login/', auth_views.LoginView.as_view(), name='login'),  
path('logout/', auth_views.LogoutView.as_view(), name='logout'),  
path('password_change/', auth_views.PasswordChangeView.as_view(), name='password_change'),  
path('password_change/done/', auth_views.PasswordChangeDoneView.as_view(), name='password_change_done'),  
path('password_reset/', auth_views.PasswordResetView.as_view(), name='password_reset'),  
path('password_reset/done/', auth_views.PasswordResetDoneView.as_view(), name='password_reset_done'),  
path('reset/<uidb64>/<token>/', auth_views.PasswordResetConfirmView.as_view(), name='password_reset_confirm'),  
path('reset/done/', auth_views.PasswordResetCompleteView.as_view(), name='password_reset_complete'),

# in order to use custom tempates app should be in INSTALLED_APPS before django.contrib.admin
# all templates should be in project/app/registration/
```
-----
 **Note the difference between authenticate and login:** 
 * `authenticate()` checks user credentials and returns a User object if they are right; 
 * `login()` sets the user in the current session.
----

**Custom backend via email**
[other-authentication-sources](https://docs.djangoproject.com/en/2.0/topics/auth/customizing/#other-authentication-sources)
```python
# authentication.py

class EmailAuthBackend(object):  
    """
    Authenticate using an e-mail address.
    """
    def authenticate(self, request, username=None, password=None):  
        try:  
            user = User.objects.get(email=username)  
            if user.check_password(password):  
                return user  
            return None  
		 except User.DoesNotExist:  
            return None  
  
	 def get_user(self, user_id):  
        try:  
            return User.objects.get(pk=user_id)  
        except User.DoesNotExist:  
            return None

# add to the AUTHENTICATION_BACKENDS in settings.py
'app.authentication.EmailAuthBackend'
```
----
**commands**
`python manage.py check --deploy`

## New tech
* [redis](https://redis.io/documentation)
* [celery](http://docs.celeryproject.org/en/latest/index.html)
* [flower](https://flower.readthedocs.io/en/latest/)
* [memcached](https://memcached.org/)
	* run `memcached -l 127.0.0.1:11211`
* [cron](http://www.unixgeeks.org/security/newbie/unix/cron-1.html)

## New libs
* [django-modeltranslation](http://django-modeltranslation.readthedocs.io/en/latest/registration.html)
* [django-parler](https://django-parler.readthedocs.io/en/latest/)
----
**Edit item on list page**
```python
@admin.register(Product)  
class ProductAdmin(admin.ModelAdmin):  
    list_display = ['name', 'price', 'available', 'created']  
    list_editable = ['price', 'available'] # edit item from list page
```
----
## Current language
How Django determines the current language?

1. If you are using `i18_patterns`, that is, you use translated URL patterns, it looks for a language prefix in the requested URL to determine the current language.

2. If no language prefix is found, it looks for an existing `LANGUAGE_SESSION_KEY` in the current user's session.

3. If the language is not set in the session, it looks for an existing cookie with the current language. A custom name for this cookie can be provided in the `LANGUAGE_COOKIE_NAME` setting. By default, the name for this cookie is `django_language`.

4. If no cookie is found, it looks for the `Accept-Language` HTTP header of the request.

5. If the Accept-Language header does not specify a language, Django uses the language defined in the `LANGUAGE_CODE `setting.

> By default, Django will use the language defined in the **LANGUAGE_CODE** setting unless you are using **LocaleMiddleware**. The process described here **only** applies when using this middleware.

-----

Schedule our management command so that the server runs it every day at `8 a.m.` If you are using a `UNIX-based` system such as `Linux` or `macOS X`, open the shell and run `crontab -e` to edit your crontab. Add the following line to it:
```shell
0 8 * * * python /path/to/educa/manage.py enroll_reminder --days=20 --settings=educa.settings.pro
```