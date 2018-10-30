## Using Django-Style Template Filters in Jinja2 Templates

```python
# core/jinja2.py
from django.contrib.staticfiles.storage import staticfiles_storage
from django.template import defaultfilters
from django.urls import reverse
from jinja2 import Environment

def environment(**options):
	env = Environment(**options)
	env.globals.update({
		'static': staticfiles_storage.url,
		'url': reverse,
		'dj': defaultfilters
	})
	return env
```
```html
<table><tbody>
	{% for purchase in purchase_list %}
		<tr>
			<a href="{{ url('purchase:detail', pk=purchase.pk) }}">
				{{ purchase.title }}
			</a>
		</tr>
		<tr>{{ dj.date(purchase.created, 'SHORT_DATE_FORMAT') }}</tr>
		<tr>{{ dj.floatformat(purchase.amount, 2) }}</tr>
	{% endfor %}
</tbody></table>
```

---
## Django Filter View Mixin for Jinja2

```python
# core/mixins.py

from django.template import defaultfilters

class DjFilterMixin:
	dj = defaultfilters
```
---
**Avoid Using Context Processors With Jinja2**

>**The Django documentation recommends against using context processors with Jinja2**. See the warning box at docs.djangoproject.com/en/1.11/topics/templates/#django.
template.backends.jinja2.Jinja2. **What they recommend instead is passing a function to the template that can be called as needed**. This can be done on a per-view basis or by
injecting the callable function as described in this subsection.
