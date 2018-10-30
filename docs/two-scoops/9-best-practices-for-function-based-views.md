# Decorators Are Sweet

```python
# sprinkles/decorators.py

from functools import wraps
from . import utils

def check_sprinkles(view_func):
	"""Check if a user can add sprinkles"""
	@wraps(view_func)
	def new_view_func(request, *args, **kwargs):
		# Act on the request object with utils.can_sprinkle()
		request = utils.can_sprinkle(request)
		
		# Call the view function
		response = view_func(request, *args, **kwargs)
		
		# Return the HttpResponse object
		return response
	return new_view_func
```
```python
# sprinkles/views.py

from django.shortcuts import get_object_or_404, render
from .decorators import check_sprinkles
from .models import Sprinkle

# Attach the decorator to the view
@check_sprinkles
def sprinkle_detail(request, pk):
	"""Standard detail view"""
	sprinkle = get_object_or_404(Sprinkle, pk=pk)
	return render(request, "sprinkles/sprinkle_detail.html", {"sprinkle": sprinkle})
```

> Astute readers may have noticed that our decorator examples used the **functools.wraps()**
decorator function from the Python standard library. **This is a convenience tool that copies
over metadata including critical data like docstrings to the newly decorated function**. It’s not necessary, but it makes project maintenance much easier.
