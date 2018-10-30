* `get_object_or_404()` instead of `get()`  # but only use it in **views**.

> **Be Careful With Queries That Might Throw Exceptions**
> in most  situations you need to use a `try-except` block.

---

**`ObjectDoesNotExist vs. DoesNotExist`**

> ObjectDoesNotExist can be applied to any model object, whereas DoesNotExist is for a specific model.

```python
from django.core.exceptions import ObjectDoesNotExist
from flavors.models import Flavor
from store.exceptions import OutOfStock

def list_flavor_line_item(sku):
	try:
		return Flavor.objects.get(sku=sku, quantity__gt=0)
	except Flavor.DoesNotExist:
		msg = 'We are out of {0}'.format(sku)
		raise OutOfStock(msg)

def list_any_line_item(model, sku):
	try:
		return model.objects.get(sku=sku, quantity__gt=0)
	except ObjectDoesNotExist:
		msg = 'We are out of {0}'.format(sku)
		raise OutOfStock(msg)
```

```python
# Don't do this!
from django.models import Q
from promos.models import Promo

def fun_function(name=None):
"""Find working ice cream promo"""
# Too much query chaining makes code go off the screen or page. Not good.
return Promo.objects.active().filter(Q(name__startswith=name)|Q(description__icontains=name)).exclude(status='melted')
```

* [expressions]( https://docs.djangoproject.com/en/1.11/ref/models/expressions/)
* [database-functions](https://docs.djangoproject.com/en/1.11/ref/models/database-functions/)
* [transactions](https://docs.djangoproject.com/en/2.0/topics/db/transactions/)
* [django-transactions-atomic-requests](https://stackoverflow.com/questions/31734306/django-transactions-atomic-requests)
* [transaction-management-with-django-1-6](https://realpython.com/transaction-management-with-django-1-6/)