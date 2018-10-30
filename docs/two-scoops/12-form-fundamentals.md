[csrf in ajax](https://docs.djangoproject.com/en/1.11/ref/csrf/#ajax)

## Adding custom attributes

```python
from django import forms
from .models import Taster

class TasterForm(forms.ModelForm):
	
	class Meta:
		model = Taster
	
	def __init__(self, *args, **kwargs):
		# set the user as an attribute of the form
		self.user = kwargs.pop('user')
		super(TasterForm, self).__init__(*args, **kwargs)
```
```python
from django.contrib.auth.mixins import LoginRequiredMixin
from django.views.generic import UpdateView
from .forms import TasterForm
from .models import Taster

class TasterUpdateView(LoginRequiredMixin, UpdateView):
	model = Taster
	form_class = TasterForm
	success_url = '/someplace/'
	
	def get_form_kwargs(self):
		"""This method is what injects forms with keyword arguments."""
		# grab the current set of form #kwargs
		kwargs = super(TasterUpdateView, self).get_form_kwargs()
		# Update the kwargs with the user_id
		kwargs['user'] = self.request.user
		return kwargs
```
---
Perhaps you need to catch the details of failed submission attempts for a form, saving both the user-supplied form data as well as the intended model instance changes.

```python
# flavors/views.py
import json
from django.contrib import messages
from django.core import serializers
from core.models import ModelFormFailureHistory

class FlavorActionMixin:
	
	@property
	def success_msg(self):
		return NotImplemented

	def form_valid(self, form):
		messages.info(self.request, self.success_msg)
		return super(FlavorActionMixin, self).form_valid(form)

	def form_invalid(self, form):
		"""Save invalid form and model data for later reference."""
		form_data = json.dumps(form.cleaned_data)
		# Serialize the form.instance
		model_data = serializers.serialize('json', [form.instance])
		# Strip away leading and ending bracket leaving only a dict
		model_data = model_data[1:-1]
		ModelFormFailureHistory.objects.create(
			form_data=form_data,
			model_data=model_data
		)
		return super(FlavorActionMixin,self).form_invalid(form)
```


## Useful Form Methods

* [as_data](https://docs.djangoproject.com/en/1.11/ref/forms/api/#django.forms.Form.errors.as_data)
* [as_json](https://docs.djangoproject.com/en/1.11/ref/forms/api/#django.forms.Form.errors.as_json)
* [non_field_errors](https://docs.djangoproject.com/en/1.11/ref/forms/api/#django.forms.Form.non_field_errors)

[series on forms expands](https://www.pydanny.com/tag/forms.html)
