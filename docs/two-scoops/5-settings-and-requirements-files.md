[SECRET_KEY](https://docs.djangoproject.com/en/1.11/topics/signing/)
```
settings/
├── __init__.py
├── base.py
├── local.py
├── staging.py
├── test.py
├── production.py
```
```
python manage.py shell --settings=twoscoops.settings.local
```
```python
# local.py settings

from .base import *

DEBUG = True

EMAIL_BACKEND = 'django.core.mail.backends.console.EmailBackend'

DATABASES = {
	'default': {
		'ENGINE': 'django.db.backends.postgresql_psycopg2',
		'NAME': 'twoscoops',
		'HOST': 'localhost',
	}
}

INSTALLED_APPS += ['debug_toolbar', ]
```

## Use environment variables
```bash
export SOME_SECRET_KEY=1c3-cr3am-15-yummy
export AUDREY_FREEZER_KEY=y34h-r1ght-d0nt-t0uch-my-1c3-cr34m

unset SOME_SECRET_KEY
unset AUDREY_FREEZER_KEY
```
```python
# Top of settings/production.py
import os
SOME_SECRET_KEY = os.environ['SOME_SECRET_KEY']
```
```python
# settings/base.py

import os
# Normally you should not import ANYTHING from Django directly
# into your settings, but ImproperlyConfigured is an exception.
from django.core.exceptions import ImproperlyConfigured

def get_env_variable(var_name):
	"""Get the environment variable or return exception."""
	try:
		return os.environ[var_name]
	except KeyError:
		error_msg = 'Set the {} environment variable'.format(var_name)
		raise ImproperlyConfigured(error_msg)

# settings/prod.py
SOME_SECRET_KEY = get_env_variable('SOME_SECRET_KEY')
```
-----
## Requirements
```
requirements/
├── base.txt
├── local.txt
├── staging.txt
├── production.txt
```

```
# requirements/local.txt

-r base.txt # includes the base.txt requirements file

coverage==4.2
django-debug-toolbar==1.5
```
```
# requirements/production.txt

-r base.txt
```
----
## Path
`Using **Pathlib** to discover project root (_since Python 3.4_)`

```python
# At the top of settings/base.py

from pathlib import Path

BASE_DIR = Path(__file__).resolve().parent.parent
MEDIA_ROOT = BASE_DIR / 'media'
STATIC_ROOT = BASE_DIR / 'static_root'
STATICFILES_DIRS = [BASE_DIR / 'static']
TEMPLATES = [
	{
		'BACKEND': 'django.template.backends.django.DjangoTemplates',
		'DIRS': [BASE_DIR / 'templates']
	},
]
```

`Using **os.path** to discover project root`

```python
# At the top of settings/base.py

from os.path import abspath, dirname, join

def root(*dirs):
	base_dir = join(dirname(__file__), '..', '..')
	return abspath(join(base_dir, *dirs))

BASE_DIR = root()
MEDIA_ROOT = root('media')
STATIC_ROOT = root('static_root')
STATICFILES_DIRS = [root('static')]
TEMPLATES = [
	{
		'BACKEND': 'django.template.backends.django.DjangoTemplates',
		'DIRS': [root('templates')],
	},
]
```