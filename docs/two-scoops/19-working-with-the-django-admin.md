# Django’s Admin Documentation Generator

* `pip install docutils`
* Add `django.contrib.admindocs` to your `INSTALLED_APPS`
* Add `(r'^admin/doc/', include('django.contrib.admindocs.urls'))` to your root URLConf. Make sure it’s included before the `r'^admin/'` entry, so that requests to `/admin/doc/` don’t get handled by the latter entry.
* *Optional*: Using the `admindocs` bookmarklets requires the `XViewMiddleware` to be installed.

Django skins
[grappelliproject](http://grappelliproject.com/)
[djangosuit](http://djangosuit.com/)  # non free for commercial

**security**
[fake admin login page](https://github.com/dmpayton/django-admin-honeypot)
