> Never write just `{% endblock %}`, 
include the whole **`{% endblock javascript %}`**

> **Templates called by other templates are prefixed with `‘_’`**. 
> This applies to templates called via **`{% include %}`** or 
> custom template tags. 
> It does not apply to templates inheritance controls 
> such as **`{% extends %}`** or **`{% block %}`**.

---

[invalid-template-variables](https://docs.djangoproject.com/en/2.0/ref/templates/api/#invalid-template-variables)

```python
# settings/local.py

TEMPLATES = [
	{
		'BACKEND': 'django.template.backends.django.DjangoTemplates',
		'APP_DIRS': True,
		'OPTIONS':
			'string_if_invalid': 'INVALID EXPRESSION: %s'
	},
]
```
