## Custom field validators

```python
# core/validators.py

from django.core.exceptions import ValidationError

def validate_tasty(value):
	"""
	Raise a ValidationError if the value doesn't start with the word 'Tasty'.
	"""
	if not value.startswith('Tasty'):
		msg = 'Must start with Tasty'
		raise ValidationError(msg)
```

## Two forms that work with one model.

```python
# stores/forms.py

from django import forms
from .models import IceCreamStore

class IceCreamStoreCreateForm(forms.ModelForm):
	
	class Meta:
		model = IceCreamStore
		fields = ['title', 'block_address', ]


class IceCreamStoreUpdateForm(IceCreamStoreCreateForm):
	
	def __init__(self, *args, **kwargs):
		super(IceCreamStoreUpdateForm, self).__init__(*args, **kwargs)
		self.fields['phone'].required = True
		self.fields['description'].required = True
	
	class Meta(IceCreamStoreCreateForm.Meta):
	# show all the fields!
	fields = ['title', 'block_address', 'phone', 'description', ]
```
---
how to reuse a search form in two views that correspond to two
different models.

> Assume that both models have a field called **title** (this pattern also demonstrates why naming standards in projects is a good thing). This example will demonstrate how a single CBV can be used to provide simple search functionality on both the **Flavor** and **IceCreamStore** models.

```python
# core/views.py

class TitleSearchMixin:
	
	def get_queryset(self):
		# Fetch the queryset from the parent's get_queryset
		queryset = super(TitleSearchMixin, self).get_queryset()
		
		# Get the q GET parameter
		q = self.request.GET.get('q')
		if q:
			# return a filtered queryset
			return queryset.filter(title__icontains=q)
		# No q is specified so we return queryset
		return queryset
```
```python
# add to flavors/views.py

from django.views.generic import ListView
from .models import Flavor
from core.views import TitleSearchMixin

class FlavorListView(TitleSearchMixin, ListView):
	model = Flavor
```
```python
# add to stores/views.py

from django.views.generic import ListView
from .models import Store
from core.views import TitleSearchMixin

class IceCreamStoreListView(TitleSearchMixin, ListView):
	model = Store
```

```html
<!-- forms for both views -->

<form action="" method="GET">
	<input type="text" name="q" />
	<button type="submit">search</button>
</form>
```
